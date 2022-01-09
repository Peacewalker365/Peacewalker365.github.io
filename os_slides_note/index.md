# OS 2

#### Memory Sharing
- can we share memory between processors?
  - some page table entries need to be mapped to the same physical frames
  - One can read, other can write?: yes, control bits can be diff
  - Does the shared mem must be the same VA in the two processes?: no
  - How can we know a shared mem can be reclaimed?: have a counter like a ref bit

#### Copy on Write
- UNIX fork with copy on write 
  1. copy the parent page table entries to the child, and turn on a special bit called copy-on-write bit on both parent and child's PTEs
  Notice this is a OS bit (unused bit modified by the OS), hardware don't know what it is!
  2. read is fine, but if anyone want to write... We need the help from the hardware
  which means we need to turn on the read-only bit on hardware.
  When a write happens on either process, exception happens due to read-only bit. Kernel handler will find out the exception is due to copy on write.
  3. Kernel then allocate a new physical frame, and copy the content from the current shared frame to the new frame.
  The offending PTE(whichever need to write) will point to this new frame.
  So now the shared mem is not shared.
  4. Clear copy-on-write bit on the offending PTE, decrease the ref bit for that shared frame, and if the is no body else sharing that shared mem(ref bit is 0), then also clear the other PTE's copy-on-write bit.
  We here also need a reverse pointer pointing to the PTE who points to the shared mem.
  if ref bit is now 0, then follow reverse pointer to clear copy-on-write and read-only bit on the last process.
  copy-on-write can be implement in the software level since hardware don't care.
##### Notice that both hardware and software know PTE, but only kernel knows the Copy-on-write
#### Fill On Demand
- Can I start running a program before its code is in physical memory?

#### Design a page fault handler
1. OS must book-keeping whereabouts of all the physical frames on persistent storage
- What info shall the kernel maintain?
  - For every swapped out VPN of a process, the disk location of the page content
- when does such book-keeping info need to be create?
  - the first time a virtual page is swapped out from a process
  - Subsequent swap out of the page amy result in change of the mapping, depending on OS policy

- What needs to happen during the page fault handler?
  allocate a free physical frame to copy the page data to
  - how to locate the page data on the persistent storage?
    - use the book-keeping info stored.
  - what kernel data needs to be updated?
    - PTE entry for the virtual page
    - can also update the TLB
- what other actions need to take place?
  - load the page content from persistent storage into mem
  - while waiting for disk I/O to complete, schedule a diff process to run, mark the offending process as "blocked".
2. How does the OS know which physical frame this offending PTE used to point?
This question is wrong!
You should not go to find that physical frame, it might have been used by another process. We should copy the disk data to a new physical frame. Or you might overwrite the data being used by other processor.

3. Transfer the data stored on the persistent storage into mem.




## Lecture 16: Concurrency II

Synchronization: Mutual Exclusive + Ordering

By the definition of thread, its access to shared memory space is unfettered.

And its scheduling is completely unpredictable.

So, from this angle, all threads race to access data! ==> very bad design

Defs:
**Race condition**
**Mutual exclusion**
**Lock**
**Critical section**

### Locks

Lock::acquire
Lock::release

Milk problem:
1. Liveness: Someone buys if needed
2. Safety: At most one person buys
3. Fairness: Everyone has fair chances to get the lock

1 & 2 are the most important, 3 is good to have.

If you only have a single processor, we should disable the interrupt, or we may have a dead lock.


### ONLY IN LECTURE

Once you acquire the lock, you can assume that it's in persistant state.

```
// Basic programming pattern for Critical section
Lock::acquire
  Manipulation of shared data
Lock::release
```
1. One lock per shared data
2. To be effective, all threads must apply locking on the data

The difference between Concurrency access in OS v.s. program:
OS does not trust others.
The program can behave like designed, there is no enforcement.

So Synchronization is kinda weird for the OS since it's more of a cooperation rather than enforcement.
The lock does not really lock it, it just signal others that the data is occupied by me, do not come.

