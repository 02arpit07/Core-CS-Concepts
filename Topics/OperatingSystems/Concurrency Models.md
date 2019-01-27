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
 - Job execution order is not predefined. There is no way to make sure which jobs get executed first which makes it almost impossible to predict the state of system at any time during the execution.
 - Persistent data structures can be used to solve the above problems. A persistent data structure preserve a previous version of itslef when modified. However, using a persistent data structure affects the performance in a severe manner.
 - Another solution for this is using stateless workers. The workers do not store the state internally and re-read the state every time they need it. The state can be stored in memory or an external database. *A worker that does not keeps the state internally is called stateless.* Rereading the state everytime the worker needs it can slow down the system. Especially when the state is stored in an external database.
 
## Assembly Line
The workers are organized like workers in an assembly line in the factory. Each worker just performs one part of the job and when it has finished that part, it just forwards the job to the next worker.

Each worker runs on its own thread and does not shares state with another worker. Therefore, this is also referred to as *shared nothing concurrency model.* These kind of systems are usually designed to use non-blocking IO.

Systems using assemly line concurrency model are also sometime called as **reactive systems** or **event-driven systems** The system workers react to event happening inside the system, either received from outside world or by another worker.

**Actors** and **Channels** are two similar examples of assembly line models. 

In **Actor** model, workers can send messages to other workers. Other workers on receiving these messages can do the computation. Sending and processing of messages is done in an asynchronous manner. Actors can be used to implement one or more job processing lines.

In **Channels** workers do not directly communicate to one another. Instead they publish their messages to a channel and other workers can listen to these channels for messages. The sender does not have to know who is listening to its messages. The listener can subscribe or unsubscribe to a channel without affecting the worker sending the messages and therefore this results in a loosely coupled system.

#### Advantages
 - Workers do not share state with another workers and therefore avoids concurrency problems which come with the shared states.
 - Since workers know that no other workers would be making changes to their data, they can decide to be stateful and choose to store the state internally. A stateful worker can therefore be faster than stateless worker.
 - As workers are running on single threaded application, single threaded code has advantages that it often conforms better with how underlying hardware works.
 - **Job ordering** is possible and it helps in predicting the state of the system at any given point of time. Also the incoming jobs can be written to the logs which can be later reused to rebuild the system in case of any failure.

#### Disadvantages
 - Disadvantage is that execution of a job is spread over multiple workers and therefore multiple classes of the project making it difficult for dubugging in case of an error.
 
## Functional Parallelism
Functional parallelism has an idea where you implement your system using functions. The functions act as actors sending messages to other functions similar to workers in an assembly line model. A function calling another function is similar to sending a message.

Parameters passed to a function are copied and therefore act as local copy to that function. Therefore, each function execution acts as an atomic operation and each function is executed independently in comparision to other functions.

As functions are executed independently, they can be run on a separate CPU for each function call resulting in an easier parallel execution setup.

Disadvantage to this pattern is understanding which function call we need to parallelize.

## Single Threaded Systems
Same threading is a model where a single threaded systems are scaled to N single threaded systems. It results in N single threaded systems running in parallel.

Same threaded systems usually have one thread per CPU. It is different from multi-threaded system in a way that single threaded system does not share state with other threads. There is no shared memory which two threads access concurrently. 

#### Load Balancing
 - **Single Threaded Microservice:** Each microservice in your system can run on a single thread. As microservice by nature do not share data, a single threaded system is optimal for this case.
 - **Services With Sharded Data:** If the system actually needs to share the data then the data can be sharded into multiple shards which each thread can access independently avoiding any data corruption.

#### Thread Communication
Threads communicate among one another through message passing. Whenever a thread wants to communicate with another thread then it passes a message to that thread as a byte sequence. The thread can receive the message by copying it and then reading it.
