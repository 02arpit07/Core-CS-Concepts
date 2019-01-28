# Race Condition
A race condition occurs when multiple threads access the shared data and try to change it at same time resulting in data corruption. The situation arises when result of multiple threads executing a section of code differs based on the execution order of the threads.

## Code patterns with high probability of race conditions
Following are two code patterns with high chances of race condition. These patterns can take multiple forms in the business logic.

#### Check & Act
Check and Act kind of pattern can result in a data race. A very typical example is a ```Singleton``` pattern where thread safety is avoided. 
```
public class Singleton {

    public static Singleton singleton = null;

    private Singleton() {
        System.out.println("Constructor called");
    }

    public static Singleton getInstance() {
        if (singleton == null) {
            singleton = new Singleton();
        }

        return singleton;
    }
}
```

If two threads are trying to use the ```getInstance()``` method, then it can result in a race conditions. Thread 1 will check that the instance is null and it will in turn trigger the private constructor to create a new instance. While this is in process, Thread 2 will check for the instance and will find it to be null. Hence it will also trigger the constructor. This results in creating two different instances in case of a Singleton. 
```
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                Singleton s1 = Singleton.getInstance();
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                Singleton s2 = Singleton.getInstance();
            }
        });

        t1.start();
        t2.start();
```
This situation can be avoided in some cases using a hack of putting a sleep time on the active thread. But the sleep time is something which becomes impossible to estimate when working with multiple threads.

One way to resolve this issue is to make the ```getInstance()``` method synchronized.
```
    public static synchronized Singleton getInstance() {
        if (singleton == null) {
            singleton = new Singleton();
        }

        return singleton;
    }
```
Drawback to the above approch is in terms of performance. Each time we need to get an instance, we would need to acquire a lock and this in terms effect the performance.

An alternative to above approach is that we can initially check if we need to create an instance and only then we need to acquire the lock. When we enter the synchronized block with the lock, we again check for null instance to keep the operation atomic
```
    public static volatile Singleton singleton = null;
    
    public static Singleton getInstance() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }

        return singleton;
    }
```
One thing to note is that the field for Singleton instance would be ```volatile``` in this case to prevent cache incoherent issues.

#### Read Modify Update
Another code pattern where we see a data race condition is where we are reading a value and based on certain conditions, making a decision to whether we would update a value or not. A simple example for this would be a ```HashMap``` where we add the value for a key if that key is not present. So, Thread 1 can initially checks the condition for if the key is present in map and enters the code block. Thread 2 checks the value for the key in the map and as Thread 1 has yet not added the value for key in map, Thread 2 also gets the access to enter the code block. Now both the threads add the value for same key, in-turn updating each other's value and resulting in data corruption.

```
public class UpdateHashTable {

    Map<String, Integer> map;

    public UpdateHashTable() {
        map = new HashMap<>();
    }

    public void addIfNotPresent(String key, int value) {
        if (!map.containsKey(key)) {
            System.out.println("Value Updated to: " + value);
            map.put(key, value);
        }
    }

    public int getValueForKey(String key) {
        return map.getOrDefault(key, 0);
    }
}

```

When 2 threads access the ```addIfNotPresent``` method, it corrupts the value for key in the map as the value is updated twice for the same key.
```
        UpdateHashTable updateHashTable = new UpdateHashTable();

        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                updateHashTable.addIfNotPresent("A", 1);
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                updateHashTable.addIfNotPresent("A", 2);
            }
        });

        t1.start();
        t2.start();
``` 

The output we see is as below:
```
Value Updated to: 1
Value Updated to: 2
```

To avoid the above problem, we can treat the process of checking for absence of key and updating the key as one transaction. We can make the transaction as synchronized so that only one thread can perform the transaction and when the other thread comes into play, it gets the correct state of the map and corresponding keys inside it.

```
    public void addIfNotPresent(String key, int value) {
        synchronized (UpdateHashTable.class) {
            if (!map.containsKey(key)) {
                System.out.println("Value Updated to: " + value);
                map.put(key, value);
            }
        }
    }
```

