# Syllabus

Full course outline for **ECEG-3163 — Computer Architecture and Organization**.

---

## Course identification

| Field | Value |
|---|---|
| **Course code** | ECEG-3163 |
| **Course title** | Computer Architecture and Organization |
| **Credit hours (ECTS)** | 5 — 2 lecture + 3 tutorial/lab per week |
| **Degree program** | BSc Electrical Engineering (Focus Area) |
| **Module** | Computer Architecture and Programming |
| **Semester** | VI/6 |
| **Status** | Compulsory |
| **Pre-requisites** | Digital Logic Design |
| **Instructor** | *(insert)* |

---

## Student workload

| Lecture | Tutorial / Lab | Home study |
|:---:|:---:|:---:|
| 2 h | 3 h | ~5 h |

Total: ~10 hours per week × 18 weeks = **~180 hours** — consistent with 5 ECTS.

---

## Course description

The course introduces the architecture and organization of a computer system and its components. Students learn how the CPU, memory, and I/O subsystems interact; how instructions are represented, decoded, and executed; and how the performance of a computer system is measured and improved. Students design and simulate a basic computer system (Logisim project).

---

## Course objectives and competences

Upon completion of this course, students will be able to:

1. **Introduce** the architecture and organization of a computer system and its components.
2. **Design and simulate** a basic computer system.

---

## Learning outcomes

| # | Outcome | Assessed by |
|---|---|---|
| LO1 | Distinguish organization from architecture and describe the evolution of computing | A1, Test 1 |
| LO2 | Perform signed-number and floating-point arithmetic at the bit level | A2, Test 1 |
| LO3 | Analyse instruction formats and addressing modes; design simple instruction sets | A3, Test 1 |
| LO4 | Design a pipelined CPU datapath and analyse its hazards and speedup | A4, Test 2 |
| LO5 | Design a hardwired or microprogrammed control unit | A5, Test 2 |
| LO6 | Analyse cache and memory hierarchies; compute AMAT and design cache parameters | A6, Final |
| LO7 | Design I/O subsystems using programmed, interrupt, and DMA techniques | A7, Final |
| LO8 | Describe parallel processing models and their trade-offs | Project, Final |

---

## Course content

**Chapter 1 — Introduction and Performance**

- Organization vs architecture
- Computer evolution (generations, Moore's law)
- Von Neumann model
- Basic computer components (CPU, memory, I/O, buses)
- Instruction cycle (fetch-decode-execute)
- Performance metrics: clock rate, CPI, MIPS, FLOPS
- The performance equation
- Amdahl's law
- Benchmarks (SPEC, Geekbench)

**Chapter 2 — Computer Arithmetic**

- Number representation: sign-magnitude, 1's complement, 2's complement, biased
- Integer addition and subtraction
- Overflow detection
- Multiplication: shift-add, Booth's algorithm
- Division: restoring, non-restoring
- Floating-point representation: IEEE 754 (single, double, extended)
- Floating-point arithmetic (addition, multiplication)
- Rounding and exceptions

**Chapter 3 — Instruction Sets and CPU Structure**

- Instruction set taxonomy (stack, accumulator, general-purpose)
- Instruction formats (R, I, J)
- Addressing modes (immediate, direct, indirect, register, register indirect, indexed, base+offset, relative)
- Operand types and operations
- CPU register organization (PC, IR, MAR, MBR, AC, general-purpose)
- Register transfer notation (RTN)
- Datapath design
- RISC vs CISC

**Chapter 4 — Pipelining and Control Unit**

- Pipeline concept and speedup
- Five-stage RISC pipeline
- Hazards: structural, data, control
- Forwarding, stalling, branch prediction
- Superscalar and out-of-order (brief)
- Control unit: hardwired vs microprogrammed
- Microinstruction formats
- Wilkes' control unit model
- Sequencing and control

**Chapter 5 — Memory Systems**

- Memory hierarchy and locality of reference
- Memory parameters: capacity, access time, cycle time, bandwidth
- SRAM, DRAM, ROM, flash
- Main memory organization and interleaving
- Cache organization: direct-mapped, set-associative, fully associative
- Cache replacement and write policies
- AMAT and cache performance
- Virtual memory and paging
- TLB
- Secondary memory: HDD and SSD

**Chapter 6 — I/O Systems**

- I/O devices (character, block, network)
- I/O modules and interfaces
- Programmed I/O
- Interrupt-driven I/O
- Direct Memory Access (DMA)
- Buses (system, I/O, PCIe)
- Disk I/O performance
- I/O buffering

**Chapter 7 — Advanced Topics**

- Parallel processing: SISD, SIMD, MISD, MIMD
- Multicore processors
- GPU computing (CUDA, OpenCL)
- Low-level data structures: stacks, queues, linked lists
- Introduction to operating systems
- Case studies: x86, ARM

---

## Laboratory / tutorial exercises

| Lab | Topic |
|---|---|
| L1 | Logisim — building an ALU |
| L2 | Logisim — building a register file |
| L3 | MARS — MIPS assembly basics |
| L4 | MARS — writing loops, arrays, and procedures in assembly |
| L5 | Logisim — assembling a single-cycle CPU datapath |
| L6 | Logisim — pipelining the CPU (with hazards) |
| L7 | Ripes — pipeline visualization and hazard analysis |
| L8 | Cache simulation (MARS or a Python simulator) |

---

## Project (part of the 50% continuous assessment)

A semester-long project in which students:

1. Design a **simple 8-bit or 16-bit CPU** in Logisim.
2. Implement the instruction set (a subset of MIPS or a custom ISA).
3. Support arithmetic (ADD, SUB), logic (AND, OR), memory (LOAD, STORE), and branching.
4. Demonstrate the CPU by running a small test program.
5. Extend to a **pipelined** version (optional, extra credit).

**Deliverables:** Logisim circuit file, test programs, written report, and demonstration.

---

## Assessment

| Component | Weight |
|---|:---:|
| Continuous assessment (assignments + tests + project) | 50% |
| Final examination | 50% |
| **Total** | **100%** |

---

## Textbook and references

**Textbook.**

- **Stallings, W.**, *Computer Organization and Architecture: Designing for Performance*, 10th ed., Pearson, 2016.

**References.**

- **Mano, M. M.**, *Computer System Architecture*, 3rd ed., Prentice Hall, 1993.
- **Patterson, D. A. & Hennessy, J. L.**, *Computer Architecture: A Quantitative Approach*, 6th ed., Morgan Kaufmann, 2017.
- **Wilkinson, B.**, *Computer Architecture: Design and Performance*, 2nd ed., Prentice Hall, 1996.

---

## Policies

- **Attendance:** minimum **75%** lecture attendance.
- **Late work:** 10% per day, zero after 3 days.
- **Academic integrity:** all submitted work must be your own. Logisim circuits are submitted individually.