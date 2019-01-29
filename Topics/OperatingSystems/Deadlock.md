# Deadlock

A deadlock is a condition when one two or more threads are blocked waiting to obtain a locks that some other threads in the deadlock are holding. Deadlock can occur when multiple threads need the same locks, at the same time but get it different order.

```
Thread 1 locks A, Requires B
Thread 2 locks B, Requires A
```

#### Example
A TreeNode class
```
class TreeNode {

    TreeNode parent   = null;
    List children = new ArrayList();

    public synchronized void addChild(TreeNode child){
        if(!this.children.contains(child)) {
            this.children.add(child);
            child.setParentOnly(this);
        }
    }

    public synchronized void addChildOnly(TreeNode child){
        if(!this.children.contains(child)) {
            this.children.add(child);
        }
    }

    public synchronized void setParent(TreeNode parent){
        this.parent = parent;
        parent.addChildOnly(this);
    }

    public synchronized void setParentOnly(TreeNode parent){
        this.parent = parent;
    }
}
```

```
    public static void main(String[] args) {
        TreeNode parent = new TreeNode();
        TreeNode child = new TreeNode();

        Thread t1 = new Thread(() -> {
            parent.addChild(child);
        });

        Thread t2 = new Thread(() -> {
            child.setParent(parent);
        });

        // Locks the parent object
        t1.start();

        // Locks the child object
        t2.start();
    }
```
Thread t1 tries to add a child to the parent object using a synchronized block. This result in locking the parent object. Thread t2 tries to set the parent for a child using synchronized block in turn locking the child. Now when ```addChild``` method tries to access the child object for ```setParentOnly``` method call, it is blocked. At the same time when ```setParent``` method tries to access the parent object for ```addChildOnly``` method, it is blocked. Hence resulting in a deadlock situation.

#### More complicated deadlocks
 - Deadlocks on multiple threads
    ```
    Thread 1 locks A, Requires B
    Thread 2 locks B, Requires C
    Thread 3 locks C, Requires D
    Thread 4 locks D, Requires A 
    ```
    Such a deadlock is more difficult to detect
  
- Deadlock on database

  If a record is locked while doing a database transaction and multiple threads are trying to perform the transaction, it may result in a deadlock situation.
  ```
  Transaction 1, request 1, locks record 1 for update
  Transaction 2, request 1, locks record 2 for update
  Transaction 1, request 2, tries to lock record 2 for update.
  Transaction 2, request 2, tries to lock record 1 for update
  ```

#### Conditions for deadlock
 - Mutual Exclusion
 - No preemption
 - Hold and wait
 - Circular wait
 
#### Deadlock prevention
Techniques for deadlock prevention
  - **Lock Ordering**
  
    Deadlock occurs when multiple thread require the same threads but acquire them in different order. If the order in which threads are acquired by a thread is kept same for each time then the deadlock would not occur.
  - **Lock Timeout**
  
    If we put a timeout on lock for a time which it can try to acquire a lock. If the thread is unable to acquire the lock in the given timeout then it pause the execution, frees all the locks and waits for a random amount of time before retrying. Waiting for this random amount of time helps other thread to acquire the freed locks and continue with the execution. Hence the deadlock is broken.
    
    For this purpose we cannot use the Java synchronized blocks and we will have to create a custom lock using the ```java.util.concurrency``` package.
    
    Also in this case, we need to analyze the timeout period because in presence of multiple locks, it can be possible that the deadlock situation remains as it is even in presence of a timeout.
  - **Deadlock Detection**
  
    In this technique a datastructure (map/graph) is used to keep track of which locks are acquired by which threads and which threads have requested to access a lock. In case a thread has requested for a lock which is already acquired then it is checked that which thread has right now acquired the required lock. It is verified that the thread acquiring the lock is not requesting for a lock which is already acquired by the first thread. If that is the case then we can say that a deadlock has occured. *We follow a Depth first traversal of the data structure in case of multiple locks*
    
    Once it is confirmed that a deadlock has occured, we can stop the execution of threads, release the locks and then wait for some random time before retrying.
    
    Another approach can be to give every thread a random priority and then backup the thread(or few threads) rather than backing up all the threads. The rest of the threads can continue execution.
