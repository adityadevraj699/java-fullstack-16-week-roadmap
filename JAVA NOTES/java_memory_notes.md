# Java Memory Management — Developer Notes
### Topic: Heap · Stack · GC Types · Memory Tuning
> Written from the perspective of a working Java developer.

---

## 1. THE BIG PICTURE — JVM MEMORY LAYOUT

When your Java process starts, the JVM carves up native memory into several regions.
As a developer you care most about two: **Heap** and **Stack**.

```
┌─────────────────────────────────────────────────────────────┐
│                        JVM Process Memory                   │
│                                                             │
│  ┌──────────────────────┐   ┌──────────────────────────┐   │
│  │        HEAP           │   │   STACK (per thread)     │   │
│  │  (shared, dynamic)    │   │   (private, fixed-size)  │   │
│  │                       │   │                          │   │
│  │  Young Gen            │   │  Frame N  ← current      │   │
│  │  ├─ Eden              │   │  Frame N-1               │   │
│  │  ├─ Survivor S0       │   │  Frame N-2               │   │
│  │  └─ Survivor S1       │   │  ...                     │   │
│  │                       │   └──────────────────────────┘   │
│  │  Old Gen (Tenured)    │                                   │
│  │                       │   ┌──────────────────────────┐   │
│  │  (Metaspace →         │   │   METASPACE (off-heap)   │   │
│  │   off-heap since J8)  │   │   Class metadata,        │   │
│  └──────────────────────┘   │   static fields, method  │   │
│                              │   bytecode               │   │
│                              └──────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. THE STACK

### What lives here?
- **Stack Frames** — one per method call, created on entry, destroyed on return.
- Each frame contains:
  - Local variables (primitives stored directly: `int`, `long`, `double`, etc.)
  - Object **references** (the pointer, NOT the object itself)
  - Operand stack (used by bytecode instructions)
  - Return address

### Key Characteristics
| Property        | Detail                                              |
|----------------|------------------------------------------------------|
| Size           | Small — default 512 KB to 1 MB per thread            |
| Lifecycle      | LIFO — frame pushed on call, popped on return        |
| Thread safety  | Completely private — no sharing between threads      |
| GC involvement | NONE — stack memory is managed automatically by scope |
| Speed          | Extremely fast — just a pointer increment/decrement  |

### Developer Implications
```java
void calculate() {
    int x = 10;           // x lives on the Stack (primitive)
    String s = "hello";   // reference 's' is on Stack,
                          // actual String object is on Heap
    doWork(x, s);         // new frame pushed onto Stack
}                         // frame popped → x, s reference gone
```

### StackOverflowError
- Caused by **infinite/deep recursion** — stack runs out of frame space.
- `-Xss` flag controls stack size per thread.
  ```bash
  java -Xss2m MyApp   # 2 MB stack per thread
  ```
- **Tip:** More threads = more total stack memory. Watch out in high-concurrency apps.

---

## 3. THE HEAP

### What lives here?
- **All objects** created with `new`.
- **Arrays** (they are objects in Java).
- **Instance variables** (even primitives inside an object live on the Heap).

### Heap Generations (Classic Model)

#### 3.1 Young Generation
This is where ALL new objects are born.

```
Eden Space  →  (Minor GC)  →  Survivor S0 / S1  →  (Promotion)  →  Old Gen
```

- **Eden Space**: All `new` allocations land here. Objects are short-lived.
- **Survivor Spaces (S0, S1)**: Objects that survive a Minor GC are copied here.  
  Only ONE survivor space is active at a time (the other is empty — always).
- **Aging**: Each GC cycle increments an object's "age counter". When age ≥ threshold (default 15), it's **promoted** to Old Gen.
- Minor GC is **Stop-The-World (STW)** but very fast (milliseconds).

```java
// This string lives in Eden briefly and is eligible for GC after the method
void shortLived() {
    String temp = new String("I'll die soon");
    System.out.println(temp);
}   // temp is unreachable here → collected in next Minor GC
```

#### 3.2 Old Generation (Tenured)
- Long-lived objects promoted from Young Gen.
- **Major GC / Full GC** collects here — slower, longer STW pauses.
- Examples of long-lived objects:
  - Caches (`HashMap` stored as a static field)
  - Application context (Spring beans)
  - Thread pools

#### 3.3 Metaspace (Java 8+)
- Replaced PermGen (which had a fixed size and caused `OutOfMemoryError: PermGen space`).
- Stores: class metadata, method bytecode, static variables.
- Lives in **native memory** — grows dynamically by default.
- **Flag:** `-XX:MaxMetaspaceSize=256m` (set a cap to prevent runaway growth).

### Heap Flags Summary
```bash
-Xms512m              # Initial heap size
-Xmx2g                # Maximum heap size
-Xmn512m              # Young Gen size (mn = min/new)
-XX:NewRatio=2        # Old:Young ratio → OldGen = 2x YoungGen
-XX:SurvivorRatio=8   # Eden:Survivor ratio → Eden = 8x each Survivor
-XX:MaxMetaspaceSize=256m
```

> **Best Practice:** Set `-Xms` = `-Xmx` in production to prevent heap resizing pauses.

---

## 4. GARBAGE COLLECTION — THE THEORY

### How does GC decide what to collect?
**Reachability**, not reference counting.

Starting from **GC Roots**, the GC traces all object references.
Anything NOT reachable = garbage.

**GC Roots include:**
- Local variables in active stack frames
- Static variables
- Active threads
- JNI references

```
GC Root (static field)
    └── HashMap (Old Gen)
            └── MyBean (Old Gen)
                    └── String (Old Gen) ← reachable, NOT collected

