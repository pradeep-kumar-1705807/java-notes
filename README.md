
# ThreadLocal in Java:

Each thread has its own instance of the ThreadLocal class.
Allows threads to store data specific to themselves, preventing interference from other threads.
Example: The main thread can set its name with a ThreadLocal variable, and a new thread can set its own name using the same ThreadLocal object.

Importance of Cleanup:

Essential to clean up ThreadLocal variables when threads are reused (especially in thread pools).
Old values persist unless explicitly removed; use the remove() method after a task is completed to prevent unexpected behavior.

# Comparison of Virtual Threads and Normal Threads:

Normal threads are managed by the JVM and correspond to OS threads (one-to-one mapping).
This can be inefficient, particularly during I/O waits as OS threads remain blocked.

# Virtual Threads:

#Introduced in JDK 19 to improve throughput.
Allow creation of more virtual threads than OS threads.
JVM can detach virtual threads from OS threads during waiting, enabling better resource utilization.
Can handle thousands of virtual threads, enhancing performance.
Backward Compatibility:

Virtual threads' functionalities are compatible with existing multithreading concepts, facilitating easier adoption for developers.
Feel free to ask if you need further clarification on any topics!
