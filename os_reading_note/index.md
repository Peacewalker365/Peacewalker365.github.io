# OS

# OS_notes_for book_reading

## Virtual Memory

### Paging

Segmentation leads to fragmentation so that we need the new idea of paging.

Pages are memory chunks with fixed size, we can view physical memory as an array of fixed-sized slots called page frames.

Paging gives us **flexibility** so that we don't need to whether the way a process uses to address space will cause external fragmentation.

Paging also gives us the **simplicity** of free-space management that paging affords.

The OS keeps a **free list** of all free pages.

### Page Table

The major role of page table is to store **address translations** for each of the virtual pages of the address space, thus letting us know where in physical memory each page resides.

It's important to know that page table is a **per-process** data structure. An exception of this is the **inverted page table**.

#### Notice

Notice that two same virtual addresses in different processes may lead to a same physical frame.

#### Translation

VA --> VPN(Virtual Address Number) + offset

For example, the page size is 16 bytes in a 64-byte address space.
Then we need 4 pages, the virtual page number will be indicated by the first 2-bit.

VPN --> PFN(Physical Frame Number)
Physical Address = VFN + offset

#### Where are page tables stored?

Page tables can get terribly large, much larger than the small segment table or base/bounds pair.
For example, for a typical 32-bit address space, with 4KB pages. This VA splits into 20-bit VPN and 12-bit offset. 20 bits implies 2^20 translations that the OS would have to manage for each process.
Assuming we need 4 bytes per PTE to hold the PFN + control bits, then we need an immense 4MB of memory for each page table. And this is only for one process.

#### What's in the page table?

The simplest one is the **linear page table**, which is just an array.
The OS indexes the array by the virtual page number, and looks up the page-table entry at that VPN to find the desired PFN.

We also have control bits such as:  

**Valid bit** ==> indicating whether the particular translation is valid. All unused space will be marked invalid.

**Protection bit** ==> indicating whether the page can be read, written, or executed.

**Present bit** ==> indicating whether this page is in physical memory or on disk.

**Dirty bit** ==> indicating whether the page has been modified

**Reference bit**(accessed bit) ==> track whether a page has been accessed, and this is useful to tell which page is popular so that we should keep in memory.

#### Problem: paging is so slow

To fetch the desired data:
1. fetch the proper page table entry from the process's page table.
2. Perform the translation.
3. Load the data from ther physical memory.

To do so, the hardware must know where to find the page table of the current running process. Thus we need a **page-table base register** holding the location.

However, implementing paging without care will lead to a slower machine as well as memory waste.

### TLBs

#### Translation-lookaside Buffer(TLB)

A TLB is a part of memory-management unit(MMU), and is a hardware cache.

#### TLB Basic Algorithm

Extract the VPN from the VA and check if the TLB holds it.

If yes, then we have a **TLB hit**. We can now extract the PFN from the relevant TLB entry, concatenate that onto the offset from the original VA, and form the PA we want. If protection checks do not fail, then we can perform the operation correctly.

If no, then we have a **TLB miss**. Then we have to walk the page table, the memory, or even copy the data from disk. And then update the TLB.

Note that page fault can be triggered by both TLB hit and TLB miss.

The TLB hit rate will greatly affect the performance, so we need to find the sweet spot for the TLB size. Also, if the TLB size is too large, walk the TLB will consume more time. And also, if you want a fast cache, it has to be small. This is the physical limit.

TLB also improves the performance due to **spatial locality** even if it's the first time to visit that memory space.

#### Who handle the TLB miss?

Could be hardware or software.

Hardware: the hardware has to know where exactly the page tables are located in memory(**page-table base register**), as well as the format. On a miss, the hardware will walk through the page table, find the right PTE and extract the desired translation, update the TLB with the translation, and retry the instruction.

For example, the Intel x86 architecture uses a fixed multi-level page table, and the current table is pointed to by the CR3 register.

