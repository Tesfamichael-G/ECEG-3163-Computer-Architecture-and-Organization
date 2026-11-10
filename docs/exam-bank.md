# Exam Bank — ECEG-3163 Computer Architecture and Organization

**60 questions across all seven chapters.** Use for self-assessment and exam preparation.

**Instructions (mock exam):**

- Time: **3 hours**
- Closed book; **one A4 handwritten formula sheet** allowed
- Non-programmable calculator permitted
- Total marks: **100**
- Marks per question in brackets

---

## Section A — Introduction and Performance (Ch 1) — 12 marks

### A1. Multiple choice [2]

Computer **architecture** refers to:

(a) the physical implementation of the computer
(b) attributes visible to a programmer (instruction set, data types, addressing)
(c) the CPU clock speed
(d) the operating system

### A2. Multiple choice [2]

Moore's law states that:

(a) clock speed doubles every year
(b) the number of transistors per chip doubles approximately every 18–24 months
(c) memory access time halves every 2 years
(d) CPU power consumption doubles every decade

### A3. Multiple choice [2]

The Von Neumann bottleneck refers to:

(a) the limited number of registers
(b) the transfer of instructions and data over a single bus
(c) the width of the ALU
(d) the cache size

### A4. Short answer [3]

Given a program with 10⁸ instructions, an average CPI of 2.5, and a 4 GHz clock, compute:
(a) the CPU execution time;
(b) the MIPS rating.

### A5. Short answer [3]

A program spends 60% of its time in a routine that can be made **3× faster**. Use **Amdahl's law** to compute the overall speedup.

---

## Section B — Computer Arithmetic (Ch 2) — 15 marks

### B1. Multiple choice [2]

The decimal value of the 8-bit two's-complement number `11110110` is:

(a) −10
(b) −9
(c) −11
(d) +246

### B2. Multiple choice [2]

In IEEE-754 single precision, the exponent field is **biased** by:

(a) 64
(b) 127
(c) 128
(d) 255

### B3. Multiple choice [2]

Which multiplication algorithm handles signed numbers **without** any separate sign-handling step?

(a) shift-add
(b) Booth's algorithm
(c) restoring division
(d) non-restoring division

### B4. Short answer [3]

Represent the decimal number **−45** in 8-bit binary as:
(a) sign-magnitude;
(b) 1's complement;
(c) 2's complement.

### B5. Short answer [3]

Add the 4-bit two's-complement numbers `0110` and `0101`. Give the result and state whether **overflow** occurred. Justify your answer with a bit-level argument.

### B6. Short answer [3]

Encode the decimal value **6.75** in **IEEE-754 single precision**. Show the sign bit, exponent field, mantissa field, and the final 32-bit hexadecimal value.

---

## Section C — Instruction Sets and CPU Structure (Ch 3) — 15 marks

### C1. Multiple choice [2]

Which addressing mode computes the effective address as **base register + constant offset**?

(a) immediate
(b) direct
(c) indexed / base+offset
(d) register indirect

### C2. Multiple choice [2]

In MIPS, an **R-type instruction** includes fields:

(a) opcode, address
(b) opcode, rs, rt, immediate
(c) opcode, rs, rt, rd, shamt, funct
(d) opcode, target address

### C3. Multiple choice [2]

A **RISC** architecture is characterized by:

(a) variable-length instructions and microcode
(b) fixed-length instructions, load-store architecture, many registers
(c) complex addressing modes
(d) microcoded instruction execution

### C4. Short answer [4]

Decode the following MIPS machine instruction (hex): `0x012A4020`. Identify the **format**, the **operation**, the **source and destination registers**, and write the equivalent assembly.

### C5. Short answer [3]

Compare the **number of memory accesses** required for each of the following addressing modes to fetch a single operand from a register-based datapath:

- immediate
- register
- register indirect
- direct
- indirect

Give the effective address formula for each.

### C6. Short answer [2]

Explain in one sentence why the **program counter** and the **instruction register** are essential for the Von Neumann instruction cycle.

---

## Section D — Pipelining and Control Unit (Ch 4) — 18 marks

### D1. Multiple choice [2]

The maximum ideal speedup of a **k-stage** pipeline is:

(a) $k^2$
(b) $k$
(c) $\log_2 k$
(d) $2k$

### D2. Multiple choice [2]

A **structural hazard** in a pipeline occurs when:

(a) two instructions want the same hardware resource
(b) an instruction depends on the result of a previous instruction
(c) a branch is taken
(d) a page fault occurs

### D3. Multiple choice [2]

**Branch prediction** is used to address:

(a) structural hazards
(b) data hazards
(c) control hazards
(d) cache misses

### D4. Short answer [4]

Consider the instruction sequence:
```
ADD  R1, R2, R3
SUB  R4, R1, R5
AND  R6, R1, R7
```
Identify all **data hazards**. For each hazard, state:
(a) the type of hazard (RAW, WAR, WAW);
(b) the number of stall cycles needed **without** forwarding;
(c) how forwarding resolves it (or whether a stall is still needed).

