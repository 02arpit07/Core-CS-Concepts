# Producer-Consumer Problem

#### Scenario
A producer produces an item and puts it into a buffer. A consumer consumes the produced item from the buffer.

**Global variable**
```
int count = 0; // Number of items in buffer
int n; // Capacity of buffer
int[] buffer; // Item Buffer
int in = 0; // Index for buffer where producer adds item
int out = 0 // Index for buffer where consumer consumes item
```

**Producer Code**
```
void producer() {
  int temp;
  while (true) {
    produce_item(temp);
    while (count == n); // Infinite loop if buffer is full. No need to add more items to the buffer
    buffer[in] = temp;
    in = (in + 1) mod n; // Incrementing index where producer can add new item
    
    // TRANSACTION STATEMENT
    count = count + 1; // Incrementing the count for total number of items in buffer
  }
}
```

**Consumer Code**
```
void consumer() {
  int item;
  while (true) {
    while (count == 0); // Infinite loop if buffer is empty. Nothing to read from buffer
    item = buffer[out];
    out = (out + 1) mod n; // Incrementing index where consumer can consume new item
    
    // TRANSACTION STATEMENT
    count = count - 1; // Decrementing the count for total number of items in buffer
    
    process_item(item);
  }
}
```

**Transaction Statement**
  - Increment(I)
  ```
    1. Load Rp, buffer[count];
    2. INCR Rp
    3. Store buffer[count], Rp
  ```
  - Decrement(D)
  ```
    1. Load Rq, buffer[count];
    2. DECR Rq
    3. Store buffer[count], Rq
  ```
  
**Problem Case**
Increment count by producer and decrement count by consumer. The count should remain the same. But in absence of process synchronization, it leads to incorrect results
```
Producer => I1 => I2 => Consumer => D1 => D2 => Producer => I3 => Consumer => D3
```

As consumer still has old count after D2 operation, it further decreases the count by 1 resulting in total two decrements.
