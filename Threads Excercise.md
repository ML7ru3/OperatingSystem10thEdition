5. List the fields of a thread control block (TCB) and explain the purpose of each field.

> - The TCB includes:
>   - Thread identifier
>   - Stack pointer: points to thread's stack in the process
>   - Program counter: points to the current program instruction
>   - State of the thread: running, ready, waiting
>   - Thread's register values
>   - Pointer to the PCB of the process that the thread lives on

7. Explain why each thread control block has entries (fields) to store the contain of its run time registers but does not have entries to store the contain of the dynamically allocated variables (variables that are allocated memory on the heap section of a process).

> - Heap memory is shared among all threads
> - The heap is managed by the process manager, not the scheduler. *(the actual memory is part of the process's heap, not the thread's local storage)*
> - It doesn't change between context switch.

8. Unlike static variables, threads do not share their stack with other threads. Why then the stack is not saved in the TCB when a context switch is performed (thread is de-scheduled)?

> Because it is unnecessary, static variables are accessed by all threads.



9. Consider the following code segment:
```C 
pid_t pid;
pid = fork();
if (pid == 0) { /* child process */
    fork();
    pthread_create( . . .);
}
fork();
```
(a) How many unique processes are created?
(b) How many unique threads are created?

> a, 3, if only count processes that are created.
> b, 2

10. Can a thread forks a new process? If yes, what will be the parent of the new process?
> Yes, the parent of the new process is the process that create that thread.

11. In a multi-threaded user process, if a user thread T (which is not detached) calls pthread exit(), thread T will become zombie. When can the user space stack of thread T get deleted?

> When the process/ thread that created thread T calls ``pthread_join()`` to that thread T.

12. Assume a thread t1 creates a child thread t2. Does both threads share the same address space?
> Yes.


13. Assume the image of a process is swapped out from main memory to the external
memory. The process has 3 active threads, two in the ready queue and one in the
running state. What will happen to those 3 threads?

> The thread in the running state will be re-scheduled.
> The other 2 threads in ready queue will be removed from the queue.