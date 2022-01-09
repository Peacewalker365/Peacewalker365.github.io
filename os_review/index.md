# OS Review

von Neumann Architecture (realization of Turing Machine)

### What are programs?

- Process – a running program

- To allow for running multiple processes, a running program must be able to be interrupted

- OS provides a virtualization of CPUs, so each program can assume they have their “own” virtual CPU at its disposal



### Memory Space of a Program

- A variable of a program corresponds to a location in memory

- Code address, e.g., function entrance addresses, corresponds to a location in memory 





### Concurrency

- Two scenarios of concurrency
  - Interleaving
  - Parallel execution

- Concurrency when happening on shared data
  - May lead to undefined result under thread semantics
  - Have to apply synchronization to ensure consistent semantics



### Three Easy Pieces

- Virtualization

- Concurrency

- Persistence





### How CPU virtualization can be realized

- **One Straightforward Approach**
  - OS emulates the real CPU
    - Each instruction is interpreted by the OS, as opposed to running on the bare metal.
    - OS has full control of a process, and can interrupt it at will.
    - OS can refuse to run an instruction that directly accesses I/O.
    - What is the drawback?
      - Very slow. Does not fully utilize the hardware’s capability
- **Direct Execution**
  - Let processes’ code run directly on CPU
    - How to preempt a running process?
      - Timer interrupt. Need to be frequent enough, e.g., every a few ms.
      - Interrupt handler is run upon an interrupt.
      - Interrupt is widely used to handle device I/O.
      - Exception can also interrupt a running process.
  - How to protect OS from the processes?
    - What about kernel code in memory?
  - How to protect processes from one another?
    - Kernel needs to do A LOT OF book keeping.

- Let’s introduce a few important concepts about processes

- Let’s look at some important UNIX process APIs





### What are the main problems to be solved?

- **How to preempt a process**

- How to protect shared resources, i.e., I/O    --I/O shall not be directly accessed by processes. **All access to I/O must be mediated by OS.**
  - The CPU must be able to distinguish two different modes: in one mode, the instructions running are those of the processes; in the other mode, the instructions running are those of the OS. We call the prior mode **user mode**, and the second mode **kernel mode**.
  - **Notion of OS Kernel:**
    -  An OS kernel consists of code that runs on behalf of the OS             
    -  N.B., this does not include OS library code
  - **In user mode, certain instructions are not permitted**. Examples?
    - If a process attempts to execute one such instruction, the CPU will **raise an exception** and the **process will be interrupted**
  - **In kernel mode, all instructions are allowed.**
  - When the **CPU starts up**, it is normally **in kernel mode**.
  - **Transition between the two modes.** 
    - from kernel to user -- simpler, since OS is in control
    - from user to kernel -- care must be taken to ensure protection

- How to protect OS from the processes?           -- wait until Virtual Memory

- How to protect processes from one another.  -- wait until Virtual Memory

- Performance & Efficiency
- Other considerations
  - Parallel execution



### Context Switches

- Always between user mode and kernel mode
  - Can a process directly switch to another process without going through kernel?
    - to do so the first process would need to access the other process's context information, and change the process's status to running
    - it will also need to switch to the other process's virtual memory.
    - It will be very hard to grant these privileges, without giving out too much.
- **Reasons for context switches/Cases of Trapping into Kernel**
  - **Hardware interrupt**
    - Timer
    - Device I/O
    - etc.
  - **Exception**
    - Illegal instruction
    - Non-permitted instruction
  - **System calls**
    - Requesting kernel to preform privileged operations on the process’s behalf



### Kernel vs. User Mode

1. **Kernel code** runs at **full privilege** (a.k.a. privileged execution)
   - **All instructions** are **available**
   - **All physical memory** is **available**
   - **Direct access to hardware I/O** is **available**
2. **Process code** runs at **restricted privilege**
   - **Certain instructions** are **not permitted** – we will encounter quite a few
   - **Direct access to physical memory is not possible** – more to be discussed later
   - **Direct access to hardware I/O is prohibited**
3. Don’t take it for granted!



### How context switches can be implemented

1. How to **trap into kernel**
   -  **hardware interrupt**   e.g., timer interrupt...
     - **Save the context of the process interrupted**
       - CPU saves PC value at the interrupt
       - CPU saves SR at the interrupt
       - CPU/OS saves GRs at the interrupt
     - **Transfer control to kernel**
       - CPU saves the current SP, find out the current process’s kernel stack through a special register, and changes SP to point to that kernel stack
       - CPU finds out the address of the IDT through a special register IDTR
       - CPU indexes into IDT, finds out the entrance address of the interrupt handler, and populates PC with the address
       - CPU changes from user mode to kernel mode
     - **Update book-keeping**
       - OS changes the interrupted process from ”running” to “ready” state
       - OS populates the relevant field in PCB regarding the interrupted process’s context info
2. **How to return from a trap** 
   - return from a special return instruction
3. **How to switch to a different process while returning from a trap**



basic notions:

- PC (program counter): CPU register pointing to the next instruction to fetch

