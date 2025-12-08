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
OS keep tracks of the process using a table like data structure and each entry in the table is process control block (PCB).
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

### Zombie process
Zombie process is the process whose execution has been terminated but it still has an entry in the process table. Once the parent process reads the exit status of the child process, then the zombie process is eliminated from the process table. This is known as reaping the zombie process.

___

### Non-Preemptive Scheduling
Once CPU has been allocated to a process, the process doesn't leave the CPU until the process is terminated or switched to wait state. It has high starvation and less CPU utilization.

### Preemptive scheduling
CPU can be taken away from the process, once the process's time quantum expires along with terminating or switching to wait state. It has less starvation and high CPU utilization.

### Goals of CPU Scheduling
- Min Turnaround Time: It is the time elapsed between the process getting into the ready queue and after it's completion, getting terminated.
- Min Wait Time
- Max CPU Utilization
- Min Response Time
- Max Throughput of the System

#### Throughput
It is the no processes getting completed per unit time.

#### Arrival Time
It is the time taken by the process to arrive at the ready queue.

#### Burst Time
It is the time taken by the process for its execution

#### Response Time
It is the time duration between the process getting into ready queue and getting a CPU for the first time

#### Wait Time
It is the time elapsed by the process waiting for CPU

### Convoy Effect
Whichever process comes first in the ready queue, will be given CPU first. As a result, if one process has a high execution time (Burst time), then it will block other process waiting in the ready queue who have a shorter burst time. This situation where many processes need CPU for shorter time are blocked by one process holding the CPu for a long time is called **Convoy Effect**. It causes poor resource management.

____

## CPU Scheduling

### Shortest Job First (SJF) – Non-preemptive
This scheduling gives the CPU the process with the least Burst Time. To do this, the system must estimate the burst time of all the process in the ready queue. Convoy effect can happen here

### Shortest Job First (SJF) – Preemptive
This scheduling gives the CPU the process with the least Burst Time. CPU can be taken away from the process, once the process's time quantum expires along with terminating or switching to wait state. When we run short jobs before long ones, the short jobs wait much less, and the extra waiting time added to the long job is small Overall, this reduces the average waiting time for all processes.

### Priority Scheduling [Non-Preemptive]
Priority is assigned to a process when it is created.

### Priority Scheduling [Preemptive]
If the CPU is running a process and a new process arrives with a higher priority, then the CPU will stop the current one and execute the one with higher priority. This can create a problem called starvation. Low-priority processes might wait for a very long time and may even never get a chance to run. 
Solution is Ageing. In Ageing, if a process has been waiting for a long time, the system slowly increases its priority.

### Round Robin
It is a CPU Scheduling algorithm that gives each process a fixed time slice and cycles through all process in order. It ensures that no process holds the CPU for too long.

____

### Multi-level scheduling
We have 3 kinds of queue with the first having the highest priority; System process queue, Interactive process queue, Batch process queue. Each queue has its own scheduling algorithm. Scheduling among different sub-queues is implemented using fixed priority preemptive schedulinh. If an interactive process is being executed, then the batch process will be preempted. There is starvation for lower priority queues. Convoy effect is also present.

### Multi-level feedback queue scheduling (MLFQ)
In this, we have multiple sub-queues. In this type of scheduling, the movement of processes between different queues is permitted. The main idea is to put the processes having high burst time into lower queues (lower priorities) and put the input bound and interactive process in higher priority queue. 
To solve the problem of low priority process waiting indefinitely is solved by Ageing. There is less starvation and is flexible

____

## Concurrency
It is the execution of the multiple instruction sequences at the same time. It happens in the OS when there are several process threads running parallel.

## Thread
A thread is the lightweight execution unit that contains its own sequence of instructions

### Thread Scheduling
Threads are scheduled for execution based on their priority. The OS gives each thread a small amount of CPU time, called time slice

### Thread Context Switching
Thread context switching means the operating system pauses the execution of one thread and starts the execution of another thread. In doing so, it saves current thread's current state like program counter, stack etc., so it can continue later on from that point. Memory address is not switched as all the threads in a process shares the same memory address. It is faster than process switching. Since new thread is working in the same program, so cached data is still relevant.

### How Each Thread Get Access to a CPU ?
The OS decides which thread should be executed by the CPU based on the thread scheduling algorithm. When a thread is scheduled to run, CPU retrieves the instruction indicated by that thread's program counter and begin executing it. Each thread has a program counter, that tells the CPU what instruction that thread should execute next.

### I/O and Thread Control Block
Context switching in threads an occur for the same reasons as processes, such as when the thread is waiting for I/O or when its time quantime expires. A Thread Control Block (TCB) is used the thread's state during context switching just like PCB.

> Single CPu can never get true performance gains from multithreading because single CPU can only run one thread at a time. It cannot make real parallel execution happen

### Multithreading
Multithreading is the ability of the program or operating system to run multiple threads within a single process at the same time. Advantages:
- Efficient CPU utilization
- Faster execution of a task