When you have some threads, you'll never know when they will be executed.

Implementing Threads:
1. kernel Threads: to the kernel, a kernel thread and a single threaded user processor looks quite similar.

2. multithreads processes using kernel threads
3. User-level threads

We are going to focus on the first possibility.



Thread data structure:
TCB and stack
TCB --> stack info, saved registers, thread meta data
shared state are shared among all threads: code global vars, heap

Thread Context Switch
1. Voluntary
yield and join
2. Involuntary
Interrupt or exception
Some other thread is higher priority
3. How does it happen?
return to a new place

The key trick:

```
void thread_switch(oldThreadTCB, newTheadTCB){
    pushad;
    oldThreadTCB->sp = %esp;
    %esp = newThreadTCB->sp;
    popad;
    return;
  }
```

#### How to implement thread_yield()?
in the thread_yield(), there is thread_switch()

in the yield() after the swith(), the it's resumed to the old TCB NOT THE chosen TCB.

And the eableInterrupt() is not re-eable the one at the beginning of the yield(), but that in another thread.

the yield() is very fast so that do not worry about the length of the time of interrupt.(for single core)

```c++
void thread_yield(){
  TCB *previous_runningTCB, *chosenTCB, *finishedTCB;
  
  //Prevent an interrupt from stopping us in the middle of a switch
  disableInterrupts();
  
  // choose another TCB from the ready list
  chosenTCB = readyList.getNextThread();
  
  if (chosenTCB == NULL){
    //Nothing, go back to the original thread
  }
  else{
    runningThread->state = ready;
    readyList.add(runningThread);
    previous_runningTCB = runningThread;
    runningThread = chosenTCB;
    thread_switch(*previous_runningTCB, chosenTCB);
    runningThread->state = running;
  }
  
  while((finishedTCB = finishedList->getNextThread()) != NULL){
    delete finishedTCB->stack;
    delete finishedTCB;
  }
  enableInterrupts();
}
```



##### why disable the interrupt?
to protect the shared data: ready list!

If this is not kernel code, we have to use other tricks like locks.

#### Never acquire a lock in interrupt handler!!!
because if the interrupt handler tries to acquire a lock that being held by others, DEAD LOK!!

In this case, one CPU core own their own ready list. Or there still may be a race condition on the ready list since the disableInterrupt() only works on the current CPU. The downside is that if a thread is ready but other cores are not doing anything.


## Lecture 18

EXAM: what does this line do? Is there anything wrong? yield() switch() join() ... we should be able to implement those.

All these funtions have nothing special that make them can be only done in kernel, you can implement similar things in user mode with proper coding.

#### Thread_create()
need a wrapper, to call the create() and exit once created, since we might not use it immediately after creating it.

- allocate TCB
-  allocate stack
- build stack frame for base of stack (stub)
- put thread on ready list
- will run sometime later (maybe right away)


```
thread_create(thread_t thread, void (*func)(int), int arg){
    TCB* tcb = new TCB();
    thread->tcb = tcb;
    tcb->stack_size = INIT_STACK_SIZE;
    tcb->stack = new Stack(INIT_STACK_SIZE);
    // now the TCB and stack are allocated

    tcb->sp = stack + INIT_STACK_SIZE;
    tcb->pc = stub;
    // init the register to let the program run at the stub
    
    *(tcb->sp--) = stub;
    *(tcb->sp) = func;
    tcb->sp -= SizeOfPopad;

    tcb->state = READY;
    readyList.add(tcb); // put tcb on ready readyList
    
  }


  stub(func, args){
      (*func)(args);
      thread_exit();
    }

```
Notice here the tcb's member vars: tcb, stack_size, stack, sp, pc, state...

the create() function  pretend it's resuming from another thread, so that every function is in a unified format, which means they can all use thread_swith(). When it gets resumed, it needs certain words int the stack to fufill the popad instructions in thread_switch(). Thus, we need a fake funcion like stub() to create that stack space.

