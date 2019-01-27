# Concurrency Models

## Parallel Worker Model
Incoming jobs are assigned to different workers. A leader distributes the coming jobs among different workers and each worker completes full job.

#### Advantages
 - Easy to understand
 - To increase parallelism, you just need to add new workers

#### Disadvantages
 - Complexity arises when parallel workers start accessing a shared data store.
 - The thread needs to access the shared data in such a way so that other workers are aware of the changes in the data. The changes should be pushed in the main memory.
 - Threads need to avoid deadlock, race condition and many other shared data concurrency problems.
 - The essence of parallelization is lost when threads are waiting to get the access of the shared data.
