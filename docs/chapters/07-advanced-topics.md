# Chapter 7 — Advanced Topics

!!! abstract "Chapter at a glance"
    **Coverage:** parallel processing models (SISD/SIMD/MISD/MIMD) · multicore · GPU computing · low-level data structures · introduction to operating systems.

---

## Learning outcomes

1. Classify computer architectures using **Flynn's taxonomy**.
2. Describe multicore and GPU architectures and their trade-offs.
3. Explain how **stacks, queues, and linked lists** are implemented in hardware-adjacent contexts.
4. Describe the role of an operating system: process management, memory management, file systems, and scheduling.

---

## 7.1 Flynn's taxonomy

!!! abstract "Flynn's taxonomy (1966)"
    Four classes of computer architecture based on the number of **instruction streams** and **data streams**:

    - **SISD** — single instruction, single data — the classic Von Neumann machine.
    - **SIMD** — single instruction, multiple data — vector processors, GPUs.
    - **MISD** — multiple instruction, single data — rare, mostly theoretical (systolic arrays).
    - **MIMD** — multiple instruction, multiple data — multicore CPUs, distributed clusters.

| Class | Example |
|---|---|
| **SISD** | Single-core Pentium |
| **SIMD** | Intel AVX (256-bit vectors), NVIDIA GPU warp |
| **MISD** | Systolic array in a specialised signal processor |
| **MIMD** | Dual-core ARM Cortex-A, AMD Ryzen, cluster |

---

## 7.2 Multicore

A **multicore** processor places two or more CPU cores on a single chip, sharing memory (L3) and I/O.

**Implications:**

- Parallel execution of **threads** requires explicit programming (pthreads, OpenMP, std::thread).
- **Cache coherence** — all cores must see a consistent view of memory (MESI, MOESI protocols).
- **Amdahl's law** caps the speedup from additional cores.

Modern chips have **heterogeneous cores** — e.g., ARM big.LITTLE with performance and efficiency cores.

---

## 7.3 GPU computing

A **GPU** is a **many-core** architecture designed for **data-parallel** workloads:

- 1000s of simple **SIMT** (single instruction, multiple threads) lanes.
- Large **vector** processing units.
- Very high memory bandwidth.
- Optimised for throughput, not latency.

**Programming models:**

- **CUDA** (NVIDIA)
- **OpenCL** (portable)
- **SYCL** (Khronos)
- **ROCm** (AMD)

**When to use a GPU.** Problems that:

- Have abundant **data parallelism** (same operation on many data items).
- Are **arithmetic-heavy** (computation dominates memory traffic).
- Can amortise **kernel launch** and **data transfer** overhead.

**Not a fit.** Branch-heavy, irregular, or latency-sensitive code.

---

## 7.4 Low-level data structures

### 7.4.1 Stack

Last-in, first-out (LIFO). Implemented with:

- **A pointer to top** (SP) and an array in memory.
- **Push**: `SP ← SP − 4; M[SP] ← value`.
- **Pop**: `value ← M[SP]; SP ← SP + 4`.

The stack is used for **function calls**, **local variables**, and **return addresses**.

### 7.4.2 Queue

First-in, first-out (FIFO). Implemented with **head** and **tail** pointers, or as a **circular buffer**.

### 7.4.3 Linked list

A sequence of nodes; each node has a **data field** and a **next pointer**.

- **Insertion at head:** O(1).
- **Search:** O(n).
- **Memory overhead:** one pointer per node.

Linked lists are used in **free lists** (memory allocators) and **page tables** (inverted page tables).

---

## 7.5 Introduction to operating systems

An **operating system** is the software layer between applications and hardware. Its main responsibilities:

- **Process management** — create, schedule, and terminate processes and threads.
- **Memory management** — virtual memory, paging, segmentation.
- **File systems** — organise persistent data.
- **I/O management** — drivers, buffering, device scheduling.
- **Security** — authentication, access control.

**Process scheduling algorithms:** FCFS, SJF, Round Robin, priority (see the OS course for full treatment).

**Virtual memory:** the OS uses the **TLB** and **page tables** to give each process the illusion of a private address space.

---

## 7.6 Case studies

### 7.6.1 x86 (Intel Core, AMD Zen)

- CISC ISA, variable-length instructions (1–15 bytes).
- Internally decoded into **RISC micro-operations**.
- Deep pipeline (14–20 stages), out-of-order execution, speculative execution.
- Massive L1/L2/L3 caches.

### 7.6.2 ARM (Cortex-A series)

- RISC ISA, fixed-length (4 bytes, Thumb 2 bytes).
- Shallower pipeline than x86 (8–15 stages).
- **Energy-efficient** — dominant in mobile.
- Extensively licensed to SoC vendors (Qualcomm, Apple, MediaTek).

---

## 7.7 Quiz

1. Classify the following under Flynn's taxonomy: (a) a four-core CPU; (b) a GPU warp; (c) a single-core Pentium; (d) an NVIDIA tensor core.
2. Explain why a GPU is good for matrix multiplication but poor for a web server.
3. Describe a stack-based function call — what is pushed and popped?
4. Compare x86 and ARM on ISA complexity and energy efficiency.

---

## Answer key

??? success "Click to reveal"

    1. (a) MIMD; (b) SIMD; (c) SISD; (d) SIMD.
    2. Matrix multiplication is massively data-parallel and arithmetic-heavy; a web server has irregular, branch-heavy, latency-sensitive work.
    3. Push return address, saved registers, local variables; pop them on return.
    4. x86 is CISC (complex, high performance); ARM is RISC (simple, energy-efficient).

---

## Further reading

- Stallings, §17.1–§18.4.
- Patterson & Hennessy, §6.1–§6.6.
- Mano, §13.1–§13.4.