The popad will add those random vals into the stack but it doesn't matter since a new thread should not trust any un-init vals.

#### Subtlety

- thread_create() puts new thread on ready list
- When it first runs, some thread needs to call thread_switch()
  - save old thread state to stack
  - pop the new thread state from stack into the registers
- Set up new thread's stack as if it has saved its state in thread_switch()
  - return to the sub at the base address of the stack to run the func




### Involuntary 
1. timer or I/O interrput
    lernel can decide that some other thread should run
  1. once you are in the kernel, the process are seen as threads.
  2. notice that user process has the kernel stack and the user stack, but the kernel thread only has the kernel stack
  3. we can use switch() as context switch
2. simple version
    End of interrupt handler calls thread_switch()
    when resumed, return from handler resumes kernel thread or uer process.
    Thus, processor context is saved/restored twice.(once by thread switch, and once by interrupt handler)

![After an interrupt at x86](img/After an interrupt at x86.png)

It seems that we need to do sth like in the yield(), call the scheduler to run to give us a chosen pcb and call swith(), in the switch() we use iret after popad.

However, think about this. Since you are already in an interrupt, we have states saved in the trap frame, on top of that we also do pushad in the swith(), which is redundant. 

Resuming a thread from the switch() which is also interrupted let the resumed thread start at the interrupt handler, which will next do the popad again followed by an iret.

#### Faster Thread/Process Switch

- Essentially tail call elimination <== if the context switch is cause by interrupt
  - interrupt handler will saved all the states
  - decide to run a new thread
  - throw away current state of interrupt handler
    - you DO NOT really call thread_switch() and create a new frame for it, which would save the registers and return address. Instead it become a jump.
    - reuse the thread's saved state when entering the interrupt handler
- Set saved stack pointer to trap frame (on x86, including the saved registers and six vals)
- On resume, pops trap frame to restore interrupt thread
- This requires thread_switch() use the same format stack frame as trap frame. On x86, this also means thread_switch() will use iret, instead of ret instruction. Now both voluntary and involuntary context switch are all looks like resume from an interrupt so that they are uniform.



#### Can we support user level threads without the help of kernel?

##### reason: kernel only has one way of scheduling, which is less flexible for the need from app to app. 

In this case, the kernel may not know the existence of the user threads since the thread context swith is not down throught kernel.

1. What if there is an I/O request and the kernel block the whole processor?
2. How do we preempt other threads when kernel does not kick in (one way to do is to use yield() everywhere to let threads balance the workload but this is unpractical)?



## Lecture 19

We can use **signal**. Like in the terminal <ctr + c> is s signal to do a **up call**, and kernel will ask the user processor to do something. And then we can write our own signal handler.

For example, kernel has timer interrupt, so kernel can decide to send a signal to a process who need it periodically.

```c
/*

An upcall is a mechanism that allows the kernel to execute a function
in userspace, and potentially be returned information as a result.

An upcall is like a signal, except that the kernel may use it at any
time, for any purpose, including in an interrupt handler.

This means that an upcall can potentially destroy the behaviour of the
kernel. If an interrupt handler decides to ask a user space function
for some information, and the function page faults or does blocking
IO, your kernel is quite likely trashed.

So, upcalls aren't there for everyday software development. But for
specific purposes, they can be extremely useful. 

*/
```



#### Green thread(early JAVA)

- user level lib, within a process
- lub does thread context swtich
- preemption via upcall/UNIX signal on timer interrupt
- use multiple processes for multi-core parallelism
  -  shared mem region mapped into each process

Since the process can change the user level stack for the threads

Say if we have a multi-core CPU, this does not work. Because when you want to let a core to take care of a thread, it needs kernel to kick in. However, the green thread does everthing at user level.

The solution is to use shared mem region mapped into each process.



#### More modern way

- Scheduler activations
  - kernel allocates processors to user-level thread lib (after the specific core take over, the scheduling will be done in that core, which are decided by the user level processors in that core, and we have the following 2 bullet points) 
  - thread lib implements context switch
  - thread lib decides what thread to run next
