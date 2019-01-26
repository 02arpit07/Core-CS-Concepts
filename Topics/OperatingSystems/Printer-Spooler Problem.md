# Printer-Spooler Problem

When multiple users are sending commands to a printer to print their documents, these commands are first stored in a queue called spooler and then spooler sends each command one by one to the printer to execute.

## Problem

**Shared variables**
```
int[] spooler; // Spooler list
int in = 0; // Index of current position of spooler/size of spooler
```
**Printer Code**
```
I1 Load Ri, in;
I2 Store spooler[Ri] "FILENAME";
I3 INCR Ri;
I4 Store in, Ri;
```

#### Problem Scenario
There are two processes p1 & p2 trying to interact with the printer
```
p1 => I1 => I2 => I3 => p2 => I1 => I2 => I3 => I4 => p1 => I4
```

Here process p1 starts executing. Store a file at spooler[Ri] and increment Ri. It has right now not updated the shared 
variable ```in```. A context switch happens and process p2 starts executing. As the shared variable is still 0, p2 processes 
all 4 instructions and overwrites the file in spooler at index ```in```. Now it terminates and context switch is done back to 
p1. p1 just finishes its execution by executing I4 and updating value to 1. 

Due to lack of synchronization, file added by p1 is overwritten by p2 and this results in **loss of data**.