- SP (stack pointer): CPU register pointing to the current top of stack
- GR (general registers): CPU general registers for storing temporary results
- SR (status register): Current CPU status, e.g., arithmetic operation results
- **A device notifies a CPU** for an interrupting event **through hard-wired signal**
  - The CPU can tell which device has sent an interrupt
  - Interrupts from different devices are assigned different numbers, i.e., interrupt no.
  - CPU checks whether there is interrupt waiting, before fetching the next instruction 



### Case Study: x86 Interrupt Mechanism

- Basic
  - PC (program counter): EIP
  - SP (stack pointer): ESP
  - GR (general registers): EAX, EBX, ...
  - SR (status register): EFLAGS
- x86 adopts segmentation in addressing, so each address also needs to have a segment 
  - For code, need to use the CS segment register 
    - Thus program counter is CS:EIP
  - For stack, need to use the SS segment register 
  - Thus stack pointer is SS:ESP 



### Resume execution of the interrupted process

- Revert the operating at the beginning of the interrupt
  - Restore the GRs to the value at interrupt
  - Restore SP/SR/PC to the value at interrupt
  - Change CPU mode from kernel to user
- Part of this is done through a “trap return” instruction
  - The semantics of the trap return instruction is the contract between the hardware and OS
  - The kernel can decide how to fulfill the rest 



### Resume execution of a different process

- Similar to before, but using another process’s context
- Also need to change CPU register pointing to the new process’s kernel stack
- OS needs to maintain each process’s context consistently, and be able to find the context when needed.



### Maintaining each process’s context

- It is extremely important that kernel maintains each process’s context consistently
  - The biggest part of an OS kernel’s job is book-keeping!
- Typically the kernel maintains a process control block (PCB) data structure for each active process.
  - The process’s current status
  - The process’s context to resume running
  - The process’s kernel stack (interrupt stack) location 



### Other ways of trapping into kernel

- Exception
  - Similar to interrupt, except that when exception happens, the **PC points to the offending instruction**, as opposed to the next instruction
  - The hardware may **inform CPU** the specific nature of the exception **through** an **error number**.
- System call
  - User process explicitly traps into kernel, by invoking a specific interrupt number through “software interrupt”
    - In x86: the “int N” instruction
    - **System call number** is **passed through** a **register**, in x86 it is %EAX
    - All **parameters of a system call** must be **copied into kernel** and **checked**



### How system call can be implemented



### Important UNIX process APIs

- Unix History
  - Developed by Dennis Ritchie and Ken Thompson at AT&T Bell Labs
  - Adapted some ideas from the Multics project in 1969
- Use man command to read manuals of commands and system calls
  - “man -a topic”: returns all entries on topic
  - Type “q” to exit an entry and move on to the next one
- UNIX Process APIs
  - fork()
    - Memory space, open files, environmental variables
  - wait()
  - exec()
- **Redirecting input/output**
  - An open file is assigned a numeric number as its “**file descriptor**”
  - 0: standard input
  - 1: standard output
  - 2: standard error
- UNIX **pipes**:
  - pipe() **returns a unidirectional message queue** between processes **for inter-process communication**
    - Returns two file descriptors, d0 and d1.
    - Bytes written on d1 will be read from d0 in the same order.
- Use the dup() system call to redirect standard input and output 





## CPU Scheduling

### Policy vs. Mechanisms

- We have introduced the mechanism of context switches
  - Before returning from kernel space to user space, OS needs to decide which user level process to resume

- The determination of the choice of which process to resume is the “Policy”



### Desired Properties of the Scheduling Policy

- What do you think are good outcomes (so we try to achieve them)

  **Fair: Each process occupies about the same amount of CPU time**

  This needs to be modulated through priority

  - **I/O intensive processes** shall have **higher priority**
  - How to achieve fairness when every process has the same priority?
    - Two different notions of time: **elapsed time** vs. **virtual time**
    - **OS** can keep **track** of **virtual time** of all processes
    - Scheduler always **pick**s the one with **the lowest virtual time** to run

- What do you think are bad outcomes (so we try to avoid them)

  - What if a new process joins later? – needs to be adjusted
    - Approach 1: initialize a new process’s virtual time to that of the minimum of the currently ready processes
  - What if a process was blocked in I/O, and is now ready to run. It will have a lower virtual time compared with the other ready processes.
    - Adversarial processes can potentially game the system to deprive other processes opportunity to make progress



### Goals of the Scheduling Policy

- Metrics on which the algorithm tries to optimize 
  - **Turnaround time**
    - Average interval length from task arrival till completion
  - **Response time (fairness)**
    - Average interval length from task arrival till “response”
    - For simplicity, we interpret “response” as the first time a task is scheduled
- The two metrics are trade-offs
  - **Optimizing on one will jeopardize the other**



### The FIFO policy



### Shortest Time-to-Completion First (STCF)



### Shortest Job First (SJF) –Historical Coverage



### Round Robin



### When a Process is Blocked by I/O

- It cannot be scheduled until it is woken up
  - What shall the scheduling policy be?
- If a process is more interactive with users, it will more often be blocked by I/O.



## Two Well Known Scheduling Algorithms


