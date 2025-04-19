7. Describe what happens in the kernel when the following command is executed in a Ubuntu command line interpreter (a shell): ls -l

> - The shell will use **fork()** to create a new process
> - Child process calls **exec()**, replace its memory into */bin/ls* with arguemnt -l
> - Load the **ls** in to binary
> - **ls** run the child process
> - **ls** exits
> - Shell calls **wait()** into **ls** to prevent zombie process

10. Explain what happens before and when you type a command in a Ubuntu command
line interpreter (a shell) if you don’t put & after the command (that is the command is not running in background)



11. When exec() is called, which part of the following PCB fields is modified? the PCB is completely re-written; the PID is changed; the parent pointer is modified; the program counter is updated?


> The parent pointer stays the same
> The PID stays the same
> The program counter is differnt

12. There are 4 sections in the address space of a process: text, stack, heap, and data.
Which of these sections are replaced when exec() is called?

> Because when exec() is called, it will replace current process with a new program. Therefore, all the sections will be replaced.

13.  Consider the following program:
~~~C
int i;
for (i=0; i<3; i++)
    fork();
printf(”xin chao”);
~~~ 
How many times this program print "xin chao" ?

> 2^3 = 8 processes

14. Consider the following program:

```C
int i;
for (i = 0; fork(); i++) {
    if (i == 4) { break; }
    printf("PID: %d, i = %d \n", getpid(), i);
}
```
(a) Excluding the calling process, how many new processes are created?
(b) Assume that initially the process ID executing this code is 3376, and the PID
always increases sequentially by 1. For example, the first time this process calls
fork() the PID of the child is 3377. You can also assume no other processes in the system are calling fork() after this program starts to execute. What will be printed by this program?

> a, 5 processes
> When it done executing everything in the loop, it will return to the head of the loop until i reaches 4. 0->4 = 5
> b, 
```C
PID: 3376, i = 0
PID: 3376, i = 1
PID: 3376, i = 2
PID: 3376, i = 3
```
> - Because in the loop, the loop only continues when fork() returns the value 0 (the parent process), if return value != 0 => the loop will exits, so that's why the loop keeping going within the parent process.
> - And why it stops when i = 3 not i = 4 because the *break;*


1.  How many processes are created by the following code
```C
int main() {
int i;
for (i = 0; i < 10; i++)
    if (i % 2 == 0) fork();
exit(0);}
```
> Because there are 5 fork() available so they create 2^5 = 32 processes

1.  A programmer has written a command line interpreter as follow:
```C
bash(...){
...
    exec(cmd, args);
    fork();
...
}
```
Which statement describes the most accurately this code ?
-  Will not work as the NULL parameter is missing in exec();
- It will work, a shell program must fork and replace itself with the user command;
- Will not work because the shell program is replaced by cmd, the shell will terminate once cmd exits;
- Will not work, the shell should create a thread and then call exec().

> Will not work because the shell program is replaced by cmd, the shell will terminate once cmd exits;


22. Give three conditions that cause switching from user mode to kernel mode.
> - When use system calls
> - Hardware interrupts
> - Exception or Faults

23. What is a system call? How does a system call differ from a call to a method or a function in a software library? What kinds of services can be obtained through system calls?

> - System calls are function in the kernel that are called by the user to execute kernel code
> - It is different because it have switch user mode from user to kernel and vice versa
> - Services:
>   - I/O
>   - Process control
>   - Memory management
>   - ...


25. A process that has been running is now waiting for the user input. What is among the 5 possible states, the state in which the process is when waiting for the user input?
> Ready state
