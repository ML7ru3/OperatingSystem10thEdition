# Objectives
- To present the concept of process synchronization
- To introduce the critical-section problem, whose solution can be used to ensure the consistency of shared data
- To present both software and hardware solutions of the critical-section problem
- To examine several classical process-synchronization problems
- To explore several tools that are used to solve process synchronization problems

# Recap
- A cooperating process is one that can affect or be affected to othres.
- Can be either:
  - Directly share a logical address space
  > achieved through the use of threads
  - Be allowed to share data only through files or messages
---
- Concurrent processes may be iterrupted at any time, partially completing execution.
- Concurrent access to shared data may result in data inconsistency.


# Producer-Consumer Problem
- Involves 2 types of processes:
  - Producers: generate data
  - Consumers: process data
- The challenge is to ensure that the producer doesn't add data to a full buffer and the consumer doesn't remove data from an empty buffer while avoiding conflicts when accessing the buffer.
---
- We can do so by having an integer ``counter`` that keeps track of the number of full buffers


```C
//Producer
while (true){
// produce an item in next produced
    while (counter == BUFFER_SIZE); // do nothing
    buffer[in] = next_produced;
    in = (in + 1) % BUFFER_SIZE;
    counter++;
}


//Consumer
while (true) {
    while (counter == 0); // do nothing
    next_consumed = buffer[out]
    out = (out + 1) % BUFFER_SIZE;
    counter--;
}
```

> Variable ``counter`` is shared by both producer and comsumer

# Race condition
- A situation when several processes access and manipulate the same data concurrently and the outcome of the execution depends on the particular order in which the access takes place, is called **a race condition**


# Critical Section Problem
- Consider system of n processes
- Each process has criticla segment of code
  - Process may be changing common variables, updating table, writing files.
  - When one process in critical section, no other may be in its critical section
- **Critical section problem** is to desgin protocol that the processes can use to cooperate and solve this problem.

```C
do{
    //entry section
    critical section
    //exit section
    remainder section
} while (true);
```

## Solution to Critical - Section Problem
1. **Mutual Exclusion** : If process Pi is executing its critical section, then no other process can be executing in their critical sections
2. **Progress**: If no process is executing in its critical section and there exist some processes that wish to enter their critical section, the the process should be allowed to enter the critical section
3. **Bounded Waiting** - A bound must exist on the number of times that other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted.

--- 
- Two approaches depending on if kernel is preemptive or non-preemptive:
  - **Preemptive** - allows preemption of process when running in kernel mode
  - **Non-preemptive** - process runs until it exits kernel mode, blocks, or voluntarily yields CPU

## Peterson's Solution
- Uses busy waiting (a spinlock)
- The two processes share two variables:
  - ``int turn;``
  - ``Boolean flag[2]``
- The variable ``turn`` indicates whose turn it is to enter the critical section
- The ``flag`` array is used to indicate if a process is ready to enter the critical section. ``flag[i] = true`` implies that process Pi is ready to enter its critical section

```C
do {
    flag[i] = true;
    turn = j
    while (flag[j] && turn == j); //do nothing

    //critical section

    flag[i] = false;
    
    // remainder section
}while (true);
```

> Initialy, the flag[] will be all false turn is randomize

> All the requirements (mutual exclusion, progress and bounded-waiting are met)

## Mutex Locks
- Mutex = Mutual Exclusion
- Protect a critical section by first ``acquire()`` a lock then ``release()`` the lock
- Calls to ``acquire()`` and ``release()`` must be atomic.
- But this solution requires busy waiting.
  - Therefore called a spin lock

```C
acquire(){
  while(!available);
  available = false;
}

release(){
  available = true;
}

do{
  acquire()
  //critical section
  release()
  //remainder section
}
```

### Semaphore
- A semaphore S is an integer variable that, apart from initialization, is accessed only through two standard atomic operations: ``wait()`` and ``signal()``
- More complicated than Mutex Lock


```C
//example
wait(S) {
  while (S <= 0);
  S--
}

signal(S) {
  S++;
}
```

---
- **Binary semaphore** - integer value can range only between 0 and 1
  - Same as mutex lock


# Deadlock and Starvation
- **Deadlock** - 2 or more processes are waiting indefinitely for an event that can be caused by only one of the waiting processes

> When p1 executes ``wait(S)``, it must wait until p0 executes ``signal(S)``. Since these ``signal()`` operation cannot be executed, p0 and p1 are **deadlock**


- **Starvation - indefinite blocking**
- A process may never be removed from the semaphore queue in which it is suspended
- Indefinite blocking may occur if we remove processes from the list associated with semaphore not in FIFO order

> tarvation in OS is a problem that occurs when low-priority processes are indefinitely blocked from executing due to high-priority processes

- **Priority Inversion** - Scheduling problem when lower-priority process holds a lock needed by higher-priority process or a chain of lower-priority processes