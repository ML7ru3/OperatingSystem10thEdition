# Objective
- To introduce the notion of a process - a program in execution
- To describe communicaiton in client - server systems
- Describe the various features of processes, including ***scheduling**, **creation and termination**, and **communication***.


# Concept
-  Program is passive entity stored on disk, process is active
> Program becomes process when executable file loaded into memory
 
 ---

- **Process** - a program in execution
- Multiple part of a process:
    - The program code **(text section)**
    - Current activity including **program counter**, processor registers
    - **Stack** containing temporary data *(function params, return addresses, local var)*
    - **Data section**
    - **Heap** contain memory  dynamically allocated 
![alt text](/assets/Screenshot%202025-04-18%20020547.png)


## PCB (Process Control Block)
> Also called *task control block*
- Process state - running, waiting, etc 

![](/assets/Screenshot%202025-04-18%20020842.png "how CPU Switch from Process to Process")

> *How can I see is that process when interrupted will be save (the state) into PCB and loaded when the scheduler calls it*

## Process state
![](/assets/Screenshot%202025-04-18%20021318.png "All state of a process")

> Admittion of a process happens when a program loaded into memory

> Interrupt happens when exists **system calls** different from running state to waiting state

> **Waiting = Blocking**

# Process Scheduling

> When process from ready state to running state

- Maximize CPU use, quickly switch processes onto CPU for time sharing
- Job of the **process scheduler** is selects among available processes for next execution on CPU

![](/assets/Screenshot%202025-04-18%20022410.png "Representation of Process Scheduling")

> When *I/O request, time slice expired, fork a child, wait for an interrupt* happens the process will from running state to ready queue (ready state)

# Scheduler 
- **Short-term scheduler (CPU Scheduler)**: selects which process should be executed next and allocates CPU
  - Invoked frequently (milliseconds) => must be fast
- **Long-term scheduler (job scheduler)**:  selects which processes should be brought into the ready queue in main memory 
  - Invoked infrequently (slow)
- Processes can be described as:
  - I/O bound process: spend more time doing I/O than computation
  - CPU bound process: spend more time doing computation


- **Medium-term scheduler**: can be added if degree of multiple programming needs to decrease
    - Remove process from memory, **swapping**, store on disk.

# Context Switch
- When CPU switches to another process, the system must **save the state** of the old process and load the **saved state** for the new process via a context switch


# Process Creation
- **Parent** process create **children** processes, which, in turn create other processes, forming a tree of processes.
- Generally, process identified and managed via a process identifier **(pid)**
![](/assets/Screenshot%202025-04-18%20023654.png "Tree of Processes in Linux")

> Unix examples
> - **fork()**: system call creates new process
> - **exec()**: system call used after a fork() to replace the process memory space with a new program


# Process Termination
- Process executes last statement and then asks the OS to delete it using the **exit()** system call
  - Return status data from child to parent (via **wait()**)
- Parent may terminate the execution of children processes using the **abort()** system call.
    - Child has exceeded allocated resources
    - Task assigned tochild is no longer required
    - The parent exiting and OS does not allow a child to continue if its parent terminates


> **Casading terminattion**: All children, grandchildren, etc  are terminated.

```C
pid = wait(&status)
```
> The call returns *status information* and the *pid* of the terminated process

- If no parent waiting, process is a **zombie**
- If parent terminated without invoking **wait()**, process is an **orphan**

## Zombie process
- When a child process is killed, the entry of the process table keeps, among other things, information about the way the child existed **(PID, exit status,...)**
- When the parent executes **wait()**, it fetches the information from the child entry in the process table, then the kernel removes the child entry in the process 
- Between the time a child exits and wait() is executed, the child is called a **zombie**
> If the parent never executes **wait()**, the zombie process might stay in the system until OS reboot.


