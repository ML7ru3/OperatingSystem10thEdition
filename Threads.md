# Objectives
- To introduce the notion of a thread - a fundamental unit of CPU utilization that forms the vasis of multithreaded computer system
 
# Motivation
- Most modern app are multithreaded
- Thread run within a process
- Process creation is heavy-weight while thread creation is light-weight
- Can simplify code, increase effciency
 
# Single and Multithread processes
 
![](/assets/Screenshot%202025-04-19%20001304.png)
 
> Code, data and files are from process section. One or multiple threads can be inside a process and share the same text, data and heap segments with the process in which they execute.
 
 
> Each thread has its own **stack pointer** (SP) gives of the thread stack inside the process stack space and **program counter** (PC) points to the next instruction in the text segment
 
# Threads versus processes
- A thread has iits own
    - PC
    - stack, registers
    - a schefilable data structure (TCB - thread control block)
    - its own entry in the process table
- Threads run within a process
- A process provides
     - The address space
     - User identity and privileges
     - File and I/O resources
 
# Benefits
- **Respornsiveness** - may allow continued execution if part of process is blocked.
- **Resource sharing** - thread share resources of process, easier than shared memory or message passing
- **Economy** - cheaper than process creation, thread switching lower overhead than context switch
- **Scalability** *(the capacity to be changed in size or scale)*
 
# Thread codes
Some POSIX threads operations
- ```pthread_create``` create a thread
- ```pthread_join``` wait for a thread
- ```pthread_exit``` exit a thread without exiting process
> Works like wait() in process
- ```pthread_self``` find out own thread ID
 
The parent thread has a set of declarations:
- ```pthread_t tid``` declares a variable, tid, which receives the id of the thread
- ```pthread_attr_t attr``` creates a data structure to store the attributes of the thread.
 
 
## A first ``pthread`` example
 
```C
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
 
int sum; /* this data is shared by the thread(s) */
 
/* threads call this function*/
void *runner(void *param){
    int i, upper = atoi(param);
    sum = 0;
    for (i = 1; i <= upper; i++) sum += i;
 
    pthread_exit(0);
}
 
int main (int argc, char *argv[]) {
    pthread_t tid; /* the thread id*/
    pthread_attr_t attr; /* set of thread attributes*/
 
    /* set the default attributes of the thread */
    pthread_attr_init(&attr);
 
    /* create the thread */
    pthread_create(&tid, &attr, runner, argv[1]);
 
    /* wait for the thread to exit */
    pthread_join(tid, NULL);
    printf("sum = %d\n", sum);
}
```
 
> ```argc```- Argument count - tells you how many arguments were passed to your program from the command line
 
> ```argv``` - Argument vector - an array of C strings representing the actual arguments
> - argv[0] is the name of the program
> - argv [1], argv[2],... are actually arguments passed
 
- The process above has 2 threads:
    - The initial threads executing main()
    - The summation (or child) thread performing runner() fumction
 
- The thread executing runner() has 2 types of variables:
    - Local var of the function runner(), which not visible to the parent thread (main())
    - A global variable "sum"
- The summation thread terminates when it calls the function ```pthread_exit(0)```
 
## Terminating Thread: ```pthread_detach```
- To avoid memory leak because the parent forgot to join with the child thread.
- This function will instruct the OS to release all the resource of a thread once it exits.
 
 
```C
int pthread_detach(pthread_t thread);
pthread_detach(pthread_self());
pthread_exit();
```
> Above the thread detach itself
 
```C
pthread_create(&tid, NULL, processfd, &fd);
ptthread_detach(tid);
```
> Example where the child thread just created it set to detach on exit
 
- To precent memory leaks, long-running program should eventually call either ```pthread_detach``` or ``pthread_join`` for every thread.
 
 
## Killing a Thread
- Threads can force other threads to return through cancellation mechanism called ``pthread_cancel()``
- The single parameter of ``pthread_cancel()`` is the thread ID of the target to be canceled.
 
