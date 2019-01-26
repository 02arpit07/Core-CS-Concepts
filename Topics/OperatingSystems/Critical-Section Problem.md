# Critical Section Problem

*Critical section is a part of a program where shared resources are accessed by multiple processes.*

To enable synchronization, the common code is put in a common section whereas the code which is associated to a single process is kept in non-common section.

This is to make sure that race condition is avoided if two processes try to access a common file/code/variable at the same time.

#### Typical flow of a program
```
Entry Code
Critical Section Code
Exit Code
```

#### Synchronization Mechanism
 - Mutual Exclusion (PRIMARY)
 - Progress (PRIMARY)
 - Bounded Wait (SECONDARY)
 - No assumptions related to hardware speed (SECONDARY)
 
**Mutual Exclusion** is verified by checking if the critical section of the code is accessed by only one process at a time.

**Progress** is shown in the way if the process continues executing without any blocking by any of the process. It is blocked in case of a *deadlock*

**Bounded Wait** means there should be a set wait time associated to a process after which it is assigned to the CPU. This is to prevent the condition where a process is kept in wait for an infinite amount of time and gets assigned to the CPU for negligible amount of time in future. This situation is known as *starvation*

There should not be any assumptions related to a particular hardware systems. The solution should be portable on any operating system and it should be universal.