UnreachableObject ← no GC root points here → COLLECTED
```

### STW (Stop-The-World) Pauses
- GC must pause all application threads during certain phases to get a consistent heap snapshot.
- STW = **the enemy of low-latency applications**.
- Modern GCs are designed to minimize STW pauses.

---

## 5. GC ALGORITHMS — TYPES IN DEPTH

### 5.1 Serial GC
```bash
-XX:+UseSerialGC
```
- **Single-threaded** — uses one CPU for both Minor and Major GC.
- **Use case:** CLI tools, small apps, single-core machines, microcontainers with tiny heaps.
- Pros: Low overhead, simple.
- Cons: Long STW pauses — completely unacceptable for server applications.

---

### 5.2 Parallel GC (Throughput Collector) ← Default pre-Java 9
```bash
-XX:+UseParallelGC
-XX:ParallelGCThreads=4
```
- Uses **multiple threads** for both Minor and Major GC.
- Goal: **Maximum throughput** — do as much work as possible, accept STW pauses.
- **Use case:** Batch processing, ETL pipelines, jobs that care about CPU throughput not latency.
- Pros: Great throughput, simple tuning.
- Cons: STW pauses can be noticeable (hundreds of ms on large heaps).

---

### 5.3 CMS — Concurrent Mark Sweep (Deprecated in J9, Removed in J14)
```bash
-XX:+UseConcMarkSweepGC
```
- First GC to run **concurrently** with your app threads (mostly).
- **Phases:**
  1. Initial Mark (STW) — find GC roots
  2. Concurrent Mark — trace references (concurrent, app runs)
  3. Remark (STW) — fix up concurrent mark changes
  4. Concurrent Sweep — free memory (concurrent, app runs)
- **Problems:**
  - **Fragmentation** — doesn't compact memory → leads to `OutOfMemoryError` even with free space.
  - **Floating garbage** — objects created during concurrent phase missed until next cycle.
  - CPU-intensive — steals cycles from your application.
- **Historical note:** CMS was a milestone but G1 superseded it completely.

---

### 5.4 G1 GC (Garbage First) ← Default from Java 9+
```bash
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200   # Target pause goal (soft)
-XX:G1HeapRegionSize=16m
```

**The big idea:** Instead of fixed generational areas, G1 divides the heap into equal-sized **regions** (1–32 MB each). Regions are dynamically assigned roles.

```
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ E  │ S  │ O  │ O  │ E  │ H  │ E  │ O  │
├────┼────┼────┼────┼────┼────┼────┼────┤
│ O  │ E  │ S  │ O  │ O  │ E  │ O  │ O  │
└────┴────┴────┴────┴────┴────┴────┴────┘
E=Eden  S=Survivor  O=Old  H=Humongous
```

- **Humongous regions:** For objects > 50% of a region size. Allocated directly in Old Gen-like regions.
- **Collection:** G1 collects regions with the MOST garbage first ("Garbage First" = the name).
- **Mixed GC:** After concurrent marking, G1 does "mixed" collections that clean both Young and selected Old regions.
- **Compacting:** G1 copies live objects during collection → no fragmentation.

**GC Phases:**
1. Young-only GC (Minor GC with STW) — keeps Eden/Survivor clean
2. Concurrent Marking Cycle — runs concurrently, finds live objects in Old Gen
3. Mixed GC — cleans Young + selected Old regions

**Tuning G1:**
```bash
-XX:MaxGCPauseMillis=100        # Target pause (G1 adapts region set)
-XX:G1NewSizePercent=20         # Min Young Gen %
-XX:G1MaxNewSizePercent=60      # Max Young Gen %
-XX:G1MixedGCCountTarget=8      # Spread mixed GCs over 8 cycles
-XX:InitiatingHeapOccupancyPercent=45  # Start concurrent mark at 45% heap
```

**Sweet spot:** General-purpose server apps, heap 4 GB – 100 GB, targeting sub-200ms pauses.

---

### 5.5 ZGC (Z Garbage Collector) — Java 15+ (Production)
```bash
-XX:+UseZGC
-XX:SoftMaxHeapSize=12g
```
- **Goal:** Sub-millisecond pause times regardless of heap size (tested up to **16 TB**!).
- Almost entirely concurrent — STW pauses are only for root scanning (< 1ms).
- Uses **colored pointers** and **load barriers** to track object state during concurrent operation.
- **Trade-off:** Higher CPU usage due to concurrent work; slightly lower throughput vs G1.
- **Use case:** Latency-sensitive apps — real-time trading, gaming servers, APIs with SLA < 10ms.

**Java 21+ ZGC improvements:** Generational ZGC (`-XX:+UseZGC -XX:+ZGenerational`) — adds generational collection to ZGC for even better throughput.

---

### 5.6 Shenandoah GC — Java 12+ (OpenJDK / Red Hat)
```bash
-XX:+UseShenandoahGC
```
- Similar goals to ZGC — ultra-low pause times.
- Also mostly concurrent; uses **forwarding pointers** (different technique from ZGC).
- Pause times scale with **root set size**, not heap size.
- Available in OpenJDK but NOT in Oracle JDK (Red Hat contribution).
- **Use case:** Same as ZGC — latency-critical workloads.

---

### GC Comparison Table

| GC         | Throughput | Pause Time | Heap Size     | Default   | Best Use Case               |
|-----------|------------|------------|---------------|-----------|-----------------------------|
| Serial    | Low        | High       | Small         | No        | Single-core, tiny apps      |
| Parallel  | High       | Medium-High| Any           | Java 8-   | Batch/ETL, throughput first |
| CMS       | Medium     | Low-Medium | Medium        | Removed   | Legacy low-latency          |
| G1        | High       | Medium     | 4 GB – 100 GB | Java 9+   | General server apps         |
| ZGC       | Medium-High| Very Low   | Up to 16 TB   | Java 21+* | Ultra-low latency           |
| Shenandoah| Medium     | Very Low   | Any           | No        | Low latency (OpenJDK)       |

---

## 6. MEMORY TUNING — PRACTICAL GUIDE

### 6.1 Tuning Methodology (Never Guess)
```
Profile First → Identify Bottleneck → Tune One Thing → Measure → Repeat
```
**Tools:**
- `jstat -gcutil <pid> 1000` — live GC stats every 1 second
- `jmap -heap <pid>` — heap summary
- `jcmd <pid> GC.heap_info`
- **VisualVM / JConsole** — visual monitoring
- **Java Flight Recorder (JFR)** + Mission Control — gold standard
  ```bash
  java -XX:+FlightRecorder -XX:StartFlightRecording=duration=60s,filename=recording.jfr MyApp
  ```
- **GC log analysis:** gc-easy.io, GCViewer

### 6.2 Enable GC Logging (Always in Production)
```bash
# Java 9+
-Xlog:gc*:file=/var/log/app/gc.log:time,uptime,level,tags:filecount=5,filesize=20m

