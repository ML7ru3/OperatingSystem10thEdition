# Chapter 1: Introduction
## Objectives
- To describe the basic organization of computer systems

## What is an Operating System?
- A program that acts as an intermediary between a user of a computer and the computer hardware
- **Goals**:
    - Execute user programs and make user problems easier
    - Make the computer system convenient to use
    - Use the computer hardware in an efficient manner

- Definition (OS is a...):
    - Resource allocator (manage all resources)
    - Control program (prevent errors and improper use)

> **Kernel of the operating system:** The one program running at all times on the computer

> Difference between OS and kernel: The kernel is a program (object) that is part of the OS


## Computer System Structure
4 component:
- Hardware (CPU, memory, I/O devices,...)
- Operating system
- Application programs 
- Users 

## Computer System Operation
- I/O devices and the CPU can execute concurrently
- Each device controller has a local buffer 
> Buffer: a small memory available temporary for copy something to something, eg: ram to CPU registers
- The CPU is controlled by OS

## Computer Startup
- **Bootstrap program:** is loaded at power-up reboot
    - Stored in ROM or EPROM, generally known as **firmware**
    - Initialized all aspects of system
    - Loads OS kernel and starts execution

- With services that are outside of the kernel, by the **system programs**, they are loaded into memory at boot time to become **system process** *(system daemons)* 
> When you run a program, it becomes a process (the program will load into the memory then becomes a proces )


>  On UNIX, the first system process is "init" and it starts many other daemons

## Interrupt
- An OS is interrupt driven
### Interrupt timeline
![alt text](./assets/Screenshot%202025-04-17%20012724.png "Interrupt timeline")

## Storage Structure

- Main memory: RAM
- Second storage:
    - Hard disks
    - SSD (Solid-state disks) - faster than hard disks, nonvolatile
> Nonvolatile: electricity <=> data

## Storage Device Hierarchy
![alt text](/assets/Screenshot%202025-04-17%20013341.png "Storage Device Hierarchy (storage capacity and access time)")

# Chapter 2: Oprating System Structure

- One set of OS services provides functions that are helpful to the user:
    - **UI:** Almost all OS have a user interface, varies between **CLI** (Command Line), **GUI** (Graphical User Interface), **Batch**
    - **Program execution:** The system must be able to load a program into memoty and to run that program, end execution
    - **I/O oprations:** A running program may require I/O
## System Calls
- Programming interface to the services provided by the OS.
- Typically in a high-level language
- Mostly accessed by a programs via a high-level API

> They are functions in the kernel that are called by a user program to execute kernel code

![](/assets/Screenshot%202025-04-17%20014423.png "Example of system calls")


## User mode / kernel mode

### Kernel mode
- Code in the kernel is the most trusted part of a computer system

- Have highest privileges in the whole system

- When a user program want to execute such opreations, it must ask the kernel to perform it on its behalf

### Transition from user to kernel mode 
- To obtain services from the OS, a user program must make a **System Call**
- The system call executes a "trap instruction" which causes a change in the value of the **mode bit**









