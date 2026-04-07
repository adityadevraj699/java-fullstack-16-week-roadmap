# Java Multithreading: Synchronization, Locks, Executors, Deadlock, CountdownLatch & CompletableFuture

---

## 🖥️ Foundation: Understanding the Hardware First

**CPU :** The CPU, often referred to as the brain of the computer, is responsible for executing instructions from programs. It performs basic arithmetic, logic, control, and input/output operations specified by the instructions.

**Core :** A core is an individual processing unit within a CPU. Modern CPUs can have multiple cores, allowing them to perform multiple tasks simultaneously.

**Program :** A Program is a set of instructions written in a programming language that tells the computer how to perform a specific task.
- *Example:* Microsoft Word is a program that allows users to create and edit documents.

**Process :** A process is an instance of a program that is being executed. When a program runs, the operating system creates a process to manage its execution.

**Thread :** A thread is the smallest unit of execution within a process. A process can have multiple threads, which share the same resources but can run independently.

**Multitasking :** Multitasking allows an operating system to run multiple processes simultaneously. On single-core CPUs, this is done through time-sharing — rapidly switching between tasks. On multi-core CPUs, true parallel execution occurs, with tasks distributed across cores. The OS scheduler balances the load, ensuring efficient and responsive system performance.

---

## 🔀 What is Multithreading?

### Multithreading refers to the ability to execute multiple threads within a single process concurrently.

- A web browser can use multithreading by having separate threads for rendering the page, running JavaScript, and managing user inputs. This makes the browser more responsive and efficient.

- Multithreading enhances the efficiency of multitasking by breaking down individual tasks into smaller sub-tasks or threads. These threads can be processed simultaneously, making better use of the CPU's capabilities.

**In a single-core system:**
Even though there's only one core, the OS rapidly switches between threads (called **context switching**) so fast that it *feels* like they're running at the same time. It's like a chef cooking multiple dishes by quickly jumping between them.

**In a multi-core system:**
Threads truly run in parallel. One thread runs on Core 1, another on Core 2 — at the exact same moment. Real parallelism.

---

## 🧵 Thread Lifecycle in Java

A thread in Java goes through the following states:

```
NEW → RUNNABLE → RUNNING → BLOCKED/WAITING/TIMED_WAITING → TERMINATED
```

| State | Meaning |
|---|---|
| **NEW** | Thread is created but `start()` hasn't been called yet |
| **RUNNABLE** | Thread is ready to run, waiting for CPU time |
| **RUNNING** | Thread is actively executing on the CPU |
| **BLOCKED** | Thread is waiting to acquire a lock (monitor) |
| **WAITING** | Thread is waiting indefinitely for another thread to signal it |
| **TIMED_WAITING** | Thread is waiting for a specific time (`sleep()`, `join(timeout)`) |
| **TERMINATED** | Thread has finished execution |

---

## 🛠️ Creating Threads in Java

There are **3 main ways** to create a thread in Java:

---

### ✅ Way 1: Extending the `Thread` Class

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // DO NOT call run() directly! That runs on main thread.
    }
}
```

> ⚠️ **Important:** Always call `start()`, never `run()` directly. Calling `run()` directly doesn't create a new thread — it just runs the method normally on the current thread.

---

### ✅ Way 2: Implementing the `Runnable` Interface (Preferred)

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Task running: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyTask());
        t1.start();
    }
}
```

> 💡 This is preferred because Java doesn't support multiple inheritance. If your class already extends something, you can't also extend `Thread` — but you CAN implement `Runnable`.

---

### ✅ Way 3: Using Lambda (Modern Java — Most Clean)

```java
public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            System.out.println("Lambda thread: " + Thread.currentThread().getName());
        });
        t1.start();
    }
}
```

---

## 🔢 Thread Priority

Java allows you to set thread priority from `1` (MIN) to `10` (MAX). Default is `5` (NORM).

```java
Thread t1 = new Thread(() -> System.out.println("High Priority"));
t1.setPriority(Thread.MAX_PRIORITY); // 10

Thread t2 = new Thread(() -> System.out.println("Low Priority"));
t2.setPriority(Thread.MIN_PRIORITY); // 1

t1.start();
t2.start();
```

> ⚠️ Priority is just a *hint* to the OS scheduler. It does NOT guarantee execution order. Don't rely on it for correctness.

---

## 😴 `sleep()` and `join()`

### `Thread.sleep(milliseconds)` — Pause a Thread