Software: More modern architecture such as RISC, has a software-managed TLB. On a TLB miss, the hardware will raise an exception to pause the current instruction stream. Then it traps into kernel mode and jumps to a trap handler dealing with TLB misses. The code will look up the page table and update the TLB and return from the trap. The hardware will retry the instruction.

Notice that the return-from-trap behaves differently from that in a system call. In the system call case, it should resume execution at the instruction after the trap into the OS. However, in this case, the hardware must resume execution at the instruction that cause the trap and retry it so that it will has a TLB hit this time. We need be aware of the OS need to save different PC in these two cases.

Also, when running the TLB miss-handling code, the OS need to be extra careful not to cause an infinite chain of TLB misses to occur. The solution would be to keep TLB miss handlers in physical memory that are unmapped and not subject to address translation. Or reserve some entries in the TLB for permanently-valid translations and use some of those permanent translation slots for the handler code itself, these wired translations always hit in the TLB.

#### Aside: TLB valid bit != page table valid bit

TLB valid bit indicates whether a TLB entry has a valid translation in it.

The page table valid bit indicates the page has not been allocated by the process, and should not be accessed by a correctly working program.

The TLB valid bit is quite useful when performing a context switch too. By setting all TLB entries to invalid, the system can ensure that the about-to-run process does not accidentally use a virtual-to-physical translation from a previous process.

#### IMPORTANT: 

The dirty bit in PTE indicates where the PTE is modified rather than whether the memory location is modified, which is indicated by the dirty bit of that memory location.

Based on this fact, we can know that the content of PTE in TLB does not need to be the same as the one in the page table since whether the memory is modified is indicated by the memory itself rather than the PTE.

In addition, TLB PTE doesn't need to be the as same as the one in the page table because the OS can keep just TLB updated rather than both. Think of about this, if we have the PTE in TLB, when we want to go to that PA, we always get a TLB hit and will never walk the page table.

The additional work would be to update the PTE in the page table when the PTE in the TLB is evicted.

##### My QUESTION here is whether the same TLB is shared by processes or even cores?

#### What's in the TLB?

VPN | PFN | other bits

A typical TLB might have 32, 64, 128 entries and be **fully associative**, which means any given translation can be anywhere in the TLB, and that the hardware will search the entire TLB in parallel to find the desired translation.

#### TLB Issue: Context Switches

The virtual-to-physical translations are not meaningful for other processes.

***How do we manage TLB contents on a context switch?**
  1. Flush the TLB on context Switches. But the cost is that each time a process runs, it must incur TLB misses as it touches its data and code pages. The cost will be high, if the OS Switches between processes frequently.
  2. Some hardware provide ASID(address space identifier) field in the TLB, which usually has fewer bits than PID. The ASID is used to distinguish/mark PTEs from different processes.

#### Issue: Replacement Policy

We have to consider cache replacement. Specifically, when we are installing a new entry in the TLB, which one should we replace?

##### How to design TLB replacement policy?

1. LRU(least-recently-used)
2. Random policy --> avoid corner-case behaviors + simplicity

#### A Real TLB Entry

![MISP TLB Entry.png](img/MISP TLB Entry.png)

G is the global bit indicating whether the page is global shared among processes. Thus if global bit is set, the ASID is ignored.

C is the coherence bits indicating how a page is cached by the hardware.