## Orphan process
- If a parent process exits when its children are still running (and doesn't kill its children), those children are **orphans**
- They are immendiately adopted by another process (could be the init process)


# Interprocess Communication
- Processes within a system may be *independent* or *cooperating*

- **Inter-process communication** is a mechanism that allows processes to communicate.

- Cooperating process can affect or be affected by other processes, including sharing data, reason:
  - Information sharing
  - Computation speedup
  - Modularity
  - Convenience
## IPC Models (Interprocess communication)
![](/assets/Screenshot%202025-04-18%20025246.png "2 types of IPC Models")

### IPC - Shared memory (*part b of the image*)
- An area of memory shared among the processes that wish to communicate
- Is under the control of the users processes, not the operating system

> The issue here is this needs a mechanism that will allow the user processes to synchronize their actions when they access shared memory

### IPC - Message Passing
- Mechanism for processes to communicate and to synchronize their actions (involves system calls through kernel)

- Message system - processes communicate with each other without resorting to shared variables.

- 2 operations:
  - **send**()
  - **receive**()
- The message size is either fixed or variable

---

- If processes P and Q wish to communicate, they need to:
  - Establish a **communication link** between them
  - Exchange messages via send.recieve
#### Direct communication
- Processes must name each other explicitly 
  - **send**(P, message)
  - **receive**(Q, message)
- Properties:
  - Links are established automatically
  - The link may be unidirectional
  #### Indirect communication
- Message are directed and received from mailboxes
  - Each mailbox has a unique id
  - Processes can communicate only if they share a mailbox
- Properties:
  - Link established only if processes share a common mailbox
  - Link maybe unidirectional or bi-directional
- Operations:
  - create a new mailbox (port)
  - send and receive messages through mailbox
  - destroy a mailbox
### Synchronization
- Messages passing may be either block or non-block

- Blocking is considered **synchronous**
  - The sender is blocked until the message is received and vice versa
---
- Non-blocking is considered **asynchronous** 
  - Non-blocking send: the sender sends the message and continue
  - Non-blocking receive either
    - A valid message
    - Null message

> Assynchronous: not existing or happnening at the same time

- If both receive and send are blocking, it is considered rendezvous 
> A meeting at an agreed time and place, typically between 2 people


## Communications in Client-Server Systems

- Sockets
- Remote Procedure Calls
- Pipes
- Remote Method Invocation
### Sockets
- Is defined as an endopint for communication
- Eg: The socket **161.25.19.8:1625** refers to port **1625** on host with IP address **161.25.19.8**
### Remote procedure call
- Remote procedure call (RPC) abstracts procedure calls between processes on networked systems
- **Stubs** - client-side proxy for the actual procedure on the server

### Pipes
- Acts as a conduit allowing 2 processes to communicate
  - Ordinary pipes *(anonymous pipes)*- cannot be accessed from the outside the process that created it
  - Named pipes *(more powerful than ordinary pipes)* - can be accessed without a parent-child relationship


# The address space of processes

- Once a process is created, its components are stored in some of these bytes
- The addresses of the bytes used by a process is called the **"address space"** of the process

## Binding times
> Chatgpt: Refers to the points during a program's lifecycle when certain decisions are made or values are associated to variables, names, or behaviors
- **Compile time**: Source are complied into object files designed to be loaded into any physical memory location
- **Load time**: if memory location not know at compile time, the loader (linker) bind the re-locatable addresses to absolute addresses
- **Execution time**:
  - Addresses in the executable are mapped to physical addresses at run time. **Binding is delayed until run time**
  - Most OS use this binding method

## Physical Address Space
- A physical address space is the actual address space in the main memory, used by MMU (Memory Management Unit)
- The term "physical" refers to precise.

## Logical Address Space
- Also called virtual address space, which is a generated address space by CPU when executing a program.
- These will be translated by the OS (MMU) into physical address space.



# Signal
- Signals are predefined messages initiated by a process, the kernel or a user and sent to another process
  - SIGHUP 1 *hangup*
  - SIGINT 2 *interrupt*
  - SIGQUIT 3 *quit*
  - ...
- As a communication tool among pocessess, signals are sent from inside a process using the kill system call:
-  **kill(my_pid, SIGSTOP)** cause the process to stop itself