- upcall whenever kernel needs a user level scheduling decision (if there is I/O interrupt, kernel will ask the process to do sth or it will be blocked)
  - process assigned a new processor
  - processor removed from process
  - sys call blocks in kernel



### Spinlock issue

```c
Spinlock::acquire() {
  while (testAndSet(&lockValue) == BUSY){
    
  }
}

Spinlock::release() {
lockValue = FREE
}
// downside: a waste of cpu resources
// fairness issues: generally cannot assure fairness
```



#### How to solve it?

we may try to make the spinlock less aggressive. for perfomance

and we should have a wait queue? for fairness

```c
// There might be multiple threads trying to acquire the lock and put themselves into the wait list. So there will be race conditions(since you will have the pointers to move and change).
// Thus, they have to be synchronized ==> they have to be protected by another lock -- a spinlock
```



```c++
class Lock {
	private:
  	SpinLock spinLock; // ==> to protext the lock data structure
		int value = FREE;
  	Queue waiting;
  public:
  	void acquire();
  	void release();
}


Lock::acquire() {
  spinLock.acquire();
  if (value == BUSY) {
    waiting.add(myTCB);
    scheduler->suspend(&spinLock);
  }else {
    value = BUSY;
    spinLock.release();
  }
}

Lock::release() {
  spinLock.aquire();
  if (!waiting.empty()) {
    next = waiting.remove();
    scheduler->makeReady(next);
  }else {
    value = FREE;
  }
  spinLock.release();
}
```



```c++
Sched::suspend(SpinLock *spinLock) {
  TCB* next;
  
  disableInterrupts(); // we need to disable interrupt to ensure the data consistancy?
  schedSpinLock.acquire(); // we need a seperate spin lock to protect the ready list
  spinLock->release();
  runningThread->state = WAITING;
  next = readyList.remove();
  runningThread = next;
  thread_switch(myTCB, next);
  runningThread-> RUNNING;
  schedSpinLock->release();
  enableInterrupts();
}


```



Notice that the suspend() can resume the thread stopped by yield().



#### why we want interrupt

if the interrupt handler need to modify the ready list, then it will acquire the spin lock that may be held by others. DEAD LOCK!

```c++
Sched::makeReady(TCB* thread) {
  disableInterrupt();
  schedSpinLock.acquire();
  readyList.add(thread);
  thread->state = READY;
  schedSpinLock.release();
  enableInterrupts();
}
```



**IMPORTANT:**

- in Lock::acquire(), can we release the spinLock right after the waiting.add()?
  - Because other threads may do sth before we change the readyList.
  - maybe makeReady the thread that we are about to suspend.
  - You won't have a dead lock.
  - But I'm about to put myself to waitList having a state of WAITING after someone put me in the readyList.
    - The problem here is that every thread in ready list should have a state of ready, it's about the data consistency.
    - It's not a bug though
  - We really need to acqurie the lock in a order so that we don't generate a gap between and may produce flaws.



## Lecture 20 Ordering, Condition Variable

**IMPORTANT**, **EXAM**:

- why should we release the lock in suspend() rather than in the acquire() after waiting.add()

  - ```c++
    // If we release the spinLock right after the waiting.add(), we will fail to keep the consistency. Other thread may get in and put that thread we just added in to the waiting list in the ready list.
    // And since the state update and switch() does not get to run, the vals of the TCB get put in the ready list is stale.
    // So when get resumed, it kind of walking back the time. Since the pointer to the code section may still point to somewhere it has executed in the past.
    ```

  - Exam may ask where the lastest timing we can put the release()

### Ordering

we can do thread_join() for every thread

thread_join() is like a waiting list to synchronize all threads

#### Shared Bounded Buffer

- A bounded buffer is shared among multiple threads for message exchange
- producer threads put items into the buffer (sending the message)
- consumerj thread get items from the buffer (receiving a message)
- **Requirement: each message is received by only one thread.**



