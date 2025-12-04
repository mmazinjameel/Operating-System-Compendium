# Operating System Compendium

## Storage devices

We have Register, Cache, Main Memory as the part of Primary Memory. Then we have Electronic Disk, Magnetic Disk, Optical Disk, Magnetic Tapes as the part of Secondary Memory.

Registers are used to store immediate data, operands and the instructions that the CPU is currently processing. if the CPU is adding two numbers, then the operand and the running result will sit in the registers. On the other hand, if the CPU needs to read a value that was recently read from RAM, like an array element from loop, it will likely be retrieved from cache.

___

## Process

Program is the compiled code ready for execution
Process is the program under execution

How OS creates a process?
- OS loads the program and static data into memory
- Allocates runtime stack to manage function calls and local variables
- Heap memory allocation to store dynamically created data during the program's execution
- I/O tasks: OS sets up the process's input/output resources such as opening standard input, output and error streams and preparing any file descriptors the process will need to interact with during execution.


### Attributes of a Process
OS keep tracks of the proce ss using a table like data structure and each entry in the table is process control block (PCB).
PCB has process ID, PC, etc. When the process is running and it's time slice is expired, then its current CPU register values are saved into the PCB. When it is scheduled to run, these values are restored from the PCB back into the CPU registers and the program continues executing.

___

## Process States

- New: The process is created by the OS
- Ready: The process is in memory and is waiting to be assigned to a processor
- Run: Instructions are executed, CPU is allocated
- Waiting: Waiting for I/O
- Terminated: The process finishes execution. PCB entry removed from the table

### Process Queues
#### Job Queue

Job Queue contains processes in the new state and in secondary memory. It has a Job Scheduler (Long-Term Scheduler) that selects processes from the job pool and puts it into the ready queue. 
**The LTS's main purpose is to control the degree of multiprogramming by deciding which jobs are allowed to enter the system for execution.**

#### Ready Queue

Ready queue contains the processes that are in the ready state and in main memory. It has the CPU scheduler (short-term scheduler) that picks a process based on the scheduling policy and dispatches it to the CPU for execution.

> Degree of Multiprogramming: It is the number of processes loaded in the main memory at a time

____

### Swapping
Swapping is a mechanism in which a process can be temporarily moved out from main memory into secondary memory (swap space) to make memory available for other processes. Later, it can be swapped back into main memory, and this swapping is done by the medium-term scheduler (MTS).

### Context Switching
Context switching is a mechanism in which the CPU switches to another process, and before it switches, the kernel saves the state of the current process in the PCB, so later when it runs again, the state is restored.

### Orphan Process
An orphan process is a process whose parent has terminated while it is still running. If a parent process starts a child process and then the parent crashes or exits before the child finishes, that child becomes an orphan. The operating system reassigns it to the init process so it can continue running normally.
