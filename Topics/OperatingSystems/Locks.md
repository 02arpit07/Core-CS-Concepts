# Locks

Locks are thread synchronization mechanism similar to ```synchronized``` blocks but with more flexibility for customization. There are various implementations of ```Lock``` interface under ```java.util.concurrent.locks``` package.

## Problem Statement
So we have a class called ```Counter``` which has a attribute called ```count``` and a method to increment this attribute by 1.
```
class Counter {
    private int count = 0;

    public void increment() {
        count = count + 1;
    }

    public int getCount() {
        return count;
    }
}
```

So when multiple threads try to access the increment method, they might update the value of ```count``` at the same time which can result in data corruption. 
```
        Counter counter = new Counter();

        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i=1; i<=100; i++) {
                    counter.increment();
                }
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i=1; i<=100; i++) {
                    counter.increment();
                }
            }
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println(counter.getCount());
```

*Note that the increment is not one operation but a series of three operations - Getting the current value of ```count```, incrementing it by 1 and then replacing the existing value with new value.*

## Solution using various lock implementation

#### Reentrant Lock
Reentrant lock allows thread to enter into lock on resource more than once. We can now use a lock on the increment method to make sure only one thread has the access to increment functionality at a given time.
```
    private int count = 0;
    private final Lock lock = new ReentrantLock();
    
    public void increment() {
        lock.lock();
        try {
            count = count + 1;
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        finally {
            if (lock != null) {
                lock.unlock();
            }
        }
    }
```

#### ReadWrite Lock
ReadWrite Lock allows multiple threads to access the resource but only allows one thread to update the resource. It has separate pair of locks for reading & writing. Read lock can be held by multiple threads as long as write lock is not held by any thread. It is better in terms of performance for applications where more reads are happening as compared to writes.

```
class Counter {
    private int count = 0;
    private final ReadWriteLock lock = new ReentrantReadWriteLock();

    public void increment() {
        lock.writeLock().lock();
        try {
            count = count + 1;
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        finally {
            if (lock != null) {
                lock.writeLock().unlock();
            }
        }
    }

    public int getCount() {
        lock.readLock().lock();
        try {
            return count;
        } finally {
            lock.readLock().unlock();
        }
    }
}
```

Here multiple threads can acquire the ```readLock``` but only one thread can hold the ```writeLock```.

## Lock Reentrance
Synchronized blocks in Java are reentrant. This means that if a Thread enters a synchronized block taking the lock on the monitor object of the synchronized blocks, the thread can enter other blocks synchronized on the same monitor object.