### D5. Short answer [4]

Describe the **difference** between a **hardwired** and a **microprogrammed** control unit in terms of:
(a) implementation complexity;
(b) speed;
(c) flexibility;
(d) which architecture class (RISC or CISC) typically uses each.

### D6. Short answer [4]

A program has **20% branch instructions**. In a 5-stage pipeline, each taken branch causes a 2-cycle penalty. If branch prediction accuracy is 90%, compute the **average CPI** assuming the ideal CPI is 1.

---

## Section E — Memory Systems (Ch 5) — 20 marks

### E1. Multiple choice [2]

The principle that justifies the memory hierarchy is:

(a) Moore's law
(b) Amdahl's law
(c) locality of reference
(d) the Von Neumann bottleneck

### E2. Multiple choice [2]

DRAM requires periodic **refresh** because:

(a) it uses flip-flops
(b) it stores each bit as charge on a capacitor that leaks
(c) it is slower than SRAM
(d) it is volatile

### E3. Multiple choice [2]

In a **direct-mapped** cache, the set is determined by:

(a) the tag bits
(b) the block-offset bits
(c) the index bits
(d) the entire address

### E4. Multiple choice [2]

**Write-back** caching reduces memory traffic compared with **write-through** because:

(a) every write goes to memory
(b) writes accumulate in the cache and reach memory only when the block is evicted
(c) it caches only reads
(d) it uses larger blocks

### E5. Short answer [4]

A cache has:
- 32 KB total size,
- 64-byte blocks,
- 4-way set associativity.

Compute:
(a) the number of blocks in the cache;
(b) the number of sets;
(c) the number of index bits;
(d) the number of tag bits (for a 32-bit address);
(e) the number of block-offset bits.

### E6. Short answer [4]

A cache has a hit time of **2 ns**, a miss penalty of **40 ns**, and a hit rate of **95%**. Compute the **AMAT** (average memory access time). Then compute the AMAT if the hit rate improves to 98%.

### E7. Short answer [4]

Explain the difference between **paging** and **segmentation**. Which one suffers from **internal fragmentation**? Which one from **external fragmentation**?

### E8. Short answer [2]

Briefly explain why an **SSD** has **no seek time**, and how this changes the relative performance of random versus sequential reads compared with an HDD.

---

## Section F — I/O Systems (Ch 6) — 12 marks

### F1. Multiple choice [2]

In **programmed I/O**, the CPU:

(a) is interrupted by the device
(b) transfers data using DMA
(c) continuously polls the device status register
(d) uses a separate I/O processor

### F2. Multiple choice [2]

**DMA** reduces CPU load because:

(a) the CPU is slower
(b) the DMA controller transfers data between memory and device without CPU intervention
(c) it uses fewer interrupts
(d) it transfers whole sectors

### F3. Short answer [4]

Compare **programmed I/O**, **interrupt-driven I/O**, and **DMA** on:
(a) CPU involvement;
(b) latency per transfer;
(c) suitability for high-speed devices.

### F4. Short answer [4]

A disk has an average seek time of **9 ms**, a rotational speed of **7200 RPM**, and a transfer rate of **150 MB/s**. Compute:
(a) the average rotational latency;
(b) the average time to transfer a 4 KB block;
(c) the total average service time for the transfer.

---

## Section G — Advanced Topics (Ch 7) — 8 marks

### G1. Multiple choice [2]

**SIMD** processing is best described as:

(a) one instruction on one datum
(b) one instruction on multiple data items
(c) multiple instructions on one datum
(d) multiple instructions on multiple data items

### G2. Multiple choice [2]

A **GPU** achieves high throughput mainly because it:

(a) runs at a very high clock speed
(b) uses thousands of simple cores to execute many threads in parallel
(c) has a very large cache
(d) uses faster memory than a CPU

### G3. Short answer [4]

For each of the following workloads, identify which architecture (SISD, SIMD, MIMD) is best suited and justify:

(a) Adding two large matrices element-wise.
(b) A single-threaded text editor.
(c) A web server handling 10,000 simultaneous clients.
(d) A scientific simulation with loosely coupled sub-tasks.

---

## Grading rubric

| Band | Marks |
|---|---|
| A | 90–100 |
| B | 75–89 |
| C | 60–74 |
| F | < 60 |

**Common error checks:**

- Wrong CPI or clock calculation: −2.
- Off-by-one in addressing-mode memory-access counts: −1.
- Confusing write-through with write-back: −2.
- Missing `overflow` in 2's-complement addition: −1.
- Wrong pipeline stall count: −2.
- Using the wrong Amdahl's law formula: −2.
- Confusing internal with external fragmentation: −2.
- Treating GPU threads as CPUs: −1.

---

## Answer key

Full worked solutions are in the [Solutions](../solutions/index.md) section — one file per chapter, each answering the corresponding section above.