# OS Basics

Operating system works as an interface between a user and the hardware. Primary goal of OS is to provide an easy way to the users to access hardware. 
A throughput of an OS is number of tasks it executes per unit time. User mostly uses applications to interact with the hardware where OS acts as an interface between User and hardware. OS works through **System Call**

#### Major functionalities of an OS are:
 - Resource Management
 - Process Management through CPU Scheduling algorithms
 - Storage Management(Hard Disk)
 - Memory Management(RAM)
 - Security & privacy
 
## Multiprogrammed & Multitasking OS
In **Multiprogramming OS** multiple processes are executed in RAM in a non-preemptive manner. Non-preemptive means the CPU executes complete process one and then moves on to the next process. Exception to this is when the process being executed, itself goes into I/O leaving the CPU idle. In such a case the CPU picks up the next process in line.

In **Multitasking(Time sharing) OS** works on processes in a preemptive manner. It spends a certain amount of time on one process and then moves on to the next process. It completes the cycle through all the processes and then comes back to working on the first process.

Benifits of multitasking over multiprogramming is the **response time**. Multitasking is more responsive. 
