---
revealjs:
  mode: diapo
  highlight:
    light: vs
    dark:  vs2015
  autoAnimate:  true
  autoAnimateDuration: 1.0
  transition: slide
  transitionSpeed: default
  js: assets/lecture-07.js

---

# ECEG-3163

## Chapter 7 — Advanced Topics

---

## 7.1 — Flynn's Taxonomy

???+ abstract "Flynn's taxonomy (1966)"
    Four classes based on **instruction streams** and **data streams**:

    | Class | Full name | Example |
    |---|---|---|
    | **SISD** | Single Instruction, Single Data | Pentium |
    | **SIMD** | Single Instruction, Multiple Data | GPU warp, Intel AVX |
    | **MISD** | Multiple Instruction, Single Data | Systolic arrays (rare) |
    | **MIMD** | Multiple Instruction, Multiple Data | Multicore CPU, cluster |

<div class="fragment">

!!! tip "Why Flynn still matters"
    The classification is 60+ years old but captures the essential design point: does the parallelism come from **many data items** (SIMD) or **many independent tasks** (MIMD)?

</div>

---

## 7.2 — SIMD

???+ note "Single Instruction, Multiple Data"
    One instruction operates on a **vector** of data items simultaneously. Examples:
    - **Intel SSE / AVX** — 128, 256, or 512-bit vector registers.
    - **NVIDIA GPU warp** — 32 threads execute the same instruction.
    - **ARM NEON** — 128-bit vector extension.

<div class="fragment">

???+ info "When SIMD wins"
    - **Data parallel** loops — e.g., adding two arrays element by element.
    - **Arithmetic-heavy** kernels — matrix operations, image processing, cryptography.
    - **When not to use it**: irregular control flow, branch-heavy code.

</div>

<div class="fragment">

???+ note "Speedup"
    An AVX-512 instruction processes 16 single-precision floats at once — theoretically **16× the throughput** of scalar code, with no change to the algorithm.

</div>

---

## 7.3 — MIMD and Multicore

???+ note "Multiple Instruction, Multiple Data"
    Multiple independent threads, each with its own instruction stream, running on separate cores. The standard model for multicore CPUs, servers, and clusters.

<div class="fragment">

???+ info "Multicore design considerations"
    - **Cache coherence** — all cores must see a consistent memory view (MESI, MOESI protocols).
    - **Consistency model** — sequential, total-store-order, or relaxed.
    - **Interconnect** — shared L3, ring, mesh, crossbar.
    - **Heterogeneous cores** — ARM big.LITTLE pairs performance with efficiency cores.

</div>

<div class="fragment">

!!! warning "Amdahl's law caps the speedup"
    With 10% serial code, a 1000-core CPU gives at most 10× — **not** 1000×. Parallelism helps only when the serial fraction is small.

</div>

---

## 7.4 — GPU Computing

???+ note "What is a GPU?"
    A **many-core** processor with thousands of simple **SIMT** lanes (Single Instruction, Multiple Thread). Designed for **throughput**, not latency.

<div class="fragment">

???+ info "Why GPUs are fast for the right workload"
    - **Massive parallelism** — thousands of threads.
    - **High memory bandwidth** (HBM, GDDR6) — 10× a CPU's.
    - **Latency hidden** by overlapping many independent warps.

</div>

<div class="fragment">

???+ note "Programming models"
    - **CUDA** (NVIDIA)
    - **OpenCL** (portable)
    - **SYCL** (Khronos)
    - **ROCm** (AMD)

</div>

<div class="fragment">

!!! warning "When NOT to use a GPU"
    - Branch-heavy, irregular code.
    - Small data (kernel-launch overhead dominates).
    - Latency-sensitive (each request has ~microseconds of overhead).

</div>

---

## Worked example 7.1 — Flynn's taxonomy for four workloads

!!! tip "Problem:"
    Classify each workload: (a) matrix addition, (b) single-threaded editor, (c) web server, (d) loosely coupled simulation.

<div class="fragment">

???+ note "**(a) Matrix addition → SIMD**"
    Each element depends only on its counterpart. Identical operation on many data items — pure vectorisation.

</div>

<div class="fragment">

??? info "**(b) Text editor → SISD**"
    Sequential, event-driven, latency-sensitive. Parallelism offers no benefit.

</div>

<div class="fragment">

??? info "**(c) Web server → MIMD**"
    Each request is an independent task on a different core. Multicore and multi-threaded servers fit exactly.

</div>

<div class="fragment">

??? info "**(d) Loosely coupled simulation → MIMD**"
    Sub-tasks are independent, exchange data infrequently. Distributed MIMD (clusters, cloud) suits best.