```java
Thread t1 = new Thread(() -> {
    try {
        System.out.println("Working...");
        Thread.sleep(2000); // pause for 2 seconds
        System.out.println("Done after 2 seconds!");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});
t1.start();
```

### `join()` — Wait for Another Thread to Finish

```java
Thread t1 = new Thread(() -> {
    System.out.println("T1 doing heavy work...");
    try { Thread.sleep(3000); } catch (InterruptedException e) {}
    System.out.println("T1 done.");
});

t1.start();
t1.join(); // Main thread WAITS here until t1 finishes
System.out.println("Main continues after T1 is done.");
```

> Think of `join()` like saying: *"Hey main thread, wait here. Don't move forward until t1 finishes."*

---

## 🔐 Synchronization — The Core Problem

### The Problem: Race Condition

When multiple threads access and modify **shared data** at the same time, we get unpredictable results. This is called a **Race Condition**.

```java
class Counter {
    int count = 0;

    void increment() {
        count++; // This is NOT atomic! It's actually 3 operations: read, add, write
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        t1.start();
        t2.start();
        t1.join();
        t2.join();

        System.out.println("Final Count: " + counter.count);
        // Expected: 2000. Actual: something random like 1743, 1891...
    }
}
```

> The problem: Both threads read `count = 5` at the same time, both add 1, both write back `6` — but the true answer should be `7`. One increment was LOST.

---

### ✅ The Fix: `synchronized` Keyword

The `synchronized` keyword ensures only **one thread at a time** can execute a block of code.

```java
class Counter {
    int count = 0;

    synchronized void increment() { // Only ONE thread can be inside here at a time
        count++;
    }
}
```

Now the output will always be `2000`. ✅

**How it works under the hood:**
Every Java object has an internal lock (called a **monitor**). When a thread enters a `synchronized` method, it *acquires* the lock. Other threads trying to enter are *blocked* until the lock is *released* (when the method exits).

---

### Synchronized Block (More Fine-Grained Control)

Instead of locking the entire method, you can lock only the critical section:

```java
class Counter {
    int count = 0;
    Object lock = new Object();

    void increment() {
        // Non-critical code can run freely here
        synchronized (lock) {
            count++; // Only this part is protected
        }
        // More non-critical code here
    }
}
```

> Use synchronized blocks when you want to minimize the time a lock is held — better performance.

---

## 🔒 Locks — `java.util.concurrent.locks`

The `synchronized` keyword is simple but limited. The `Lock` interface gives you more power.

