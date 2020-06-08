---
title: locks
date: 2020-06-07 12:17:29
tags:
- concurrency
categories:
- OS
---
## Evaluate Lock

1. The first is whether the lock does its basic task, which is to provide mutual exclusion.
2. The second is fairness. Does each thread contending for the lock get a fair shot at acquiring it once it is free? Another way to look at this is by examining the more extreme case: does any thread contending for the lock starve while doing so, thus never obtaining it?
3. The final criterion is performance, specifically the time overheads added by using the lock.
    1. One is the case of no contention; when a single thread is running and grabs and releases the lock.
    2. Multiple threads are contending for the lock on a single CPU; in this case, are there performance concerns?
    3. There are multiple CPUs involved, and threads on each contending for the lock?

## Control Interupt

- How: disable interrupts for critical sections by using hardware instruction as if iy were atomic. When finished, re-enable interrupts(via hardware instruction).
- What: single-processor systems
- Code:
    ![image](https://s1.ax1x.com/2020/06/06/tgp9dH.png)
<!-- more -->
- Positive: Simplicity
- Negative: 
    1. require to allow any calling thread to perform a privileged operation(turning on and off interrupts) and too much trust in applications.
    2. not work on multiprocessors. If multiple threads are running on different CPUs, doesn\'t matter whether interrupts are disavled. Multiple processors are now commonplace, need a general solution.
    3. lead to interrupts being lost.
    4. least importantly, inefficient.

For these reasons, the method is only used in limited contexts. For example, when OS accesses its own data structures. Because trust issue disappears inside the OS. 

## Failed attempt: just using load/store

![t2Pzzn.png](https://s1.ax1x.com/2020/06/07/t2Pzzn.png)

- How: use a simple variable(flag) to indicate whether a lock is available or not. A thread calls `lock()`, sets the flag to 1 to indicate it holds the lock. If finished, call `unlock()`, the flag is set to 0 to indicate it doesn\'t possess the lock. If B calls `lock()` when A holds the lock, the it will simply **spin-wait**. Until A calls `unlock()`, B whill quit the loop and set the flag.
- Problems:
    1. Correctness: see the picture below. Assuming flag begins with 0. We do not provide mutual exclusion. Both threads set the flag to 1 and can enter the critical section.
    ![t2Fi6I.png](https://s1.ax1x.com/2020/06/07/t2Fi6I.png)
    2. Performance: the way a thread waits to acquire a lock that is alread held in this attempt is **spin-waiting**. It endlessly checks the value of flag. Waste too much time waiting for the other thread to release a lock. We should avoid the kind of waste.

## Building working spin locks with test-and-set

- How: By hardware supporting, which is known as **test-and-set**(or **atomic exchange** instruction). It does the following things.The key, of course, is that this sequence of operations is performed **atomically**.
![t2AEFS.png](https://s1.ax1x.com/2020/06/07/t2AEFS.png)
![t2Zko6.png](https://s1.ax1x.com/2020/06/07/t2Zko6.png)

## Evaluate spin locks

- Correctness: the spin lock only allows a single thread to enter the critical section at a time.
- Fairness: simple spin locks discussed thus far may be not fair and lead to starvation.
- Performance: 
    - single CPU: performance overheads can be quite painful. If the thread holding the lock is preempted within a critical section, then the scheduler might run every other thread, each of which tries to get the lock. Each thread will spin for the duration of a time slice before giving up the CPU.
    - multiple CPUs: work reasonably well(if number of thread equals the number of CPUs). Spinning to wait for a lock held on another processor does not waste many cycles, thus can be effective.

## Compare-And-Swap

- what: hardware primitive that some systems provide, which is called **compare-and-swap** on SPARC, or **compare-and-exchange** on x86. Following is C pseudocode. Replace `lock()` routine as following.
![t2oo9g.png](https://s1.ax1x.com/2020/06/07/t2oo9g.png)
![t2Tn8e.png](https://s1.ax1x.com/2020/06/07/t2Tn8e.png)

- characteristic: more powerful than test-and-set. Using the power when delving into **lock-free synchronization**. If simplely build spin lock, then it\'s identical to the spin lock above.

## Load-Linked and Store-Conditional

- what: A pair of instructions that work in concert to help build critical sections. Which on MIPS architecture, are the **load-linked** and **store-conditional** instructions. The following is C pseudocode for these instructions.
![tRpEy6.png](https://s1.ax1x.com/2020/06/07/tRpEy6.png)

- characteristic: The key difference comes from **store-conditional**, which only succeeds and updates the value stored at the address just **load-linked** from if no intervening store to the address has taken place. If suceess, updates the value at ptr and return 1. If fail, then not update and 0 is returned.
- lock:
    ![tRiMHf.png](https://s1.ax1x.com/2020/06/07/tRiMHf.png)
    - success: A thread spins waiting for the flag to be set to 0. Once done, the thread tries to acquire the lock via the store-conditional. If succeeds, the thread has atomically changed the flag's value to 1 and thus can proceed to the critical section.
    - failure: A thread calls `lock()` and `LoadLinked()` returns 0 because the lock is not held. Then it is be interupted before it attempt the `StoreConditional()`. B thread enters `lock` code and executes the `LoadLinked` and also gets 0. The two both attempt the `StoreConditional`, but one of them can succeed, because when one thread succeeds, it must change the value of address `ptr`, so the other thread can not update and will try again.
    - A more concise code as following.
    ![tRFEZV.png](https://s1.ax1x.com/2020/06/07/tRFEZV.png)

## Fetch-And-Add

- what: harware primitive which atomically increments a value while returning the old value at a particular address. The C pseudocode is as following.
![tRgIR1.png](https://s1.ax1x.com/2020/06/08/tRgIR1.png)