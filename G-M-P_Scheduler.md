# Go Scheduler: GMP Model

| Letter | Stands for | Meaning |
|---|---|---|
| **G** | Goroutine | Lightweight unit of work |
| **M** | Machine | OS thread |
| **P** | Processor | Logical runtime resource |

## Table of Contents

- [Why GMP?](#why-gmp)
- [1. G: Goroutine](#1-g-goroutine)
- [2. M: Machine](#2-m-machine)
- [3. P: Processor](#3-p-processor)
- [4. Local Run Queue](#4-local-run-queue)
- [5. Global Run Queue](#5-global-run-queue)
- [6. Work Stealing](#6-work-stealing)
- [7. GOMAXPROCS](#7-gomaxprocs)
- [8. Context Switching](#8-context-switching)
- [9. Preemption](#9-preemption)
- [10. Blocking Syscall](#10-blocking-syscall)
- [11. No Work: Parking](#11-no-work-parking)
- [12. Network Poller](#12-network-poller)
- [Complete GMP Picture](#complete-gmp-picture)
- [One-line Revision](#one-line-revision)

## Why GMP?

Go can have many goroutines, but we don't want one OS thread for every goroutine.

```text
Many Goroutines
      ↓
Go Scheduler
      ↓
OS Threads
      ↓
CPU
```

The Go runtime scheduler decides which runnable goroutine should execute, while an M executes Go code with a P.

## 1. G: Goroutine

- Lightweight unit of work.
- Created using the `go` keyword.
- Many goroutines can exist at the same time.
- Goroutines don't directly execute on the CPU.
- They execute through OS threads (M).

```text
G1  G2  G3  G4  G5 ...
```

## 2. M: Machine

- Represents an OS thread.
- Actually executes Go code on a CPU.
- An M needs a P to execute Go code.

```text
M → OS Thread → CPU
```

## 3. P: Processor

- A logical runtime resource, **not** a CPU core.
- Required by an M to execute Go code.
- Each P has a local run queue.
- The number of Ps is controlled by `GOMAXPROCS`.

```text
P1 → Local Run Queue
     [G1 G2 G3]

P2 → Local Run Queue
     [G4 G5]
```

## 4. Local Run Queue

Each P has its own local run queue containing runnable goroutines.

```text
P1 → [G1 G2 G3]
P2 → [G4 G5]
```

A P can execute runnable goroutines from its available work.

## 5. Global Run Queue

There is also a global run queue shared by all Ps.

```text
P1 → [G1 G2]
P2 → [G3 G4]

Global Run Queue
→ [G5 G6 G7]
```

The scheduler can obtain runnable work from the runtime's available queues.

> **Important:** Don't memorize a rigid order like `Local → Global → Steal`. The actual scheduler is more complex.

## 6. Work Stealing

Suppose P2 has no work:

```text
P1 → [G1 G2 G3 G4]
P2 → []
```

P2 can steal runnable goroutines from another P's local queue:

```text
P1 → [G1 G2]
P2 → [G3 G4]
```

**Purpose:** keep Ps busy and balance work.

## 7. GOMAXPROCS

```go
runtime.GOMAXPROCS(2)
```

This means at most 2 Ps can execute Go code in parallel.

It does **not** mean:

- ❌ 2 OS processes
- ❌ 2 OS threads
- ❌ only 2 goroutines
- ❌ a fixed number of goroutines per P

Example:

```text
100 Goroutines
      ↓
     2 Ps
      ↓
Many Gs scheduled over those Ps
```

## 8. Context Switching

When execution changes from one goroutine to another (`G1 → G2`), that is a context switch.

It can happen when G1:

- finishes
- blocks
- waits
- gets preempted

```text
P + M
  ↓
  G1
  ↓
context switch
  ↓
  G2
```

## 9. Preemption

If a goroutine runs for too long, the runtime pauses it and gives another runnable goroutine a chance.

```text
G1 ────────────────→
          ↓
      preempted
          ↓
G2 ─────────→
```

- Modern Go supports asynchronous preemption.
- G1 is paused, not killed.

## 10. Blocking Syscall

Suppose G1 makes a blocking syscall:

```text
P1 → M1 → G1
           ↓
     blocking syscall
```

M1 may block. The runtime can detach P1 from M1:

```text
M1 → blocked

P1 → M2 → G2 → CPU
```

So other goroutines can continue running.

When G1 becomes runnable again:

```text
G1 → Runnable
      ↓
  Scheduler
      ↓
Available P + M
      ↓
 Execute G1
```

G1 doesn't have to return to the same P/M.

## 11. No Work: Parking

If an M has no useful work, it can be parked:

```text
No runnable G
      ↓
M can be parked
```

- Parked ≠ killed.
- When work becomes available, the runtime can wake an M.

## 12. Network Poller

For network I/O:

```text
G1
 ↓
waiting for network
 ↓
Network Poller
 ↓
network event ready
 ↓
G1 becomes runnable
 ↓
Scheduler
 ↓
P + M
 ↓
CPU
```

This allows other goroutines to execute while G1 waits for network I/O.

## Complete GMP Picture

```text
                    Goroutines
             G1 G2 G3 G4 G5 G6...
                       ↓
                  Go Scheduler
                       ↓
                 findRunnable()
                       ↓
          ┌────────────┴────────────┐
          ↓                         ↓
         P1                        P2
   Local Run Queue           Local Run Queue
     [G1 G2 G3]                [G4 G5]
          ↓                         ↓
         M1                        M2
      OS Thread                 OS Thread
          ↓                         ↓
       CPU Core                  CPU Core
```

The scheduler can also do the following:

| Situation | What happens |
|---|---|
| **Work stealing** | An idle P takes runnable Gs from another P's queue |
| **Blocking syscall** | P is detached from the blocked M, and another M uses that P |
| **Preemption** | G1 is paused, and G2 runs |
| **No work** | M is parked, and woken again when new work arrives |

## One-line Revision

> The Go scheduler manages many goroutines (G) across OS threads (M), using Ps as logical runtime resources. Ps maintain runnable work, work can be stolen between Ps, blocked Ms can release their Ps, and preemption/parking help the runtime use CPU resources efficiently.



Interview Add-ons:

Why are goroutines cheap?
	Goroutine	OS thread
Initial stack	~2 KB, grows and shrinks dynamically	Fixed, typically 1 MB or more
Created / managed by	Go runtime	Kernel
Context switch	In user space (cheap)	Through the kernel (expensive)


G states
text
Runnable → Running → Waiting → Runnable ...
Runnable: ready, sitting in a run queue.
Running: executing on an M that holds a P.
Waiting: blocked on a channel, mutex, timer, network I/O, etc.

When a G blocks on a channel or mutex, only the G is parked. The M is not blocked and picks up another G. This is different from a blocking syscall, where the M itself blocks.

GOMAXPROCS default
Defaults to the number of logical CPUs.
From Go 1.25, the default also respects the container's cgroup CPU limit (relevant when running in Kubernetes).

Preemption details
Before Go 1.14, preemption was cooperative (only at function calls), so a tight loop like for {} could starve other goroutines.

From Go 1.14, preemption is asynchronous and signal based.
The sysmon background thread preempts a goroutine that has been running for more than about 10 ms.

Work stealing details
An idle P steals half of the runnable Gs from another P's local queue.

A local run queue holds up to 256 Gs. When it is full, half of them are moved to the global run queue.
runtime.Gosched()

Makes the current goroutine voluntarily yield, so other runnable goroutines get a chance. The goroutine stays runnable and resumes later.

Self-check Questions

Goroutine vs thread? A goroutine is a user-space unit managed by the Go runtime with a small growable stack; a thread is managed by the kernel with a large fixed stack. Many goroutines are multiplexed onto a few threads.

If I create 10,000 goroutines, do I get 10,000 threads? No. They are multiplexed over a small number of Ms. The number running Go code in parallel is limited by GOMAXPROCS.

What happens to other goroutines when one makes a blocking syscall? The M blocks, the runtime detaches its P and hands it to another M, so the other goroutines keep running.

With GOMAXPROCS=1, is there concurrency? Parallelism? Concurrency yes (goroutines interleave on one P). Parallelism no (only one goroutine runs Go code at a time).

One-line Revision

The Go scheduler manages many goroutines (G) across OS threads (M), using Ps as logical runtime resources. Ps maintain runnable work, work can be stolen between Ps, blocked Ms can release their Ps, and preemption/parking help the runtime use CPU resources efficiently.