### `ReentrantLock` — The Most Common Lock

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Counter {
    int count = 0;
    Lock lock = new ReentrantLock();

    void increment() {
        lock.lock(); // Acquire the lock
        try {
            count++;
        } finally {
            lock.unlock(); // ALWAYS release in finally block!
        }
    }
}
```

> ⚠️ **Golden Rule:** Always release the lock in a `finally` block. If an exception occurs between `lock()` and `unlock()`, without `finally`, the lock is never released and your program hangs forever.

### Why use `ReentrantLock` over `synchronized`?

| Feature | `synchronized` | `ReentrantLock` |
|---|---|---|
| Try to acquire lock | ❌ No | ✅ `tryLock()` |
| Timeout for lock | ❌ No | ✅ `tryLock(timeout)` |
| Interruptible waiting | ❌ No | ✅ `lockInterruptibly()` |
| Fairness policy | ❌ No | ✅ `new ReentrantLock(true)` |
| Multiple conditions | ❌ No | ✅ `newCondition()` |

```java
// tryLock() example — don't block, just try
if (lock.tryLock()) {
    try {
        count++;
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Lock not available, doing something else...");
}
```

---

### `ReadWriteLock` — For Read-Heavy Scenarios

If many threads are reading but few are writing, use `ReadWriteLock`. Multiple readers can read simultaneously, but a writer gets exclusive access.

```java
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

class DataStore {
    private String data = "initial";
    private ReadWriteLock rwLock = new ReentrantReadWriteLock();

    public String read() {
        rwLock.readLock().lock(); // Multiple threads can hold this simultaneously
        try {
            return data;
        } finally {
            rwLock.readLock().unlock();
        }
    }

    public void write(String newData) {
        rwLock.writeLock().lock(); // Only ONE thread at a time
        try {
            data = newData;
        } finally {
            rwLock.writeLock().unlock();
        }
    }
}
```

---

## 💀 Deadlock — The Silent Killer

### What is Deadlock?

Deadlock occurs when two or more threads are **each waiting for a lock held by the other** — and none of them can proceed. Everyone is stuck. Forever.

```
Thread 1 holds Lock A → waiting for Lock B
Thread 2 holds Lock B → waiting for Lock A

Result: Both wait forever. Program hangs.
```

```java
public class DeadlockDemo {
    static Object lockA = new Object();
    static Object lockB = new Object();

    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (lockA) {
                System.out.println("T1 acquired A, waiting for B...");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                synchronized (lockB) { // T1 is waiting here
                    System.out.println("T1 acquired B");
                }
            }
        });

        Thread t2 = new Thread(() -> {
            synchronized (lockB) {
                System.out.println("T2 acquired B, waiting for A...");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                synchronized (lockA) { // T2 is waiting here
                    System.out.println("T2 acquired A");
                }
            }
        });

        t1.start();
        t2.start();
        // Both threads are now stuck. Output stops. Program never ends.
    }
}
```

### ✅ How to Prevent Deadlock

**1. Lock Ordering** — Always acquire locks in the SAME ORDER in every thread.

```java
// Both threads now acquire lockA FIRST, then lockB
// T2 will wait for lockA before even touching lockB — no deadlock possible
Thread t2 = new Thread(() -> {
    synchronized (lockA) { // Same order as T1
        synchronized (lockB) {
            System.out.println("T2 done");
        }
    }
});
```

**2. Use `tryLock()` with Timeout**

```java
if (lockA.tryLock(1, TimeUnit.SECONDS)) {
    try {
        if (lockB.tryLock(1, TimeUnit.SECONDS)) {
            try {
                // Do work
            } finally { lockB.unlock(); }
        }
    } finally { lockA.unlock(); }
} else {
    System.out.println("Couldn't acquire lock, backing off...");
}
```

**3. Minimize Lock Scope** — Don't hold locks for long. The shorter the lock duration, the less chance of deadlock.

---

## 🏊 Executor Framework — Thread Pooling

Creating a new thread every time is **expensive** (memory + CPU cost). A **Thread Pool** creates a fixed number of threads and reuses them for multiple tasks.

Think of it like a restaurant: instead of hiring a new chef for every order, you have a fixed kitchen crew that handles all orders.

### `ExecutorService` — The Main Interface

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        // Create a pool of 3 threads
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 10; i++) {
            int taskNum = i;
            executor.submit(() -> {
                System.out.println("Task " + taskNum + " running on: " + Thread.currentThread().getName());
                try { Thread.sleep(1000); } catch (InterruptedException e) {}
            });
        }

        executor.shutdown(); // No more new tasks accepted
        executor.awaitTermination(60, TimeUnit.SECONDS); // Wait for all tasks to complete
        System.out.println("All tasks done!");
    }
}
```

### Types of Thread Pools

```java
// Fixed pool — good for known workloads
ExecutorService fixed = Executors.newFixedThreadPool(4);

// Cached pool — creates threads as needed, reuses idle ones — good for short-lived tasks
ExecutorService cached = Executors.newCachedThreadPool();

// Single thread — all tasks execute sequentially in one thread
ExecutorService single = Executors.newSingleThreadExecutor();

// Scheduled — run tasks after a delay or periodically
ScheduledExecutorService scheduled = Executors.newScheduledThreadPool(2);
scheduled.scheduleAtFixedRate(() -> System.out.println("Heartbeat!"), 0, 5, TimeUnit.SECONDS);
```

---

### `Callable` and `Future` — Getting Results from Threads

`Runnable` can't return a value. `Callable` can.

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        Callable<Integer> task = () -> {
            System.out.println("Computing...");
            Thread.sleep(2000);
            return 42; // Return the result
        };

        Future<Integer> future = executor.submit(task); // Submit and get a Future

        System.out.println("Doing other work while task runs...");

        Integer result = future.get(); // BLOCKS here until result is ready
        System.out.println("Result: " + result); // Output: Result: 42

        executor.shutdown();
    }
}
```

> `Future.get()` blocks the calling thread. If you don't want to block, use `CompletableFuture` instead (covered below).

---

## ⏱️ CountDownLatch — Wait for Multiple Threads to Complete

`CountDownLatch` is like a countdown timer. You initialize it with a count. Threads call `countDown()` to decrement it. Other threads call `await()` to block until the count reaches zero.

**Real-world analogy:** A rocket launch. The control room (`await()`) waits until all pre-launch checks (threads) are done (`countDown()`). Only then, LAUNCH!

```java
import java.util.concurrent.*;