# Java 8
-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/var/log/app/gc.log
-XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=20m
```

### 6.3 Heap Sizing Rules of Thumb
- **Live data set** = amount of heap used after a Full GC.
- Set `-Xmx` to **3–4× your live data set** (gives GC room to breathe).
- If you see frequent Full GCs → heap is too small OR you have a memory leak.
- If Old Gen is always < 30% after Full GC → heap may be oversized (wasted memory).

```bash
# Example: Live set = 512MB → Xmx should be ~1.5–2GB
java -Xms2g -Xmx2g -XX:+UseG1GC -XX:MaxGCPauseMillis=200 MyApp
```

### 6.4 Common Memory Problems & Fixes

#### Problem 1: Frequent Minor GC (Eden too small)
```bash
# Symptom: Minor GC every few seconds
# Fix: Increase Young Gen
-Xmn1g        # OR
-XX:NewRatio=1  # Old:Young = 1:1 (more Young space)
```

#### Problem 2: Objects Promoted Too Early
```bash
# Symptom: Old Gen fills up fast, frequent Major GC
# Fix: Increase survivor space or tenuring threshold
-XX:MaxTenuringThreshold=15
-XX:SurvivorRatio=6   # Larger survivors
```

#### Problem 3: Humongous Object Allocation (G1)
```bash
# Symptom: G1 logs show "Humongous allocation"
# Fix: Increase region size to be > 2x your large object size
-XX:G1HeapRegionSize=32m
```

#### Problem 4: High GC Overhead / Long STW Pauses
```bash
# Option A: Switch to ZGC
-XX:+UseZGC

