# Operating System Compendium

## Storage devices

We have Register, Cache, Main Memory as the part of Primary Memory. Then we have Electronic Disk, Magnetic Disk, Optical Disk, Magnetic Tapes as the part of Secondary Memory.

Registers are used to store immediate data, operands and the instructions that the CPU is currently processing. if the CPU is adding two numbers, then the operand and the running result will sit in the registers. On the other hand, if the CPU needs to read a value that was recently read from RAM, like an array element from loop, it will likely be retrieved from cache.

___

## Process

Program is the compiled code ready for execution.

Process is the program under execution.

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

> Single CPU can never get true performance gains from multithreading because single CPU can only run one thread at a time. It cannot make real parallel execution happen

### Multithreading
Multithreading is the ability of the program or operating system to run multiple threads within a single process at the same time. Advantages:
- Efficient CPU utilization
- Faster execution of a task

___

## Process Synchronization
Process synchronization is the coordination of multiple processes to ensure they access shared resources safely and without conflicts.

### Critical Section
Critical Section referes to the segment of the code where process/threads access shared resources.

### Race Condition
Race condition occurs when two or more processes try to access or modify shared resource at the same time. Since their execution order is unpredictable, the final result can become incorrect or inconsistent.

### Solution to Race Condition
- Make the critical code section an atomic operation, i.e, executed in one CPU cycle.
- Make the processes mutually exclusive using locks, i.e, don't let different processes execute at the same time. Ensure that they execute sequentially. It fully blocks all other processors from a resource
- Semaphore is the simple locking tool that controls how many processes or threads can access a shared resource at the same time.

### Peterson's Solution
Peterson's solution is a software only method that ensures two threads take turns safely when accessing a shared resource.

### Mutexs or Locks 
Mutexs or Locks are tools that let only one thread or process enter a critical section at a time. It comes with several disadvantages:
- Contention: If one thread holds the lock, others wait. But if one thread holds the lock, gets crashed then others have to wait forever.
-  They are difficult to debug because timing issues are hard to reproduce.

___

## Conditional Variables
Conditional variable is a synchronization primitive that lets the thread wait until a certain condition occurs. Suppose we have 2 threads $T_1$ and $T_2$. Suppose $T_2$ is being executed and $T_1$ also comes, then it goes in the wait state based on the condition. When $T_2$ is finished executing, it will notify the condition that puts $T_1$ on wait. The lock will be released and T1 will start executing. In the meantime, the CPU can perform other work, thus avoiding busy waiting.

## Semaphore
Semaphore is a synchronization method. Suppose we have multiple instances of a resource and say we have 3 resources, and 10 threads $T_1 \rightarrow T_{10}$. We also have a variable named sema = 3. When $T_1$ takes one resource, then sema -= 1. The same happens for other threads $T_2, T_3$. When $T_1$ finishes executing, sema += 1, and then the next waiting thread will be allowed to execute.

___

## Producer-Consumer Problem
There are two types of threads:
- The Producer, which creates data and puts it into a shared buffer.
- The Consumer, which takes data out of that buffer

The buffer has a fixed number of slots, so it can become either fully or empty. Main problems that must be solved:
- Only one thread should access the buffer at a time.
- The producer must not insert data if the buffer is full.
- The consumer must not remove data if the buffer is empty.

To solve this, we have semaphores:
- A binary semaphore ensures that only one thread enters the critical section at a time.
- A counting semaphore `empty` tracks how many empty slots are left.
- Another counting semaphore `full` tracks how many filled slots exist

How the solution works:
- If the buffer is empty, the consumer must wait until the producer adds something.
- If the buffer is full, the producer must wait until the consumer removes something.
- Mutex ensures orderly access so both cannot modify the buffer at the same time.

___

## Reader-Writer Problem
This situation involves shared database. Many threads may want to read from it and many may want to write to it. The key idea of the problem is:
- Many readers can read at the same time.
- But only one writer should write at a time.
- And when a writer is writing, no reader should be allowed inside.

- `mutex`: lets only one thread update readcount at a time.
- `wrt`: ensures exclusive access to the database.
- `readcount`: number of active readers.

### Writer Logic
- Wait on `wrt`
- Write
- Signal `wrt` (only one writer can enter because `wrt` acts as the lock)

