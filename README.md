# 🧵 ThreadLocal in Java – Complete Guide

## 📘 What is ThreadLocal?

`ThreadLocal` is a Java utility that allows you to store data **per-thread**. Each thread accessing the variable via `ThreadLocal` gets an isolated copy. It's often used to **avoid synchronization** or to **maintain state across method calls** without passing it explicitly.

> Internally, it's like a Map: `Map<Thread, Object>`

---

## ✅ When to Use

* Non-thread-safe objects like `SimpleDateFormat`
* Per-thread data like authentication context or session
* Thread-isolated logging contexts (e.g., request IDs)
* Reusing objects across methods without synchronization

---

## 🧠 Core Methods

| Method          | Description                         |
| --------------- | ----------------------------------- |
| `get()`         | Returns the current thread's value  |
| `set(value)`    | Sets the current thread's value     |
| `remove()`      | Clears the value for current thread |
| `withInitial()` | Provides default value per thread   |

---

## 🧪 Basic Example

```java
public class ThreadLocalExample {
    private static final ThreadLocal<Integer> threadId =
        ThreadLocal.withInitial(() -> (int) (Math.random() * 10000));

    public static void main(String[] args) {
        Runnable task = () ->
            System.out.println(Thread.currentThread().getName() + " ID: " + threadId.get());

        new Thread(task).start();
        new Thread(task).start();
    }
}
```

### Output (example):

```
Thread: Thread-0 ID: 232
Thread: Thread-1 ID: 674
```

Each thread gets its own value.

---

## ⚠️ Important Notes

### 1. Memory Leaks in Thread Pools

If the thread is reused (as in `ExecutorService`), the value persists between tasks.

✅ **Always call `remove()` manually** in such environments.

```java
try {
    threadLocal.set(data);
    // Do work
} finally {
    threadLocal.remove();
}
```

### 2. Avoid Overuse

Use `ThreadLocal` only when:

* You need thread-specific storage.
* Passing state via parameters is difficult or messy.

Don't use it to avoid proper thread-safe design.

---

## 🎯 Real Use Cases

### 1. Date Formatter (Non-Thread-Safe Object)

```java
private static final ThreadLocal<SimpleDateFormat> formatter =
    ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

public static String formatDate(Date date) {
    return formatter.get().format(date);
}
```

### 2. User Context

```java
public class UserContext {
    private static final ThreadLocal<String> currentUser = new ThreadLocal<>();

    public static void set(String user) {
        currentUser.set(user);
    }

    public static String get() {
        return currentUser.get();
    }

    public static void clear() {
        currentUser.remove();
    }
}
```

---

## 🌐 Usage in Frameworks

### Spring Security

Spring Security uses `ThreadLocal` internally in `SecurityContextHolder` to store user authentication per thread.

```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
```

Under the hood:

```java
private static final ThreadLocal<SecurityContext> contextHolder = new ThreadLocal<>();
```

### Spring Transaction Management

Spring uses `ThreadLocal` to manage transaction state and resources like database connections:

```java
private static final ThreadLocal<Map<Object, Object>> resources =
    new NamedThreadLocal<>("Transactional resources");
```

### Logging (MDC in SLF4J / Logback)

```java
MDC.put("userId", "12345");
log.info("Request started");  // MDC value added to log
MDC.clear();
```

MDC uses `ThreadLocal` to maintain log context per thread.

---

## 🧼 Best Practices

| Best Practice             | Reason                                   |
| ------------------------- | ---------------------------------------- |
| Always call `remove()`    | Prevent memory leaks in thread pools     |
| Use `withInitial()`       | Avoid null and redundant init logic      |
| Use only for thread-local | Don't use as global state hack           |
| Avoid in reactive systems | ThreadLocal doesn't work with async flow |

---

## 🔚 Summary

`ThreadLocal` is a powerful utility for managing per-thread state, but comes with responsibility. Use it wisely to avoid hidden bugs or memory issues, especially in environments like servlet containers or thread pools.

---

### 📌 References

* [Java Docs - ThreadLocal](https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html)
* [Spring SecurityContextHolder](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/core/context/SecurityContextHolder.html)
* [MDC Logging Context](https://www.baeldung.com/mdc-in-log4j-2)
