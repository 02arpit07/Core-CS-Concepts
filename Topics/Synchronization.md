# Synchronization

#### Typical flow of a concurrent system
Multiple processes try to execute a piece of code and in order to do so they go through a flow of operations as below:
```
Entry Code (Before a process starts executing critical section code)
Critical Section
Exit Code (After a process finishes executing critical section code)
```
The exit code has operation as up()/signal() and ebtry code has operation as down()/wait()/post()/release().


#### Locks 
Locks are used to lock critical sections of the program from **threads of the same process** usually

#### Mutex
Mutex are used to lock critical sections/files/databases from **multiple processes** or **threads running in multiple processes**.

#### Semaphore
To allow **particular group of accessors or threads** to access a critical section of the program. It is used to prevent race condition. 

*It is an integer variable which is used a tool which is used in a mutual exclusive manner to achieve synchronization among various concurrent processes.*

 - #### Counting Semaphore
 
    The value for counting semaphore can range from negative infinity to infinity. 
    
    Example of entry code:
    ```
    down(semaphore s) {
      s = s - 1;
      if (s < 0) {
        // Put the process p in suspend list
        p.sleep();
      }
      else {
        return;
      }
    }
    ```
    
    Example of exit code:
    ```
    up(semaphore s) {
      s = s + 1;
      if (s >= 0) {
        // Select a process p from suspend list
        p.wakeup();
      }
    }
    ```
    
 - #### Binary Semaphore
 
    Only two possible values i.e. 0 and 1. Two possible operations i.e. up(signal) and down(wait). 
    
    Example of Down opertion: Assume the current value of semaphore(s) as 1. (s = 1)
    ```
    if (s == 1) {
      s = 0; // Successful operation
    }
    else {
      // Block the process p
      // Add it to suspend list
      p.sleep();
    }
    ```
    Example of Up opertion. If the value of s is 1, it will remain 1. The first priority for up operation is to check the suspended process list and wake up if there are any processes present.
    ```
    if (suspended processes list is empty) {
      s = 1;
    }
    else {
      // Select a process p from the suspended process list
      p.wakeup();
    }
    ```
    
  
