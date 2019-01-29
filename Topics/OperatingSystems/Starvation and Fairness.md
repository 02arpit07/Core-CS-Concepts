# Starvation and Fairness
If a thread does not get CPU time because other grab it all, it is called startvation. The thread is "starved to death" as other thread are allowed to take all the CPU time. The solution to this is called fairness i.e. all threads are fairly granted a chance to execute.

#### Causes of starvation
 - Threads with high priority take up all the CPU time from threads with low priority
 - Threads are waiting indefinitly to enter the synchronized block to enter a synchronized block as other threads are constantly allowed access before it.
 - Threads waiting on object remain waiting indefinitely because other threads are constantly awakened instead of it.

In Java ```wait``` is an instance method used for thread synchronization. It can only be called from a synchronized block and it releases the lock so that other threads can jump and acquire the lock. Whereas ```sleep``` method just pause the execution and does not release the lock

For waking up a thread from ```wait``` state we need to call either ```notify``` or ```notifyAll``` method. ```notifyAll``` wakes up all thread in waiting state. Both these methods also need to be called from synchronized block.

#### Fairness using Java
 - Using locks instead of synchronized blocks
 - Fair lock
  
  Once a thread acquires a lock, other threads trying to access the lock are kept in a queue and put into wait state. Once the original thread releases the lock, one thread is removed from the queue and notified to acquire the free lock.
  
