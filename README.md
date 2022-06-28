# Operating Systems

An operating system (OS) is a software that usually are used to scale applications easily and 
typically replaces the **superloop** found in simple systems. The operating system ...

- manages the **hardware resources** such as the CPU, memory and I/O devices
- manages an application or group of **tasks** running on the computer



## 1. Classification
_______________________________________________________________________________

![](assets\OS-Classification.png)

The are several differentiation criteria used to classify the operating system. If we take the 
access to the CPU in cosideration, then the operating systems is be classified as ...

- A **single-task OS** that allows a single task to use the CPU
- A **multi-tasking OS** that allows the execution of multiple tasks on a single CPU

Further operating systems might be further differentiated based on the number of users such 
as ...

- A **single-user OS** allows only a single user to use the OS
- A **multi-user OS** allows multiple users to use the OS

And finally based on their use case, the operating systems might be divided in the following 
categories ...

- **General-purpose OS** that ensures the execution of all tasks without blocking
- **Real-time OS** that ensures the execution of high priority tasks within a time limit
- **Distributed OS** that ensures the distribution of task on physically separated CPUs
- ...


### Practice

1. Determine the 3 basic categories of the following operating systems

- **MS-DOS**
- **Windows 95** 
- **Windows 10**
- **Unix**
- **FreeRTOS** 

2. In your oppinion what is the best operating system?
3. Is there a multi-user multi-task RTOS?

## 2. Structure
_______________________________________________________________________________

![](assets\OS-Structure.png)

### 2.1. Hardware

The computer usually has a mother board hosting a variaty of chips such as the CPU, memory, the 
peripherial controllers and the chipset which manages the data flow between all of these components.

### 2.2. HAL

General purpose operating systems such as Linux or Windows are written in such a way that they 
work without knowledge of the underlying hardware. This is achieved by using a special chip 
called BIOS (Basic Input/Output System). It separates the interface, which is used by the OS to 
work with hardware and the concrete implementation itself. In this case each motherboard produced is 
obliged to implement some basic functionality and provide the same interface to the operating 
system.

UEFI, BSP?

### 2.3. Operating system

The operating system usually consists of a kernel and some drivers used to access the file 
system and other critical functionality required to start the OS. The kernel itself can have 
different types of architectures, such as ...

- monolithic
- microkernel
- ...

### 2.4. Middleware

The operating system itself offers basic memory management and task synchronization services along
with higher level drivers such as the USB host system, the file system, network stacks and others.

Depending on the Windows version, different software libraries and drivers might be provided. 
For example Windows Pro or Windows Server provide much more features as Windows Home. These 
features are separated into a dedicated layer called middleware. 

The middleware provides a standard interface of OS related features to the application. The 
concrete implementation depends on the version of the underlying OS and is very similar in 
concept to the HAL layer.

### 2.5. OSAL

The middleware can be easily extended by the user providing own interfaces and libraries. One 
very useful component as part of the middleware is **OS abstraction layer** or the **OSAL**. As 
its counterpart on the hardware level, it allow separation of the interface of the basic kernel 
services from its implementation. This increases the reusability of the programming application 
allwoing the same software to run on different operating systems. In the **UNIX** world it is 
also named **POSIX**.

### 2.6. Application

The application layer is the end layer of the software stack. The application itself might also 
implement functionality of the middleware too, such as drivers or custom implementation of the 
OSAL interface. 

### Practice

A
- Identify the main hardware components on a development board
- Compare the embedded hardwate to a standard PC
- Clarify how embedded systems differ from standard hardware

B
- Create a new netXStudio template project with an operating system
- Identify the main components and find the HAL section

- Create a new netXStudio template project with an operating system
- Identify the main components of the operating system

C
- Create a new netXStudio template project with an operating system
- Identify the main components of the middleware

D
- Create a new netXStudio template project with an operating system
- Find the OSAL interface and its implementation

E
- Create a new netXStudio template project with an operating system
- Write a simple application which uses the UART to print 'Hello world!'


## 3. Dataflow
_______________________________________________________________________________

![](assets\OS-Dataflow.png)

The task usually can call the drivers and libraries from the middleware or the OS. Additionally to 
increase the usability of the program the task has also access to the OSAL interface. 

The middleware itself might use also OSAL for better reusability and has access to basic drivers 
provided by the operating system.

The drivers and the kernal of the OS always access the hardware using the hardware abstraction 
layer. 

### Practice
- Demo with a logging function to show how the layers communicate with each other
- Learn how to trace the calls using debugging and profiling tools for a simple project

