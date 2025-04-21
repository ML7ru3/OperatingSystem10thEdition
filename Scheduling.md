# Objectives
- To introduce CPU scheduling, which is the basis for multiprogrammed OS
- To describe various CPU - scheduling algorithms
- To examine the scheduling algorithms of several OS.

# Basic Concepts
- Maximum CPU utilization with multiprogramming
- CPU - I/O Burst Cycle
  - Process execution consists of a cycle of CPU execution and I/O wait
- CPU burst followed by I/O burst
- CPU burst distribution is of main concern
> Example:
> load store, add store, read from file... : CPU burst
> wait for I/O: I/O burst
> store increment index, write to file: CPU burst
> wait for I/O: I/O burst


# Preemptive vs Non-preemptive Scheduling
- **Non-premeptive shceduling**: One the CPU has been allocated to a process, the process keeps the CPU until it releases the CPU either by terminating or by switching to the waiting state
- **Peremptive**: Allows removal of a process from CPU

# CPU Scheduler
- **Short-term scheduler** selects from among the processes in the ready queue, and allocated the CPU to one of them.
- CPU scheduling decisions may take place when a process:  
  1. Switches from running to waiting state
  2. Switches from running to ready state
  3. Switches from waiting to ready
  4. Terminates
> Scheduling under **1 and 4** are **non-preemptive**

- All other scheduling is peremptive
  - Consider access to shared data
  - Consider preemption while in kernel mode
  - Consider interrupts occurring during crucial OS activities. 

# Dispatcher
- Dispatcher module gives control of the CPU to the process selected by the short-term scheduler; this involves:
  - Context switching
  - Switching to user mode
  - Jumping to the proper location in the user program to restart that program
- **Dispatch latency**  - time it takes for the dispatcher to stop one process and start another running.

# Scheduling Criteria (very important)
- **CPU utilization** - keep the CPU as busy as possible
- **Throughput** - # of processes that complete their execution per time unit
- **Turnaround time** - amount of time to execute a particular process
- **Waiting time** - amount of time a process has been waiting in the ready queue
- **Response time** - amount of time it takes from when a request was submitted until the first response is produced.
  

# Scheduling Algorithms
- First-come, first serve
- Shortest-job first
- Shortest-remaining-time-first
- Priority
- Round-robin
- Multilevel feedback queues

## First - Come, First - Served (FCFS)

![](/assets/Screenshot%202025-04-21%20022536.png "Example of FCFS")

> **Convoy effect** - short process behind long process


## Shortest - Job - First (SJF) Scheduling
- Associate with each process the length of its next CPU burst
- SJF is optimal - gives minimum average waiting time for a given set of processes
    - The difficulty is knowing the length of the next CPU request

![](/assets/Screenshot%202025-04-21%20023130.png "Example of SJF")

### Shortest - remaining - time - first

![](/assets/Screenshot%202025-04-21%20023429.png "Another example of SJF")

> When P1 is executing, it see that P2 have smaller burst time so it changes to P2

## Priority Scheduling
- A priority number (integer) is associated with each process
- SJF is priority scheduling where priority is the inverse of predicted next CPU burst time
- Problem =  **Starvation** - low priority processes may never execute
- Solution = **Aging** - as time progresses increase the priority of the process

![](/assets/Screenshot%202025-04-21%20024036.png "Example of priority scheduling")


## Round Robin (RR)

- Each process gets  a small unit of CPU time (time quantum) usually 10-100 milliseconds. After this time has elapsed, the process is preempted and added to the end of the ready queue.
- If there are n processes in the ready queue and the time quantum is q, then each process gets 1/n of the CPU time in the chunks of at most q time units at once. No process waits more than (n-1)q time units
- Performance
  - q large => FIFO
  - q small => overhead is too high

![](/assets/Screenshot%202025-04-21%20024657.png "Example with q = 4")

---
- Although the time quantum should be large compared with the context-switch time
- A rule of thumb is that 8- percent of the CPU bursts should be shorter than the time quantum.

# Multilevel Queue
- Ready queue is partioned into separate queues
  - **foreground** (interactive)
  - **background** (batch)
  
- Each queue has its own scheduling algorithm:
  - foreground - RR
  - background - FCFS
- Scheduling must be done between the queues
  - Fixed prioriy scheduling (serve all from foreground then from background)
  - Time slice - each queue gets a certain amount of CPU time which it can schedule amongst its process; i.e 80% to foreground in RR
  - 20% to backgroudn in FCFS
![](/assets/Screenshot%202025-04-21%20025546.png "Example of multilevel queue")

---

## Multilvel Feedback Queue
- A process can move between queues, aging can be implemented this way.
- Multilevel-feedback-queue scheduler defined by following parameters:
  - Number of queues
  - Scheduling algos for each queue
  - Method used to determine when to upgrade a process
  - Method used to determine when to demote a process
  - Method used to determine which queue a process will enter when that process needs service
![](/assets/Screenshot%202025-04-21%20030518.png "Example of multilevel feedback queue")

# Multicore Processors
- Faster and consumes less power
- Researchers have discovered that when a processor accesses memory, it spends a significant amount of time waiting for the data to become available. This situation, known as **memory stall**, may occur for reasons... such as a **cache miss** *(accessing data that are not in cache memory)*
> Multicore use that advantage to do another task.

![](/assets/Screenshot%202025-04-21%20031323.png "example of multicore processor")


> **Turn around time** is the time to complete the process *(Completion - Arrival)*

> **Waiting time** amount of time a process spent in the ready queue