# Java Memory Model

 - Java memory model specifies how Java virtual machine works with the computer's memory(RAM).
 - The Java memory model used inside the JVM divides memory into two parts: thread stack and the heap
 - Each thread running a Java program has an individual stack associated to it. The stack contains the information about the methods the thread has called until now to reach the current state.
 - A thread stack also contains the local variables for all the methods getting executed by that particular thread.
 - A thread can only access its own thread stack and not that of other threads.
 - The heap contains all the objects created in your Java application regardless of which thread created the object.
 - Even if object created is assigned to a local variable or created as a member variable of another object, it will be stored inside the heap.
 - If local variable is of primitive type, it is kept on thread stack
 - In case a local variable is a reference to an object then the reference is kept on stack but the object itself is kept on the heap.
 - If the object contains method and that method has local variables, then these variables are stored in thread stack.
 - Object's member variable are stored on heap alongside the object. Both primitive & objects.
 - Static class variables are also stored on heap along with class definition.
 - Objects on the heap can be accessed by all threads that have the reference to that object.
 - On the hardware level both the stack and the heap are present in the main memory. Parts of stack and heap may be present on the CPU cache and internal CPU registers.

#### Problems when objects and variables are stored in different memory areas
 - **Visibility of shared objects**
 
   If two threads are accessing an object without volatile declarations or synchonizations, updates done by one thread may not be visible to other threads. For example: If one thread makes the changes to an object, it loads it from the main memory to its CPU cache. Now if the cache has not been flushed to the main memory another thread accessing the object will get an older copy of this reference resulting in data corruption. This is also called as **Visibility Problem**

   A solution to this is to use the Java **volatile** keyword. The **volatile** makes sure that the object is always read from the main memory and always written back to main memory when updated.
   
   If a thread reads a **volatile** variable then all the variables visible to **volatile** variable will also be read from memory.
   
   **volatile** comes with its challenges such as:
    - **Instruction reordering challenge**
       
       As the VM can reorder instructions for performance, the reordering can result in an old value of a variable in the memory. Example:
       ```
       public class Person {
          int id;
          int salary;
          volatile String name;

          public void doChanges(int id, int salary, String name) {
            this.id = id;
            this.salary = salary;
            this.name = name;
          }
       }
       ```

       In the above case the ```id``` and ```salary``` will be read from the main memory when we read the **volatile** variable ```name```. But if VM reorders the variables in following order.
       ```
       public class Person {
          int id;
          int salary;
          volatile String name;

          public void doChanges(int id, int salary, String name) {
            this.name = name;
            this.id = id;
            this.salary = salary;
          }
       }   
       ```
       The VM will read all three variables while reading the **volatile** variable, but does not writes back to the memory when it updates the other two variables resulting in an old value to be present in main memory. This is also known as **Instruction reordering challenge**.
   
       To solve the above mentioned problem **volatile** keyword gives a *happen before guarantee* which states that:
        - Read and write to a variable cannot be reordered to occur after a write to the **volatile** variable if the reads/writes originally occured before the write to **volatile** variable. As an analogy, in case of write after-to-before switch is allowed but before-to-after switch is not allowed.
        - Read and write to a variable cannot be reordered to occur before a read to the **volatile** variable if the reads/writes originally occured after the read to **volatile** variable. As an analogy, in case of read before-to-after switch is allowed but after-to-before switch is not allowed. 

    - **Race conditions** 
 
       **volatile** keyword is not enough when two or more threads are trying to update a **volatile** variable simulataneously. The small time gap between reading a **volatile** variable from main memory, updating it and then writing it back to the main memory creates a race condition where another thread can read the old value from main memory and result in out of sync results. In such a case we need to use something as ```synchronized``` block to take care of multiple threads updating the value.

    - **Performance Consideration**
      
      Using **volatile** enforces that main memory is accessed for every read and write. We do not leverage the faster read time of a cache. Also using **volatile** prevents from instruction reordering in some cases which affects the performance of the program.
 
