# Solutions — Chapter 7: Advanced Topics

[:octicons-arrow-left-24: Back to Chapter 7](../chapters/07-advanced-topics.md)

Answers to **Section G** of the [Exam Bank](../exam-bank.md).

---

## G1 — SIMD [2]

**Answer: (b)** one instruction on multiple data items.

**Why.** In SIMD, a single instruction operates on a **vector** of data — e.g., one `VADDPS` instruction adds 8 floating-point numbers to another 8 simultaneously. Examples: Intel SSE/AVX vector instructions, NVIDIA GPU warps executing one instruction per lane, systolic arrays.

**Why the others are wrong.**

- (a) SISD is the classic Von Neumann single instruction, single data.
- (c) MISD is multiple instructions on the same data — very rare.
- (d) MIMD is multiple independent threads, each with its own instruction stream (multicore CPUs).

---

## G2 — GPU throughput [2]

**Answer: (b)** uses thousands of simple cores to execute many threads in parallel.

**Why.** A GPU prioritises **throughput** over single-thread **latency**: each core is simple and many are placed on the die. Hundreds of threads are scheduled per cycle, hiding memory latency by **overlapping** many independent requests.

**Why the others are wrong.**

- (a) GPUs typically run at **lower** clock speeds than CPUs.
- (c) GPU caches are small; the throughput win comes from parallelism, not caching.
- (d) GPU memory (GDDR, HBM) is **high bandwidth** but **higher** latency than CPU DRAM — again, throughput, not latency.

---

## G3 — Flynn's taxonomy for four workloads [4]

**Workload (a) — Element-wise matrix addition.**

- **Best class:** SIMD.
- **Why:** each output element depends only on the corresponding input elements, with identical operations. Perfectly data-parallel — vectorises trivially or runs on a GPU at huge speedup.

**Workload (b) — Single-threaded text editor.**

- **Best class:** SISD.
- **Why:** the workload is sequential, event-driven, and latency-sensitive. Parallelism offers no benefit — the user waits on the UI, not on computation.

**Workload (c) — Web server handling 10,000 simultaneous clients.**

- **Best class:** MIMD.
- **Why:** each request is an independent task, executed on a different core (or a different server). Multicore CPUs and multi-threaded servers fit the MIMD model exactly.

**Workload (d) — Scientific simulation with loosely coupled sub-tasks.**

- **Best class:** MIMD.
- **Why:** sub-tasks are independent, may take variable time, and exchange data infrequently. Distributed MIMD (clusters, cloud) suits it best. If the sub-tasks were *tightly coupled* and *regular*, SIMD or a GPU would also fit — but "loosely coupled" points to MIMD.

**Summary.**

| Workload | Flynn class |
|---|---|
| (a) Matrix addition | **SIMD** |
| (b) Text editor | **SISD** |
| (c) Web server | **MIMD** |
| (d) Simulation | **MIMD** |