##### implementation

```c++
// Consumer
while(!(item=tryget())) {
  
}
use(item);

// producer
while(!tryput(item) {
}
      
tryget() {
  item = NULL;
  lock.acquire();
  if (front < tail) {
    item = buf(front % MAX);
    front++;
    notify threads waiting on condition (tail - front) < MAX
  } else {
		go to sleep on condition front < tail
    put thread on waiting list and release the lock
    lock.acquire
  }
  lock.release();
  return item;
}
      
tryput(item) {
  success = 0;
  lock.acquire();
  if (tail - front != 0) {
    buf(tail % MAX) = item;
    tail++;
    success = 1;
    notify threads waiting on condition front < tail
  } else {
    go to sleep on condition (tail - front) < MAX
    put thread on waiting list and release the lock
    lock.acquire
  }
  lock.release();
  return success;
}
      
// need to have separate waiting queues for differnent conditions of interest
```

Should we assume the condition is right after being waken up.

NO!! Since other threads may take over in between right after the thread was woken up.



How to fix this?

```c++
tryget() {
  item = NULL;
  lock.acquire();
  while (!(front < tail) {
    go to sleep on condition front < tail
    put thread on waiting list and release the lock // context switch (suspend or yield)
    lock.acquire
  }
  item = buf(front % MAX);
  front++;
  notify threads waiting on condition (tail - front) < MAX
  lock.release();
  return item;
}
      
tryput(item) {
  success = 0;
  lock.acquire();
  while (!((tail - front) < MAX)) {
    go to sleep on condition (tail - front) < MAX
    put thread on waiting list and release the lock // context switch (suspend or yield)
    lock.acquire
  } 
  buf(tail % MAX) = item;
  tail++;
  success = 1;
  notify threads waiting on condition front < tail
  lock.release();
  return success;
}
```



### Condition Variables

- Indicates a condition that needs to be satisfied for a thread to proceed.
- if condition is not satisfied, a thread can be put on a wait queue associated with the condition variable.
  - Must **call wait()** inside a critical section, **while holding the lock**
  - wait() **atomically** releases the lock and put the thread on the wait queue.
  - when woken, **reacquires the lock before returning from wait()**
- A thread can wake thread(s) waiting on a condition variable
  - signal
  - broadcast



### CV Design Pattern

```c++
methodThatWaits() {
  lock.acquire();
  // R/W shared state
  
  while (!testSharedState()) {
    cv.wait(&lock);
  }
  
  // R/W shared state
  lock.release();
}

methodThatSignals() {
  lock.acquire();
  // R/W shared state
  
  // if testSharedState is no true
  cv.signal();
  
  // R/W shared state
  lock.release();
}
```



### Shared Bounded Buffer

```c++
get() {
  lock.acquire();
  while(front == tail) {
    empty.wait(&lock);
  }
  item = buf[front % MAX];
  front++;
  full.signal();
  lock.release();
  return item;
}

put(item) {
  lock.acquire();
  while ((tail - front) == MAX) {
    full.wait(&lock);
  }
  buf[tail % MAX] = item;
  tail++;
  empty.signal();
  lock.release*();
}

// Initially: front = tail = 0; MAX is buffer capacity
// empty/full are condition variables
```

pre/post CV

### Key Pionts for CV

- ALWAYS hold lock when calling wait, signal, broadcast

  - Condition variable is sync FOR shared state
  - ALWAYS hold lock when accessing shared state

- CV is memoryless

  - if signal when no one is waiting, no op
  - if wait before signal, waiter wakes up

- Wait atomically releases lock and put the thread into waiting list

  - why?
    - we might lost the signal, since before putting the thread into the waiting list, the signal might be emitted.

- When a thread is woken up from wait, it may not run immediately (this is why we need to put it in a loop)

  - signal/broadcast put thread on ready list

- Wait MUST be in a loop

  - ```c++
    while(needToWait()) {
      condition.Wait(&lock);
    }
    ```