## Threads of threads
- As expected , thre exist no parent child relationship among threads at the system level
- thread have no parent.
 
 
# Threads Attributes
- When a thread is created, it is passed a set of attributes which are used to run the thread. For linux, the default attributes are:
    - Detach state
    - Scope
    - Inherit Scheduler
    - Scheduling policy
    - Scheduling priority
    - Guard size
    - Stack address
    - Stack size


## Detach state
- The possible values of the thread detach state are:
  - ``PTHREAD_CREATE_JOINABLE``
  - ``PTHREAD_CREATE _DETACHED``
- By default, threads are joinable.
- If the thread is joinable, it means the parent will execute a ``pthread_join()`` on that thread

---
- Pthreads can be created as DETACHED
- The detached state allow the resources to be completely release once a thread exit.
- ``pthread_attr_setdetachedstate(&attr, pthread_create_DETACHED);``

## Stack size
- The function: ``pthread_attr_setstacksize()`` 
- Also ``pthread_attr_getstack()`` to examine the stack parameters
  
## Scheduling
- The contentions scope controls the thread competes within the process or at the system level for scheduling resources.
- The ``pthread_attr_getscope`` examines the contention scope

## Scheduling policy
- Linux supports 3 scheduling policies
  - SCHED_FIFO
  - SCHED_RR
  - SHCED_OTHER (user threads only have access to this)
- SHCED_OTHER is for user threads which are scheduled with the **Completely Fair Scheduler (CFS)**, have priority between 100 and 139
- SHCED_FIFO and SCHED_RR are real time policies for real time threads, have priority between 1 and 99
 
# Multicore Programming
- Multicore or multiprocessor systems putting pressure on programmers, challenges include:
    - Dividing acts
    - Balance
    - Data splitting
    - Data dependency
    - Testing and debugging
- Types of parallelism:
    - Data parallelism - distribute subsets of the same data across multiple cores
    - Task parallelism - distributing threads across cores.
 
## User Threads and Kernel Threads
 
- User threads - management doen by user-level threads livrary
    - **POSIX Pthreads**
    - Window threads
    - Java threads
 
- Kernel threads - Supported by the Kernel
 
# Multithreading Models
- A relationship must exist between user threads and kernel threads
- 3 possible types:
    - Many to One
    - One to One
    - Many to Many
## Many to One
- Many user-level threads mapped to single kernel thread
- One thread blocking causes many to block
- May not be parallel
 
- Example:
    - Solaris Green Threads
    - GNU Portable Threads
 
## One to One
- Each user-level thread maps to kernel thread
- Creating a user-level thread creates a kernel thread
- Can cause overhead *(too expensive)*
- More concurrency than many-to-one
 
- Example:
    - Window
    - Linux
    - Solaris 9 and later
 
 
## Many to Many
![](/assets/Screenshot%202025-04-19%20003456.png) "Many to Many model")
- Allows the OS to create a sufficient number of kernel threads
 
 
## Two-level Model
![](/assets/Screenshot%202025-04-19%20003716.png "Two level model")
 
- Similar to M:M, except that it allows a user thread to be bound to kernel thread.
- Examples:
    - IRIX
    - HP-UX
    - Solaris 8 and earlier
 
# Thread Lib
- Thread library provides programmer with API for creating and managing threads
- 2 primary ways for implementing:
    - Library entirely in user space
    - Kernel-level lib
 
## Pthreads
- Maybe provided either as user-level or kernel-level
- **Specification**, not **implementation**
 
 
## Java Threads
- Are managed by the JVM
- Java threads may be created by
 
```Java
public interface Runnable
{
    public abstract void run();
}
```
    - Exending thread class
    - Implementing the Runnable interface
 
 
## Linux threads
- Linux refers to them as tasks rather than threads
- Thread creation is done through ```clone()``` system call
- ```clone()``` allows a child task to share the address space of the parent task (process)

# Threading issues
- **fork()** and threads
- Multi-threading and signla handling
- Thread-Local Storage

## fork() and threads
- The question is: Does fork() duplicate only the calling thread or all threads?
    - Some Unix (Solaris) have 2 versions of fork():
      - forkall(): duplicate all threads
      - fork1(): duplicate only the thread is invoking
  