# Option B: Tune G1 pause target
-XX:MaxGCPauseMillis=100

# Option C: Reduce allocation rate (code-level fix — most effective)
# → Use object pooling, reuse buffers, avoid unnecessary object creation
```

#### Problem 5: OutOfMemoryError: Java heap space
- First confirm: is it a leak or undersizing?
- Use heap dump: `jmap -dump:format=b,file=heap.hprof <pid>`
- Analyze with **Eclipse MAT** or **IntelliJ Heap Analyzer**.
- Common leaks: unbounded caches, static collections, event listeners not removed, ThreadLocal not cleaned.

#### Problem 6: OutOfMemoryError: Metaspace
```bash
# Fix: Set a cap or increase Metaspace
-XX:MaxMetaspaceSize=512m
# OR investigate dynamic class loading / classloader leaks (common with OSGi, app servers)
```

### 6.5 Code-Level Memory Optimizations (The Most Effective Tuning)

```java
// BAD: Creates many short-lived String objects
String result = "";
for (int i = 0; i < 10000; i++) {
    result += "item" + i;   // Each += creates new String on heap
}

// GOOD: Single StringBuilder, stays in Eden, minimal GC pressure
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append("item").append(i);
}
String result = sb.toString();
```

```java
// BAD: Boxing primitives in tight loops → many Integer objects on heap
List<Integer> nums = new ArrayList<>();
for (int i = 0; i < 1_000_000; i++) nums.add(i);  // 1M Integer objects!

// BETTER: Use primitive collections (Eclipse Collections, Trove, Koloboke)
IntList nums = IntLists.mutable.empty();
for (int i = 0; i < 1_000_000; i++) nums.add(i);  // No boxing
```

```java
// GOOD: Reuse expensive objects — ByteBuffer for I/O
// Pre-allocate and reuse
private static final ThreadLocal<ByteBuffer> BUFFER =
    ThreadLocal.withInitial(() -> ByteBuffer.allocateDirect(8192));

// WARNING: Always clean ThreadLocal in thread pools!
try {
    ByteBuffer buf = BUFFER.get();
    buf.clear();
    // use buf
} finally {
    // If using thread pools, remove to prevent memory leak
    BUFFER.remove();
}
```

```java
// Weak/Soft references for caches — let GC reclaim under pressure
Map<Key, SoftReference<ExpensiveObject>> cache = new HashMap<>();

