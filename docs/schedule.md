# Schedule

Week-by-week plan for **ECEG-3163**, 18-week semester.

---

## At a glance

| Week | Chapter | Topic | Tutorial / Lab | Assessment |
|:---:|:---:|---|---|---|
| 1 | 1 | Organization vs architecture; Von Neumann model | — | — |
| 2 | 1 | Evolution of computing; performance metrics | T1/L1 | A1 out |
| 3 | 2 | Number representation; integer arithmetic | — | — |
| 4 | 2 | Multiplication and division algorithms | T2/L1 | A1 due / A2 out |
| 5 | 2 | Floating point (IEEE 754) | — | — |
| 6 | 3 | Instruction formats; addressing modes | T3/L2 | A2 due / A3 out |
| 7 | 3 | CPU registers and datapath; RISC vs CISC | — | A3 due |
| 8 | — | **Test 1** (Chapters 1–3) | — | **15%** |
| 9 | 4 | Pipelining basics; hazards | T4/L3 | A4 out |
| 10 | 4 | Forwarding, branch prediction | — | A4 due / A5 out |
| 11 | 4 | Control unit — hardwired and microprogrammed | T5/L4 | — |
| 12 | 5 | Memory hierarchy; cache organization | — | A5 due / A6 out |
| 13 | 5 | Cache performance; virtual memory | T6/L5 | — |
| 14 | 6 | Programmed, interrupt, DMA | — | A6 due / A7 out |
| 15 | 6 | Buses; I/O performance | T7/L6 | **Test 2** (Chapters 4–6) — **15%** |
| 16 | 7 | Parallel processing; multicore; GPUs | T8/L7 | A7 due / A8 out |
| 17 | — | Review + project demos | — | Project due |
| 18 | — | **Final examination** | — | **50%** |

**Legend.** T = tutorial · L = laboratory · A = assignment · — = no activity

---

## Detail by week

### Week 1 — Chapter 1, Part 1

- **Lecture.** Organization vs architecture; the Von Neumann model; components of a computer system.
- **Reading.** Stallings §1.1–§1.3; Mano §1.1–§1.2.

### Week 2 — Chapter 1, Part 2

- **Lecture.** Computer evolution; performance equation (CPU time = IC × CPI × clock period); Amdahl's law.
- **Tutorial 1.** Performance calculations on given benchmarks.
- **Laboratory 1.** Logisim — build and simulate a basic ALU.
- **Assignment 1** released.

### Week 3 — Chapter 2, Part 1

- **Lecture.** Sign-magnitude, 1's complement, 2's complement, biased representations; integer addition and subtraction; overflow detection.
- **Reading.** Stallings §10.1–§10.3; Mano §2.1–§2.3.

### Week 4 — Chapter 2, Part 2

- **Lecture.** Shift-add multiplication; Booth's algorithm; restoring and non-restoring division.
- **Tutorial 2.** Bit-level multiplication and division traces.
- **Assignment 1 due; Assignment 2** released.

### Week 5 — Chapter 2, Part 3

- **Lecture.** IEEE 754 single and double precision; floating-point addition and multiplication; rounding; exceptions.
- **Reading.** Stallings §11.1–§11.5.

### Week 6 — Chapter 3, Part 1

- **Lecture.** Instruction set taxonomy; instruction formats (R, I, J); addressing modes.
- **Tutorial 3.** Decode given machine-code fragments.
- **Laboratory 2.** Logisim — build a register file.
- **Assignment 2 due; Assignment 3** released.

### Week 7 — Chapter 3, Part 2

- **Lecture.** CPU register organization; datapath; RISC vs CISC.
- **Reading.** Stallings §12.1–§12.5, §13.1–§13.3.
- **Assignment 3 due.**

### Week 8 — Test 1

- **Test.** 2 hours, closed book, **15%**. Covers Chapters 1–3.

### Week 9 — Chapter 4, Part 1

- **Lecture.** Pipelining concept and speedup; five-stage RISC pipeline; structural, data, control hazards.
- **Tutorial 4.** Identify hazards in a given instruction sequence.
- **Laboratory 3.** MARS — write and trace MIPS assembly.
- **Assignment 4** released.

### Week 10 — Chapter 4, Part 2

- **Lecture.** Forwarding, stalling, branch prediction; superscalar and out-of-order (brief).
- **Reading.** Stallings §14.1–§14.5; Patterson & Hennessy §4.1–§4.8.
- **Assignment 4 due; Assignment 5** released.

### Week 11 — Chapter 4, Part 3

- **Lecture.** Control unit: hardwired and microprogrammed; Wilkes' model.
- **Tutorial 5.** Design a microprogram for a simple ISA.
- **Laboratory 4.** MARS — write loops, arrays, procedures.
- **Assignment 5 due; Assignment 6** released.

### Week 12 — Chapter 5, Part 1

- **Lecture.** Memory hierarchy; SRAM, DRAM, ROM, flash; main memory organization and interleaving; direct-mapped, set-associative, fully associative caches.
- **Reading.** Stallings §4.1–§4.4.

### Week 13 — Chapter 5, Part 2

- **Lecture.** Cache replacement, write policies, AMAT and performance; virtual memory and TLB; HDD and SSD.
- **Tutorial 6.** Compute AMAT for various cache designs.
- **Laboratory 5.** Logisim — assemble a single-cycle CPU datapath.
- **Assignment 6 due; Assignment 7** released.

### Week 14 — Chapter 6

- **Lecture.** I/O modules; programmed I/O; interrupt-driven I/O; DMA; buses.
- **Reading.** Stallings §7.1–§7.6.

### Week 15 — Chapter 6 (cont.)

- **Lecture.** I/O performance and buffering; disk scheduling.
- **Tutorial 7.** Compute disk I/O throughput for given workloads.
- **Laboratory 6.** Logisim — pipeline the CPU (with hazards).
- **Test 2** (week 15, **15%**) — Chapters 4–6.
- **Assignment 7 due; Assignment 8** released.

### Week 16 — Chapter 7

- **Lecture.** SISD/SIMD/MISD/MIMD; multicore; GPU computing; low-level data structures; introduction to operating systems.
- **Tutorial 8.** Parallel-processing classification exercises.
- **Laboratory 7.** Ripes — pipeline visualization and hazard analysis.
- **Assignment 8 due; Project due.**

### Week 17 — Review

- **Lecture.** Review of all chapters; worked exam-style problems.
- **Tutorial.** Project demonstrations.
- **Project due.**

### Week 18 — Final examination

- **Exam.** 3 hours, closed book, **50%** of final grade. Covers the whole syllabus.