## 4. Task management
_______________________________________________________________________________

### 4.1. Task concept

A task is a simple program that runs as if it had the microprocessor all to itself.
Each owns a stack space to store temporary values and executes specific functions. Tasks 
have also a priority based on their importance. 

![](assets\OS-TaskModel.png)

Threads are tasks that share the same memory layout. Because of that any two threads can
access the same memory locations.

Processes are tasks with their own memory layout. Two processes cannot normally access the
same memory locations. Different processes typically have different access rights.

### 4.2. Task states

![](assets\OS-TaskStates.png)

The minimum set of states in typical task state model consists of the following states ...

1. **Running** (takes control of the CPU);
2. **Ready** (ready to be executed);
3. **Waiting** (blocked until an event occurrs, I/O for example).


### 4.3. Task scheduling

Schedulers determine which task to be executed at a given point of time and differ mainly in the 
way they distribute computation time between tasks in the READY state.

#### 4.3.1. Priority scheduling

***TODO: Picture showing round-robin scheduling in different contexts (terminate, ISR, etc...)***

With priority scheduling tasks are executed by their assigned prority. Usually higher numbers 
mean higher priority.

#### 4.3.2. Round-robin

***TODO: Picture showing round-robin scheduling in different contexts (terminate, ISR, etc...)***

With round-robin scheduling each task gets a certain amount of time or `time slices` to use
the CPU. After the predefined amount of time passes the scheduler deactivates the running
task and activates the next task that is in the READY state. This ensures that each task
gets some CPU time.

#### 4.3.3. First Come, First Served

***TODO: Picture showing scheduling in different contexts (terminate, ISR, etc...)***

With this type of algorithm tasks are executed in order of their arrival. It is the easiest
and simplest CPU scheduling algorithm.

### 4.4. Task switching

The scheduler activates the highest priority task that is ready-to-run. Scheduling occurres when...

- An interrupt sends a message to a task using the services of the kernel. Based on the priority
of the sending and the receiving tasks, the one with higher priority gets resumed

- A task sends a message to another task. Again, if the receiving task is with higher priority
than the sending task then the kernel will switch to the receiving task. Otherwise, the
sending task continues execution. 

- A task delays itself for a specified amount of time. In that case, the task is suspended
and the kernel selects and runs the next most important task taht is ready-to-run.

Task or context switching is the process of storing the state of a task, so that it can be 
restored and resume execution later, while another (more important) task gets activated. The 
state of the task is a special data structure called **Task Control Block** or short **TCB**.

In more details the scheduler:
1. Saves the processor registers on the current task's stack
2. Saves the stack pointer on the TCB of the current task.
3. Loads the stack pointer from the TCB of the new task.
4. Loads the processor registers from the values stored on the new task's stack.

#### 4.4.1. Cooperative switching

A cooperative task switch is performed by the task itself. The task suspends itself
for example by calling a blocking function.

Pure cooperative multi-tasking systems have the disadvantage of longer reaction times
when a higher priority task becomes ready for execution and are therefor rarely used in
real-time systems.

#### 4.4.2. Preemptive switching

A **preemptive task switch** may be caused externally. If a higher priority task becomes
ready, then the scheduler suspends the current task and executes the higher priority one. 
Preemptive multitasking **may be switched off** in sections of a program where task switches are 
prohibited, known as **critical regions**.

### Practice
- Write a scheduler with a priority switching
- Write a scheduler with round robin switching
- Write a scheduler with first-come-first-served scheduling

## 5. Memory management

- Explain heap, stack and other memory related definition
- Explain some important concepts such as memory initialization and NULL
- Explain the function of the linker
- Take a look at a program (for example .com, .exe or .elf)
- Explain how the program is loaded in to the memory



## 6. Interrupts

Interrupts can be software and hardware.

Hardware interrupts are interruptions of a program caused by hardware. When an interrupt occurres
the CPU saves its registers and executes an ISR (Interrupt service routine). After the 
ISR is completed the highest priority task which is ready to run executes.
ISRs are nestable - an ISR can execute within another ISR.

Software interrupts are caused by an exceptional condition or a special instruction
which causes an interrupt when executed.


Interrupt latency is the time between the interrupt occurres and the time when the
according ISR starts executing. The worst case interrupt latency is an important value
regarding a RTOS.

A *maskable* interrupt can be disabled or ignored by the instructions of the CPU.
*Maskable* interrupts can be handled after the current instruction executes.