public class RocketLaunch {
    public static void main(String[] args) throws InterruptedException {
        int numChecks = 3;
        CountDownLatch latch = new CountDownLatch(numChecks);

        // Check 1: Fuel
        new Thread(() -> {
            System.out.println("Fuel check... OK");
            latch.countDown(); // Count: 3 → 2
        }).start();

        // Check 2: Navigation
        new Thread(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            System.out.println("Navigation check... OK");
            latch.countDown(); // Count: 2 → 1
        }).start();

        // Check 3: Communication
        new Thread(() -> {
            try { Thread.sleep(2000); } catch (InterruptedException e) {}
            System.out.println("Communication check... OK");
            latch.countDown(); // Count: 1 → 0
        }).start();

        System.out.println("Control room waiting for all checks...");
        latch.await(); // Blocks here until count = 0
        System.out.println("🚀 ALL SYSTEMS GO! LAUNCH!");
    }
}
```

```
Output:
Control room waiting for all checks...
Fuel check... OK
Navigation check... OK
Communication check... OK
🚀 ALL SYSTEMS GO! LAUNCH!
```

> ⚠️ `CountDownLatch` is **one-time use**. Once the count reaches 0, it cannot be reset. For reusable barriers, use `CyclicBarrier`.

---

## 🔄 CyclicBarrier — Synchronize Threads at a Meeting Point

All threads must reach the barrier before any of them can proceed. Unlike `CountDownLatch`, it can be reused.

```java
import java.util.concurrent.*;

public class TeamMeeting {
    public static void main(String[] args) {
        int teamSize = 3;
        CyclicBarrier barrier = new CyclicBarrier(teamSize, () -> {
            System.out.println("--- All team members arrived. Meeting starts! ---");
        });

        for (int i = 1; i <= teamSize; i++) {
            int member = i;
            new Thread(() -> {
                try {
                    System.out.println("Member " + member + " is on the way...");
                    Thread.sleep((long)(Math.random() * 2000));
                    System.out.println("Member " + member + " arrived.");
                    barrier.await(); // Wait for all members
                    System.out.println("Member " + member + " is now in the meeting.");
                } catch (Exception e) {}
            }).start();
        }
    }
}
```

---

## ⚡ CompletableFuture — Async Programming Done Right

`Future` has one big problem: `get()` blocks. `CompletableFuture` solves this with a **non-blocking, chained, pipeline-style** approach.

Think of it like ordering food online: you place the order (submit task), and you get a callback when it's ready. You don't have to stand at the door waiting.

### Basic Usage

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws Exception {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            // Runs in a separate thread
            try { Thread.sleep(2000); } catch (InterruptedException e) {}
            return "Hello from async task!";
        });

        // Non-blocking: do other work here
        System.out.println("Main thread is free to do other things...");

        // Chain a callback that runs when the result is ready
        future.thenAccept(result -> System.out.println("Got result: " + result));

        // Keep main alive to see the result
        Thread.sleep(3000);
    }
}
```

---

### Chaining Operations (Pipeline)

```java
CompletableFuture.supplyAsync(() -> "user123")           // Step 1: Get user ID
    .thenApply(userId -> "Fetched profile for: " + userId) // Step 2: Fetch profile (transform)
    .thenApply(profile -> profile.toUpperCase())            // Step 3: Process
    .thenAccept(result -> System.out.println(result))       // Step 4: Consume
    .exceptionally(ex -> {                                  // Handle any error
        System.out.println("Error: " + ex.getMessage());
        return null;
    });
```

---

### Combining Multiple Futures

```java
CompletableFuture<String> weather = CompletableFuture.supplyAsync(() -> {
    try { Thread.sleep(1000); } catch (Exception e) {}
    return "Sunny";
});

CompletableFuture<String> news = CompletableFuture.supplyAsync(() -> {
    try { Thread.sleep(1500); } catch (Exception e) {}
    return "Top Headlines Ready";
});

// Wait for BOTH to complete, then combine their results
CompletableFuture<String> combined = weather.thenCombine(news,
    (w, n) -> "Weather: " + w + " | News: " + n);

System.out.println(combined.get()); // Weather: Sunny | News: Top Headlines Ready
```

---

### `anyOf` vs `allOf`

```java
// allOf — wait for ALL futures to complete
CompletableFuture.allOf(future1, future2, future3).join();
System.out.println("All done!");

// anyOf — proceed as soon as ANY one completes
CompletableFuture.anyOf(future1, future2, future3)
    .thenAccept(result -> System.out.println("First to finish: " + result));
```

---

## 🧲 `volatile` Keyword — Visibility Guarantee