- simplified implementation

  - of condition vars and locks
  - of code that uses condition vars and locks



### CV implementation

```c++
class CV {
  private:
  Queue waiting;
  public:
  void wait(lock.isHeld());
  void signal();
  void broadcast();
}
```

```c++
void CV::wait(Lock *lock) {
  assert(lock.isHeld());
  waiting.add(myTCB);
  scheduler.suspend(&lock);
  lock->acquire();
}


void CV::signal() {
  if (waiting.notEmpty()) {
    thread = waiting.remove();
    scheduler.makeReady(thread);
  }
}

void CV::broadcast() {
  while (waiting.notEmpty()) {
    thread = waiting.remove();
    scheduler.makeReady(thread);
  }
}
```



## Lecture 21 Semaphore

(Dijkstra again)

**One execution sequence waits for another**

purpose 1: mutual exclusion

purpose 2: ordering (we use CV)

|                  | Block oneself  | Unblock others |
| ---------------- | -------------- | -------------- |
| Mutual Exclusion | Lock.acquire() | Lock.release() |
| Ordering         | Cond.wait()    | Cond.signal()  |



### Semaphore Concept

- Semaphore has a non-negative integer value
  - P() **atomically** waits for value to become > 0,. then decrements
  - V() **atomically** increments value (waking up waiter if needed)
  - Operations are **atomic**
    - e.g., if value is 1, two P's will result in value 0 and one waiter



|                  | Block oneself | Unblock others |
| ---------------- | ------------- | -------------- |
| Mutual Exclusion | Sem.P()       | Sem.V()        |
| Ordering         | Sem.P()       | Sem.V()        |



Prob: it may hard to understand the code since this is too elegant



#### Init val!

Sem.init(N) sets an initial value of the semaphore

The init val determines the behavior of the semaphore in subsequent use



#### Using Sem for Mutual Exclusion

Sem.init(1)

![Sem for Mutual Exclusion](img/Sem for Mutual Exclusion.png)


EXAM: how to implement sth in Semaphore!

The atomic means the whole things have to be atomic. From sem operations to wake up funciont and increment/decrement are all atomic!

When we use with a init of 1, we call it binary sem, since it works like a lock.

#### Using Sem for Ordering

![Sem for Ordering](img/Sem for Ordering.png)

##### why you can't do sem_mutex.P() first?
```c++
// Becase if you do a sem_full.P(), you can potentially get blocked.
// You can only be woken up by another thread using V().
// However, you are holding the sem_mutex.P() so that nobody can get in
// DEAD LOCK!
```



empty is init with 0 and full is init with MAX.
Since the put() will work first and get() will wait first.

Notice:
For singals, they can emit singals and nobody is waiting.
But for V(), if no one is waiting, then we will change the val!
Which is not acceptable.
V() will increment the val by 1.
Signal is memoryless, but V() is not.

Thus,semaphore is harder to implement.


Also, for signal, when you wake up, you have to check the condition since
it can be false by then. However, semaphore is differnent.


##### Sem for CV?

### Bottom Like regarding Semaphore
- an elegant and uniform solution for Synchronization
- Require careful design, in particular mapping the semaphore's initial value to
problem domain
- can be confusing, especially since it can be used for two very differnent purposes
- Recommendation for general programming: stick with lock and CV

### Concurrency Bugs
(safty: race condition   progress: dead lock or starvation)
- did not apply Synchronization
- deadlock or starvation

#### The Dining Philosopher Problem

#### Principled Synchronization
- identfy objs or data structures that can be accessed by multiple threads Concurrency
  - in OS kernel, everything
- Add locks to objs/module
  - grab lock on start to every method/procedure
  - Release lock on finish
- If need to wait
  - while (needToWait()){condition.Wait(lock);}
  - do not assume when you wake up the condition has become true
- if do sth that might wake someone up
  - signal or broadcast
- always leave shared state vars in a consistent state
  - when lock is released, or when entering waiting
- if need to acquire multiple locks, use the same order across all threads