Interrupts can have priorities. If two different interrupts occur at the same time
the one with the higher priority gets executed first. Additionaly an interrupt can
never be interrupted by an interrupt of lower or equal priority.

An ISR perserves all registers and must restore the environment completely.
Interrupt handlers must finish quickly.



## 7. Task synchronization
_______________________________________________________________________________

Kernels provide a variety of services for synchronizing tasks, communicating between tasks and 
handling events.

### 7.1 Semaphore

Semaphore is an integer variable which is used as a signal to allow a process to access the 
critical section of the code or certain other resources. A semaphore manages an internal counter 
which is decremented by each `acquire()` call and incremented by each `release()` call. The 
counter of the semaphore can never go below zero and when `acquire()` finds that it is zero, it 
blocks, waiting until some other task calls `release()`.

Upon releasing the semaphore the kernel determines the highest priority task waiting for the 
semaphore and passes it to the task. If the task releasing the semaphore is of higher priority 
than the task waiting for the semaphore, then the releasing task continues executing. Otherwise the 
releasing task is preempted and the kernel switches to the waiting task.

Depending on the counter type there are two types of semaphores:

1. **Binary** - used to access a single resource. It holds the value of 0 or 1,
signaling that the resource is occupied or available

2. **Counting** - A counting semaphore stores the number of free instances of said resource and
blocks until an instance becomes available.

 

### 7.2 Mutex

A mutex or the **mutual exclusion** service is a special type of synchronization mechanism which 
resembles the binary semaphore.It implements though some additional logic to solve a 
common problem of semaphores called **priority inversion**.

Priority inversions can occur for example in the following situation ... 

![Priority Inversion](assets/OS-PriorityInversion.png)

1. A Low Priority Task (LP Task) owns a semaphore for accessing a given resource
2. A High Prioriy Task (HP Task) waits for a resource currently owned by the LP Task
3. A Medium Priority Task (MP Task) becomes ready-to-run (after an event occurres or a delay passes)
and preempts the LP Task.
4. The MP Task completes execution.
5. The LP Task resumes
6. The LP Task finishes using the resouce and releases the semaphore
7. The HP Task acquires the semaphore and resumes

In this situation the priority of the HP Task is reduced to that of the LP Task
that it waits for to finish using a resource. Because of that the HP Task gets
unnecessarily delayed.

A mutex would elevate the priority of the LP task to that of the HP task. In this way the medium 
priority task will not be scheduled for execution while the mutex is acquired. This mechanism is 
also called **priority inheritance**.

Now the same situation as above will change to the following scenario ...

![img.png](assets/OS-PriorityInheritance.png)

1. A Low Priority Task (LP Task) owns a mutex for accessing a given resource
2. A High Prioriy Task (HP Task) waits for a resource currently owned by the LP Task
3. The priority of the LP task is elevated to that of the HP task
4. A Medium Priority Task (MP Task) becomes ready
5. The LP Task is temporary with higher priority and resumes
6. The LP Task finishes using the resource and releases the mutex
7. The HP Task acquires the mutex and resumes
8. The HP Task finishes using the resource and releases the mutex
9. The MP Task is scheduled for execution

### 7.3 Lock

A reader-writer lock allows simultaneous access for read-only operations while write operations 
require exclusive access.

Multiple tasks can read at the same time, but a writing task will block others from reading or 
writing. A readers-writer block can also be implemented using semaphores and mutexes.


### 7.4 Event



## 8. Task communication
_______________________________________________________________________________


### 8.1 Mailbox

- A mailbox is a **message buffer** managed by the RTOS.
- The messages have **fixed data size** and are usually small.
- Mailboxes work as **FIFO** (first in, first out)
- Tasks can **send and retrieve** messages to/from the mailbox
- If the **mailbox is empty the reading task be blocked** for a specified amount of time or 
  until a message arrives.
- When a message arrives the **kernel notifies the waiting task** and the scheduler determines 
  if a task switching must be done, according to the priority of the running task and the task 
  waiting for a message

### 8.2 Queues

- Queues are **message buffers**
- Queues accept **messages of different lengths**.
- The **message size must be passed as a parameter** along with the message.
- Tasks can **send and retrieve** messages to/from the queue
- If the **queue is empty the reading task be blocked** for a specified amount of time or 
  until a message arrives.
- When a message arrives the **kernel notifies the waiting task** and the scheduler determines 
  if a task switching must be done, according to the priority of the running task and the task 
  waiting for a message