Without `volatile`, each thread may cache a variable's value in its local CPU cache. Changes made by one thread may **not be visible** to other threads.

```java
class FlagExample {
    private volatile boolean running = true; // Without volatile, the loop might never stop!

    public void stop() {
        running = false; // Written by one thread
    }

    public void run() {
        while (running) { // Read by another thread
            // do work
        }
        System.out.println("Stopped.");
    }
}
```

> `volatile` guarantees **visibility** but NOT **atomicity**. It's good for simple flags, not for compound operations like `count++`.

---

## 🔢 Atomic Classes — Lock-Free Thread Safety

For simple operations like counter increments, Java provides atomic classes that use **low-level CPU instructions** (CAS — Compare And Swap) instead of locks. They're faster than `synchronized`.

```java
import java.util.concurrent.atomic.*;

class Counter {
    AtomicInteger count = new AtomicInteger(0);

    void increment() {
        count.incrementAndGet(); // Atomic, no lock needed
    }

    int getCount() {
        return count.get();
    }
}
```

Other useful atomic classes:
- `AtomicLong` — for long values
- `AtomicBoolean` — for boolean flags
- `AtomicReference<T>` — for object references

---

## 📦 Thread-Safe Collections

Java's standard collections (`ArrayList`, `HashMap`) are **NOT thread-safe**. Use these alternatives:

| Use Case | Thread-Safe Collection |
|---|---|
| List with concurrent reads/writes | `CopyOnWriteArrayList` |
| HashMap for concurrent access | `ConcurrentHashMap` |
| Queue for producer-consumer | `LinkedBlockingQueue` |
| Deque for dual-end access | `ConcurrentLinkedDeque` |

```java
import java.util.concurrent.*;

// ConcurrentHashMap — allows concurrent reads and partial-lock writes
Map<String, Integer> map = new ConcurrentHashMap<>();
map.put("key", 1);

// LinkedBlockingQueue — classic producer-consumer
BlockingQueue<String> queue = new LinkedBlockingQueue<>(10);

// Producer
new Thread(() -> {
    try { queue.put("Task 1"); } catch (InterruptedException e) {}
}).start();

// Consumer
new Thread(() -> {
    try {
        String task = queue.take(); // Blocks if queue is empty
        System.out.println("Processing: " + task);
    } catch (InterruptedException e) {}
}).start();
```

---

## 🎯 Quick Cheat Sheet

| Concept | When to Use |
|---|---|
| `synchronized` | Simple mutual exclusion for a method or block |
| `ReentrantLock` | Need `tryLock()`, timeout, or fairness |
| `ReadWriteLock` | Many readers, few writers |
| `volatile` | Simple visibility flag between threads |
| `AtomicInteger` | Fast lock-free counters |
| `ExecutorService` | Managing a pool of threads for multiple tasks |
| `CountDownLatch` | Wait for N events to happen (one-time) |
| `CyclicBarrier` | All threads must reach a point before continuing (reusable) |
| `CompletableFuture` | Non-blocking async tasks with chaining/callbacks |
| `ConcurrentHashMap` | Shared map across threads |
| `BlockingQueue` | Producer-consumer patterns |

---

## 🏆 Best Practices from an Experienced Java Developer

1. **Prefer `ExecutorService` over raw threads.** Managing threads manually is error-prone. Let the framework do it.

2. **Keep synchronized blocks as short as possible.** Only lock the minimum code that truly needs protection. Longer locks = worse performance.

3. **Always release locks in a `finally` block.** If you forget, a thrown exception leaves the lock held forever.

4. **Avoid `Thread.stop()` and `Thread.suspend()`.** They're deprecated and unsafe. Use flags or interrupts to stop threads gracefully.

5. **Use `volatile` for simple flags, `AtomicInteger` for counters, `synchronized`/`Lock` for complex operations.**

6. **Never call `Thread.run()` directly.** Always use `Thread.start()`.

7. **Design for deadlock prevention from the start.** Consistent lock ordering is the simplest fix.

8. **Prefer `CompletableFuture` over raw `Future`.** It's non-blocking and composable — cleaner async code.

9. **Use thread-safe collections** (`ConcurrentHashMap`, `CopyOnWriteArrayList`) instead of wrapping regular collections with `synchronized`.

10. **Name your threads.** `new Thread(task, "Order-Processor-1")` makes debugging and log reading much easier.

---

*Notes by: ADITYA KUMAR | Topic: Java Multithreading | Level: Intermediate → Advanced*