D is the dirty bit indicating whether the page has been written to. (notice that this is the Intel's way of implementation.)

V is the valid bit telling the hardware if the there is a valid translation present in the entry.


##### RAM isn't always RAM

If the number of pages you want to access exceeds the TLB coverage, it can lead to severe performance issue. **Culler's Law** TLB as the source of many performance problems.

TLB actually brings many problems. Check the book for details.

### Smaller Tables

#### Page tables are too big

Linear page tables are pretty big. Assuming a 32-bit address space, with 4KB pages and a 4-byte page-table size. There are roughly one million virtual pages in it, which will occupy 4MB memory.

#### Simple Solution: Bigger Pages

##### Side note: many archs now support multiple page sizes.

Bigger pages may cause **internal fragmentation**.

#### Hybrid Approach: Paging and Segments

Most of the page table is unused, full of invalid entries.

Instead of havin a single page table for the entris, why not one per logical segment.

Base tells us the address of each segment. It holds the pysical address of the page table of that segment.

Bound tells us the size of each segment.

Thus the VA looks like:
Seg | VPN | Offset

The hardware use segment bit(SN) to determine which base and bound pair to use.

#### IDEA: When you have two good and seemly opposite ideas, you should always see if you can combine them into hybrid that manage to achieve the best of both worlds.

So if the code segment just have three entries, then the bound will be set to 3. This greatly reduces the waste of space for page tables.

However, the problem is that if we have a large but sparsely-used heap. This will cause the **external fragmentation** again.

#### Multi-level Page Tables

It turns the linear page table like a tree.
We use **page directory** to find the valid page tables.

![Multi-level Page Tables](img/Multi-level Page Tables.png)

The page directory, in a simple two-level table, contains of a number of **PDE**(page directory entry).

A PDE has a valid bit and a PFN, similar to PTE. However, the valid bit in PDE indcates whether at least one of the PTEs on the page pointed to by this PDE is valid. If the PDE is not valid, then the rest of the PDE is not defined. (The valid bit is the first bit in PDE)

##### Advantage:

1. Better supports sparse address spaces.
2. If carefully constructed, each portion of the page table fits neatly within a page, so it's easier to manage memory to avoid internal fragmentation.

##### Disadvantage:

If we have a TLB miss, two loads from memory will be required to get the right translation, one for the page directory and one for the PTE itself.

So there is a **time-space trade-off**. Smaller cache is faster but not for free. The TLB miss suffers from higher cost with this smaller table.

![PDE](img/PDE.png)

![Page Directory and Page of PT](img/Page Directory and Page of PT.png)

#### More Than Two Levels

![More than two levels](img/More than two levels.png)

#### IMPORTANT: The PDT's PA will be stored in a register, but it does not mean that the addr of PDT has to be in a fixed PA, it just has to be memorized.

#### Inverted Page Tables

**Inverted page tables** are even more space saving.

We keep a single page table that has an entry for each physical page of the system. The entry tells use which prcess is using this page, and which virtual page of that process maps to this physical page.

### Swapping: Mechanisms

#### Swap space

Firstly, we need to reserve some space on the disk for moving pages back and forth.

Then the OS need to remember the disk address of a given page.

The size of the swap space is important since it determines the maximum number of memory pages that can be in use by a system in a ginven time.

![Physical Memory and Swap Space](img/Physical Memory and Swap Space.png)

#### The Present Bit

The hardware first extract the VPN from the VA, the check the TLB for a match. If a miss, the hardware locates the PDT and corresponding page tables and look up the PTE, and the present bit on the PTE is 0, it means that the page is not in physical memory, which is commonly referred to as a **page fault**.

Nowadays not only swapping will trigger page fault but also illegal memory accesses and so on. It becomes a more general term for "page missing".

#### The Page Fault

Upon a page fault, the OS invokes **page-fault handler** to deal with the issue, no matter for a hardware or software managed TLBs.

When a page fault happens, the OS needs to swap the page into memory in order to service the page fault.

When the disk I/O completes, the OS will then update the page table to mark the page as present, update the PFN of the PTE, and retry the instruction.

Notice that this next attampt still may generate a TLB miss, which would the be serviced and update the TLB with the translation.(The other approach is to update the TLB when handling the page fault.)

Finally, a last attampt will get a TLB hit.

Note that while the I/O is in flight, the process will be blocked, and other processes will execute.

##### How will the OS know where to find the desired page?

The OS could use the bits in the PTE normally used for data such as the PFN of the page for a disk address. When the OS receives a page fault for a page, it looks in the PTE to find the address, and issues the request to disk to fetch the page into memory.

##### Why hardware doesn't handle page faults?

1. Page fault to disk is slow, even if the OS takes a long time to handle it, there is not to much of a difference.
2. To deal with the page fault, the hardware need to know the swap space, the I/Os to disk, and a lot of other details. 

#### What if memory is full?

The OS may lie to first page out one or more pages to make room for the new pages. The process of picking a page to kick out, or replace is known as the **page-replacement policy**.

#### Page Fault Control Flow

If the page was both present and valid, the TLB miss handler will just grab the PFN from the PTE into TLB and try again.

If the page was not present but valid, then the page fault handler will run.

If the page is not valid, in this case, no other bits matter. The hardware traps this invalid access, and the OS trap handler runs, likely terminating the offending process.

#### When Replacement Really occur

PLACEHOLDER

### Swapping Policies

#### Cache management

#### The Optimal Replacement Policy

#### FIFO

#### Random

#### LRU

#### Approximating LRU

The key is the use bit and clock algorithm.

#### Thrashing





## Concurrency

### Intro

Each thread is very much like a separate process, except for one difference: they share the same address space and thus can access the same data.

Context switch between threads are similar to that between processes. We save the PC, registers, state and so on in TCB. The difference is that the address space remains the same.

#### why thread?

1. Parallelism
2. To avoid blocking program progress due to slow I/O

#### Thread Creation

After the threads are created, the OS scheduler decides who runs the next.

#### Why It Gets Worse: Shared Data

##### [recall the concept if the Hazard]

- Data Hazards

  - RAW

    ```
    i1. R2 <- R5 + R3
    i2. R4 <- R2 + R3
    ```

  - WAR

    ```
    i1. R4 <- R1 + R5
    i2. R5 <- R1 + R2
    ```

  - WAW

      ```
      i1. R2 <- R4 + R7
      i2. R2 <- R1 + R3
      ```
    

- Structural Hazards

- Control Hazards



// compile with -g will include symbol info in the program. Then `prompt > objdump -d main will give you the assembly code neatly labeled.

// You may want to master debugger gdb, memory profilers valgrind or purify, and the compiler itself.

#### Core Problem: Uncontrolled Scheduling

We may have a race condition, or so to speak, a data race.

An**indeterminate**programconsistsofoneormoreraceconditions; the output of the program varies from run to run, depending on which threads ran when. The outcome is thus not **deterministic**, something we usually expect from computer systems.

A critical section is a piece of code that accesses a shared variable (or more generally, a shared resource) and must not be concurrently executed by more than one thread.

What we really want for this code is what we call **mutual exclusion**. This property guarantees that if one thread is executing within the critical section, the others will be prevented from doing so.

#### One More Problem: Waiting For Another



### Interlude: Thread API

#### Thread Creation

```c
#include <pthread.h>
int pthread_create(pthread_t* thread, const pthread_attr_t * attr, void* (*start_routine) (void*), void *arg);

/*

The second argument, attr, is used to specify any attributes this thread might have. Some examples include setting the stack size or perhaps in- formation about the scheduling priority of the thread. An attribute is initialized with a separate call to pthread attr init(); see the man- ual page for details. However, in most cases, the defaults will be fine; in this case, we will simply pass the value NULL in.
The third argument is the most complex, but is really just asking: which function should this thread start running in? In C, we call this a function pointer, and this one tells us the following is expected: a function name (start routine), which is passed a single argument of type void * (as indicated in the parentheses after start routine), and which returns a value of type void * (i.e., a void pointer).
Finally, the fourth argument, arg, is exactly the argument to be passed to the function where the thread begins execution.
*/

/*
having a void pointer as an argument to the function start routine allows us to pass in any type of argument; having it as a return value allows the thread to return any type of result.
*/
```

```c
#include <stdio.h>
#include <pthread.h>

typedef struct {
  int a;
  int b;
} myarg_t;

void *mythread(void *arg) {
  myarg_t *args = (myarg_t*) arg;
  printf("%d %d\n", args->a, args->b);
  return NULL;
} 

int main(int argc, char* argv[]){
  pthread_t p;
  myarg_t args = { 10, 20 };
  int rc = pthread_create(&p, NULL, mythread, &args);
}
```



#### Thread Completion

```c
// What if you want to wait for a thread to complete?
int pthread_join(pthread_t thread, void **value_ptr);

/*
This routine takes two arguments. The first is of type pthread t, and is used to specify which thread to wait for. This variable is initialized by the thread creation routine (when you pass a pointer to it as an argument to pthread create()); if you keep it around, you can use it to wait for that thread to terminate.
The second argument is a pointer to the return value you expect to get back. Because the routine can return anything, it is defined to return a pointer to void; because the pthread join() routine changes the value of the passed in argument, you need to pass in a pointer to that value, not just the value itself.
*/
```

```c
typedef struct { int a; int b; } myarg_t;
typedef struct { int x; int y; } myret_t;

void *mythread(void *arg) {
  myret_t *rvals = Malloc(sizeof(myret_t));
	rvals->x = 1;
	rvals->y = 2;
	return (void *) rvals;
}

int main(int argc, char *argv[]) {
pthread_t p;
myret_t *rvals;
myarg_t args = { 10, 20 };
Pthread_create(&p, NULL, mythread, &args);
Pthread_join(p, (void **) &rvals);
printf("returned %d %d\n", rvals->x, rvals->y);
free(rvals);
return 0;
}
```



1. If we just create a thread with no args, we can pass `NULL` in as an arg.
2. Similarly, we can pass `NULL` into `pthread_join()` if we care nothing about the return value.
3. If we are just passing in a single value, then we don't need to package it up as a arg.
4. We should be extremely careful with how values are returned from a thread. Never return a pointer which refers to something allocated on the thread's call stack.
5. We have a `pthread_create()` followed by`pthread_join()`. We can use **procedure call** instead.

#### Locks

The routines should be easy to understand and use. When you have a region of code that is a **critical section**, and thus needs to be protected to ensure correct operation, locks are quite useful. 

`int pthread_mutex_lock(pthread_mutex_t *mutex)`

`int pthread_mutex_unlock(pthread_mutex_t *mutex)`

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER; // we must do the init for locks
// or we can do as
int rc = pthread_mutex_init(&lock, NULL);
assert(rc == 0); // always check success!


pthread_mutex_lock(&lock);
x = x + 1; // or whatever your critical section is
pthread_mutex_unlock(&lock);
```

Also, `pthread mutex destroy() ` should be called afterwards.

There is a problem in the code above, which is that we didn't check the errors. If it fails, it fails silently.

The lock and unlock routines are not the only routines within the

pthreads library to interact with locks. Two other routines of interest:

```c
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_timedlock(pthread_mutex_t *mutex, struct timespec *abs_timeout);
```

These two calls are used in lock acquisition. The trylock version returns failure if the lock is already held; the timedlock version of acquiring a lock returns after a timeout or after acquiring the lock, whichever happens first. Thus, the timedlock with a timeout of zero degenerates to the trylock case. Both of these versions should generally be avoided; however, there are a few cases where avoiding getting stuck (perhaps indefinitely) in a lock acquisition routine can be useful.



#### Condition Variables

```c
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
int pthread_cond_signal(pthread_cond_t *cond);

/*
The first routine, pthread cond wait(), puts the calling thread to sleep, and thus waits for some other thread to signal it, usually when something in the program has changed that the now-sleeping thread might care about.
*/
```

#### Compile and Run

All of the code examples in this chapter are relatively easy to get up and running. To compile them, you must include the header pthread.h in your code. On the link line, you must also explicitly link with the pthreads library, by adding the -pthread flag.

For example, to compile a simple multi-threaded program, all you have to do is the following:

```powershell
gcc -o main main.c -Wall -pthread
```

#### Summary

- Keep it simple
- Minimize thread interactions
- Initialize locks and conditon variables
- Check you return codes
- Be careful with how you pass arguments to, and return values from, threads
- Each thread has its own stack
- Always use condition variables to signal beween threads
- Use the manual pages



### Locks

#### The Basic Idea