</div>

<div class="fragment">

??? success "**Summary.**"
    (a) SIMD · (b) SISD · (c) MIMD · (d) MIMD.

</div>

---

## 7.5 — Low-Level Data Structures

???+ note "Stack"
    LIFO. A pointer to the top (SP) plus an array. Push: `SP ← SP−4; M[SP] ← value`. Pop: `value ← M[SP]; SP ← SP+4`.
    
    Used for **function calls**, **local variables**, and **return addresses**.

<div class="fragment">

???+ note "Queue"
    FIFO. Head and tail pointers, or a **circular buffer**. Used in **I/O request queues**, **scheduling**, and **message passing**.

</div>

<div class="fragment">

???+ note "Linked list"
    Sequence of nodes; each node has a data field and a next pointer.
    - Insert at head: O(1).
    - Search: O(n).
    - Memory overhead: one pointer per node.
    
    Used in **free lists** (memory allocators) and **page tables**.

</div>

---

## 7.6 — Introduction to Operating Systems

???+ note "What an OS does"
    - **Process management** — create, schedule, terminate processes and threads.
    - **Memory management** — virtual memory, paging, segmentation.
    - **File systems** — organise persistent data.
    - **I/O management** — drivers, buffering, scheduling.
    - **Security** — authentication, access control.

<div class="fragment">

???+ info "Scheduling algorithms"
    - **FCFS** — first-come, first-served.
    - **SJF** — shortest job first (optimal for average wait).
    - **Round Robin** — fixed time quantum.
    - **Priority** — higher priority runs first (aging avoids starvation).

</div>

<div class="fragment">

!!! tip "The OS lives on the hardware from Chapters 1–6"
    Scheduler uses the timer interrupt (Ch 6), virtual memory uses the TLB (Ch 5), context switches save/restore registers (Ch 3), and security uses isolation from protected mode (Ch 1).

</div>

---

## 7.7 — x86 vs ARM Case Study

???+ note "x86 — CISC"
    - Variable-length instructions (1–15 bytes).
    - Decoded into RISC-like **micro-ops** internally.
    - Deep pipeline (14–20 stages), out-of-order execution.
    - Massive L1/L2/L3 caches.
    - **High performance, high power.**

<div class="fragment">

???+ note "ARM — RISC"
    - Fixed-length (4 bytes, or Thumb 2 bytes).
    - Shallower pipeline (8–15 stages).
    - **Energy-efficient** — dominant in mobile.
    - Licensed to many SoC vendors.

</div>

<div class="fragment">

!!! tip "The convergence"
    Modern x86 looks like a RISC machine **inside** — the CISC decoder is just a front end. Modern ARM looks like a wide, out-of-order CPU. The distinction is increasingly about **the ISA contract**, not the microarchitecture.

</div>

---

## Chapter 7 — key takeaways

<div class="fragment">

**1.** **Flynn's taxonomy** captures the essential parallel design choice: SIMD vs MIMD.

</div>

<div class="fragment">

**2.** **SIMD** (GPU, AVX) wins on regular, data-parallel workloads with massive throughput.

</div>

<div class="fragment">

**3.** **MIMD** (multicore) wins on independent tasks — the standard for servers.

</div>

<div class="fragment">

**4.** **GPUs** deliver ~10–100× throughput on the right workloads, but are poor for irregular or latency-sensitive code.

</div>

<div class="fragment">

**5.** The **stack**, **queue**, and **linked list** are the primitive data structures that every operating system builds on.

</div>

<div class="fragment">

**6.** The **OS** integrates everything from Chapters 1–6 — scheduler on timers, virtual memory on TLB, security on privileged mode.

</div>

---

## Course wrap-up

<div class="fragment">

**The arc of ECEG-3163:**

1. **Chapter 1** — what a computer is, and how to measure it.
2. **Chapter 2** — arithmetic at the bit level.
3. **Chapter 3** — the ISA: what instructions look like.
4. **Chapter 4** — pipelining and control: making it fast.
5. **Chapter 5** — memory: hiding the latency of DRAM.
6. **Chapter 6** — I/O: hiding the latency of devices.
7. **Chapter 7** — advanced: many cores, many threads.

</div>

<div class="fragment">

!!! tip "The unifying question"
    Every chapter asks: **where is the bottleneck, and what architectural trick hides it?**
    
    - Bottleneck = arithmetic speed → faster ALU, better pipeline.
    - Bottleneck = memory latency → caches, virtual memory.
    - Bottleneck = I/O latency → DMA, buffering.
    - Bottleneck = single-core throughput → more cores, GPUs.

</div>