// OR use a proper cache library: Caffeine (best), Guava Cache
Cache<Key, Value> cache = Caffeine.newBuilder()
    .maximumSize(10_000)
    .expireAfterWrite(Duration.ofMinutes(10))
    .build();
```

### 6.6 Off-Heap Memory
For extreme performance (low-latency, large data), bypass GC entirely:
```java
// Direct ByteBuffer — allocated outside heap
ByteBuffer buf = ByteBuffer.allocateDirect(1024 * 1024 * 100); // 100 MB off-heap
// GC doesn't manage this → no GC pauses for this data
// Must be careful: freed when buffer is GC'd, or use Cleaner API
```

Libraries: **Chronicle Map**, **Agrona**, **MapDB** for off-heap data structures.

---

## 7. QUICK REFERENCE — JVM FLAGS CHEAT SHEET

```bash
# === HEAP ===
-Xms2g                          # Initial heap
-Xmx4g                          # Max heap (set = Xms in prod)
-Xmn1g                          # Young Gen size
-XX:NewRatio=2                  # OldGen / YoungGen ratio
-XX:SurvivorRatio=8             # Eden / each Survivor ratio
-XX:MaxTenuringThreshold=15     # Max GC cycles before promotion

# === METASPACE ===
-XX:MetaspaceSize=128m          # Initial Metaspace
-XX:MaxMetaspaceSize=512m       # Max Metaspace

# === GC SELECTION ===
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+UseG1GC
-XX:+UseZGC
-XX:+UseShenandoahGC

# === G1 TUNING ===
-XX:MaxGCPauseMillis=200
-XX:G1HeapRegionSize=16m
-XX:InitiatingHeapOccupancyPercent=45
-XX:G1MixedGCCountTarget=8
-XX:G1NewSizePercent=20
-XX:G1MaxNewSizePercent=60

# === ZGC ===
-XX:SoftMaxHeapSize=8g          # Soft heap limit (ZGC tries to stay under)
-XX:ZCollectionInterval=5       # Force GC every 5 seconds (use carefully)

# === STACK ===
-Xss512k                        # Stack size per thread

# === GC LOGGING (Java 9+) ===
-Xlog:gc*:file=/logs/gc.log:time,uptime:filecount=5,filesize=20m

# === OOM DIAGNOSIS ===
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/dumps/heap.hprof
-XX:+ExitOnOutOfMemoryError     # Fail fast rather than limp along

# === PERFORMANCE ===
-XX:+UseStringDeduplication     # G1 only — deduplicates String objects
-XX:+OptimizeStringConcat       # JIT optimization for string concat
-XX:+UseCompressedOops          # Default on 64-bit, <32GB heap → saves memory
```

---

## 8. MENTAL MODEL — THINKING AS A DEVELOPER

```
Every object you create is a GC liability.
The best GC tuning is writing code that generates less garbage.

Ask yourself:
  → Does this object need to be on the heap?       (use primitives/stack where possible)
  → How long will this object live?                (short-lived = good, avoid premature promotion)
  → Am I holding a reference longer than needed?   (= memory leak root cause)
  → Can I reuse this object?                       (pooling, StringBuilder, ByteBuffer)
  → Is this collection bounded?                    (unbounded = leak waiting to happen)
```

---

## 9. INTERVIEW / QUICK RECALL

| Question | Answer |
|---------|--------|
| Where do objects live? | Heap |
| Where do local primitives live? | Stack |
| What triggers Minor GC? | Eden is full |
| What triggers Major/Full GC? | Old Gen is full or explicit `System.gc()` |
| What is GC root? | Starting point for reachability tracing |
| What replaced PermGen? | Metaspace (Java 8+) |
| Default GC in Java 9+? | G1GC |
| Lowest pause GC? | ZGC (sub-millisecond) |
| Best GC for batch jobs? | ParallelGC (throughput) |
| How to diagnose OOM? | Heap dump + Eclipse MAT |
| StackOverflowError cause? | Deep/infinite recursion |

---

*These notes cover the full mental model a Java developer needs — from object allocation to GC internals to production tuning. Bookmark this. You'll come back to it.*