### Reader Logic
- Wait `mutex`, incremenet `readcount`
- if first reader $\rightarrow$ wait on `wrt` (block writers)
- Signal `mutex`
- Read
- Wait `mutex`, decrement `readcount`
- if last reader $\rightarrow$ signal `wrt`
- Signal `mutex`

___

## The Dining Philosopher
The dining philosopher problem models multiple competing process sharing limited resources in a circular arrangement. We can use semaphore to solve this. Make each fork as a semaphore (binary). `fork[5]{1}`. When a philosopher picks a fork, it calls a `wait()` on `fork[i]=1` and `fork[(i+1)%n]=1`, so other philosophers can't use it. Once it's done using that fork, it will call `release()`. Although the semaphore solution makes sure that no two neighbors are eating simultaneously
but it could still create Deadlock. Suppose that all 5 ph. Become hungry at the same time and each picks up their left fork, then all fork semaphores would be 0. Some solutions:

- Allow at most 4 philosopher to be sitting simultaneously. So P1 can pick both the forks, complete his work and when done, then P2 can complete his task.
- Allow a philospher, to pick up the fork only if both are available and make it atomic.
- Odd-even rule: Odd philosophers pick up first his left fork and then his right fork.

___

## Deadlock
Suppose a process requests a resource. If the resource is not available, the process enters a waiting state. Sometimes, the waiting state can be indefinitely long because the resource that was requested is busy sometimes foreverly.

### Deadlock Necessary Conditions
- Mutual Exclusion: Only 1 process can request a resource at a time, if another process requests that resource, then that process needs to enter in a wait state until the resource has been released.
- Hold & Wait: A process must hold at least one resource and must waiting for other resource, but that resource is held by other processes.
- No-preemption: A resource must be voluntarily released by the process after completion of execution.
- Circular Wait: A set ${P0, P1, ... , Pn}$ of waiting resources must exist such that P0 is waiting for a resource held by P1. P1 is waiting for a resource held by P2, and so on.

### Methods for handling Deadlocks
- Use a protocol to prevent or avoid deadlocks, ensuring that the system will never enter a deadlocked state.
- Allow the system to enter a deadlocked state, detect it, and recover.
- Ignore the problem altogether and pretend that deadlock never occur in system (Ostrich algorithm)

### Deadlock Prevention
- Mutual Exclusion: Use locks only for non-shareable resources.
- Hold & Wait: To ensure H&W condition never occurs in the system, we must guarantee that, whenever a process requests a resource, it doesn't hold any other resource,
  - Protocol (A) can be, each process has to request and be allocated all its resources before its execution.
  - Protocol (B) can be, only allow a process to request resources only when it has none.
- No preemption: If a process is holding a resource R1 and request a resource R2 and it is busy. The R1 should be released and the process must wait for R2 to be allocated to it. It can also give rise to live lock. Another thing can be done, suppose if P1 is requesting a resource R2, which is held by P2, which itself is waiting for R3, then in the meantime we can release R2 and allocate it to R1.
- Circular Wait: Suppose if P1 is having R1, and is waiting for R2. R2 is with P2, but P2 wants R1. Then we can implement a technique which involves ordering. Start with R1, both P1 and P2 try to acquire and whosoever acquires it first will go on acquiring R2 while the process having it unacquired, will wait for R1.

___

## Deadlock Avoidance
Kernel knows info about the resource. By this, system can decide for each request, whether the process should wait. To decide whether the current request can be satisfied or delayed, the system must consider the resources available, allocated and future requests.
- Schedule process and its resource allocation in such a way that the DL never occur.

### Safe State
A state is safe if a system can allocate resources to each process and still avoid DL. A system is in safe state if there exists a safe sequence.

### Unsafe State
In an unsafe state, a system cannot prevent the processes from requesting resources in such a way that deadlock occurs.

> The main idea of deadlock avoidance is, if a request is made for a resource, then it can only be allocated if the resulting state is a safe state.

### Deadlock Detection
If after making a wait for graph we detect a cycle, then there is a deadlock. If not, then not. (Valid for single Instance). For multiple instances, we can use banker alogrithm.


### Deadlock Recovery
- Process Termination: Abort all DL processes or Abort one process at a time until DL cycle is eliminated
- Resource preemption: We successively preempt some resources from processes and give these resources to other processes until DL cycle is broken