> Now when a fork is executed inside a thread, only that particular thread is duplicated in the child process


## Multithreading and signal handling
- Unix signals were originally designed for single-threaded processes. Multi-threading complicates the picture.
- Signals can be sent to a specific thread using ``pthread_kill(TID, signal)``
> I don't think the kernel can send signals to a thread

## Thread-Local Storage (TLS)
- In thread-local storage,each threads has its own copy of global, static variables.

```C
/******************************************************************************
* FILE: arrayloops.c
* DESCRIPTION:
*   Example code demonstrating decomposition of array processing by
*   distributing loop iterations.  A global sum is maintained by a mutex
*   variable.  
* AUTHOR: Blaise Barney
* LAST REVISED: 01/29/09
******************************************************************************/
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/syscall.h>

#define NTHREADS     4
#define ARRAYSIZE   1000000
#define ITERATIONS   ARRAYSIZE / NTHREADS

double  sum=0.0, a[ARRAYSIZE];
pthread_mutex_t sum_mutex;

void *do_work(void *tid) 
{
  int i, start, *mytid, end;
  double mysum=0.0;
  pid_t tid2 = syscall(SYS_gettid);

  /* Initialize my part of the global array and keep local sum */
  mytid = (int *) tid;
  start = (*mytid * ITERATIONS);
  end = start + ITERATIONS;
 
  for (i=start; i < end ; i++) {   a[i] = i * 1.0;   mysum = mysum + a[i];   }
  printf ("Thread %d with id %d is doing iterations %d to %d with its partial sum =  %.2f\n",*mytid,tid2,start,end-1,mysum); 
  /* Lock the mutex and update the global sum, then exit */
  pthread_mutex_lock (&sum_mutex);
  sum = sum + mysum;
  pthread_mutex_unlock (&sum_mutex);
  sleep(10);
  pthread_exit(NULL);
  }


int main(int argc, char *argv[])
{
  int i, start, tids[NTHREADS];
  pthread_t threads[NTHREADS];
  pthread_attr_t attr;

  /* Pthreads setup: initialize mutex and explicitly create threads in a
     joinable state (for portability).  Pass each thread its loop offset */
  pthread_mutex_init(&sum_mutex, NULL);
  pthread_attr_init(&attr);
  pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
  printf("Number of threads is %d\n",NTHREADS);
  for (i=0; i<NTHREADS; i++) {
    tids[i] = i;
    pthread_create(&threads[i], &attr, do_work, (void *) &tids[i]);
    }

  /* Wait for all threads to complete then print global sum */ 
  for (i=0; i<NTHREADS; i++) {
    pthread_join(threads[i], NULL);
  }
  printf ("This is the sum as calculated by the threads Sum= %e \n", sum);

  sum=0.0;
  for (i=0;i<ARRAYSIZE;i++){ 
  a[i] = i*1.0;
  sum = sum + a[i]; }
  printf("This is the verification from the main thread %e\n",sum);

  /* Clean up and exit */
  pthread_attr_destroy(&attr);
  pthread_mutex_destroy(&sum_mutex);
  pthread_exit (NULL);
}
```
> For this example, instead of using ``mysum`` locally, a global variable ``Local_sum`` could have been declared as thread-local, then each thread will have copy of it and stores local sums in it


# Thread Control Block (TCB)
- Very similar to PCB (Process Control Block), TCB (Thread Control Blocks) represents thread generated in the system.
- One TCB for each thread
- The TCB includes:
  - Thread identifier
  - Stack pointer: points to thread's stack in the process
  - Program counter: points to the current program instruction
  - State of the thread: running, ready, waiting
  - Thread's register values
  - Pointer that points to the PCB of the process that the thread lives on

## Thread Context Switch
- 2 types:
  - Context switch between 2 threads in the same process
  - Context swithc between 2 threads in different processes
  
- A thread context switch in the same process involves to change the execution context (register, stack pointer, program counter)

- With different processes, it also involves to change address space.

> When a context switch occurs, the OS saves this data into the TCB so the thread can later resume seamlessly.

