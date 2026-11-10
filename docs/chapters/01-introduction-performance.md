# Chapter 1 — Introduction and Performance

!!! abstract "Chapter at a glance"
    **Contact hours:** 2 lecture + 3 tutorial/lab · **Home study:** ~5 h

    **Coverage:** organization vs architecture · computer evolution · the Von Neumann model · components of a computer system · instruction cycle · performance metrics · Amdahl's law · benchmarks.

---

## Learning outcomes

1. Distinguish **computer architecture** from **computer organization** and give examples of each.
2. Trace the **evolution** of computing through its generations and Moore's law.
3. Describe the **Von Neumann model** and identify the role of each subsystem.
4. Trace the **instruction cycle** (fetch, decode, execute).
5. Compute the **CPU performance equation** and apply **Amdahl's law** to evaluate a proposed optimisation.

---

## 1.1 Organization vs. architecture

!!! note "Definition (Stallings §1.1)"
    **Computer architecture** refers to those attributes of a system **visible to a programmer** — the instruction set, data types, addressing modes, and I/O mechanisms. **Computer organization** refers to the **operational units and their interconnections** that realize the architectural specification — control signals, memory technology, and interfaces.

The distinction is analogous to the distinction between the **specification** of a programming language and the **implementation** of its compiler:

| Aspect | Architecture | Organization |
|---|---|---|
| Visible to programmer | **Yes** | **No** |
| Example | Whether an instruction `MUL` exists | Whether `MUL` is implemented by a hardware multiplier or by repeated addition |
| Example | Word size (32 or 64 bits) | The technology of the memory (SRAM vs DRAM) |
| Example | Number of registers | The physical wiring of the register file |

!!! tip "Why the distinction matters"
    Two CPUs can be **architecturally identical** but **organizationally different** — same instruction set, different implementation. This is why Intel and AMD x86 CPUs run the same binaries but have very different microarchitectures, power envelopes, and performance profiles.

---

## 1.2 Evolution of computing

### 1.2.1 The generations

| Generation | Era | Technology | Characteristics |
|---|---|---|---|
| **First** | 1945–1955 | Vacuum tubes | ENIAC; 5,000 additions/second |
| **Second** | 1955–1965 | Transistors | IBM 7094, smaller and more reliable |
| **Third** | 1965–1980 | Integrated circuits | IBM System/360, DEC PDP-11 |
| **Fourth** | 1980–present | VLSI | PCs, mobile, servers |

### 1.2.2 Moore's law

!!! abstract "Moore's law"
    The number of transistors on a chip **doubles approximately every 18–24 months**, while the cost per transistor falls proportionally.

Moore's law drove a **40-year exponential** in computing capability. It has slowed since ~2010 because:

- **Power density** (Dennard scaling) broke down.
- **Manufacturing costs** rise at advanced nodes (7 nm, 5 nm, 3 nm).
- **Physical limits** (transistor size, wire delay) loom.

The industry's response has shifted from **higher clock speed** to **more parallelism** — multicore CPUs, GPUs, and specialised accelerators.

### 1.2.3 The Von Neumann bottleneck

In a Von Neumann machine, **both instructions and data** share the same memory and the same bus between CPU and memory. The processor therefore cannot fetch an instruction and a data word in the same cycle — a bandwidth limit known as the **Von Neumann bottleneck**.

The **Harvard architecture** separates instruction and data memories, doubling the effective bandwidth. Modern CPUs use **modified Harvard** designs — separate L1 instruction and data caches, but a unified L2/L3 hierarchy.

---

## 1.3 The Von Neumann model

```
┌─────────────┐         ┌──────────────┐
│             │◄───────►│              │
│    CPU      │  Bus    │    Memory    │
│             │◄───────►│              │
└─────┬───────┘         └──────────────┘
      │
      │
┌─────▼───────┐
│  I/O        │
│  Devices    │
└─────────────┘
```

The Von Neumann model has four components:

1. **CPU** — executes instructions. Contains the **ALU** (arithmetic-logic unit), the **control unit**, and **registers**.
2. **Memory** — stores both instructions and data, addressed by location.
3. **I/O** — interacts with the outside world.
4. **Bus** — carries addresses, data, and control signals between the components.

---

## 1.4 The instruction cycle

Every instruction goes through **three phases** (sometimes split into more):

```
Fetch  →  Decode  →  Execute
```

**Fetch:**
1. CPU puts the PC value on the address bus.
2. Memory returns the instruction at that address.
3. CPU loads the instruction into the IR and increments the PC.

**Decode:**
1. Control unit reads the opcode and determines the instruction type.
2. Source operands are read from the register file.

**Execute:**
1. ALU performs the operation (or memory access, or branch).
2. Result is written back to a register (or memory).

Many architectures add **memory** and **write-back** stages, yielding a **five-stage** pipeline (Chapter 4).

---

## 1.5 Performance metrics

### 1.5.1 Clock rate and cycle time

The **clock rate** $f$ (Hz) determines how many cycles per second the CPU executes. The **cycle time** is $\tau = 1/f$.

### 1.5.2 The performance equation

!!! abstract "CPU time formula (Patterson & Hennessy)"
    $$\text{CPU time} = \text{IC} \times \text{CPI} \times \tau = \frac{\text{IC} \times \text{CPI}}{f}$$

    where:
    - $\text{IC}$ = instruction count (total instructions executed);
    - $\text{CPI}$ = cycles per instruction (average);
    - $\tau$ = clock period; $f$ = clock rate.

The three factors are **interdependent**. Reducing IC (fewer, more powerful instructions) usually raises CPI. Increasing $f$ often increases CPI because pipeline stages become harder to balance.

### 1.5.3 MIPS and FLOPS

**MIPS** (millions of instructions per second):

$$
\text{MIPS} = \frac{\text{IC}}{T \times 10^6} = \frac{f}{\text{CPI} \times 10^6}
$$

MIPS is misleading — it ignores the complexity of instructions. A CISC machine can have higher MIPS than a RISC machine but lower throughput.

**FLOPS** (floating-point operations per second) is more meaningful for scientific workloads.

### 1.5.4 Amdahl's law

!!! abstract "Amdahl's law"
    If a fraction $f$ of the execution time is **improved by a factor** $s$, the overall speedup is

    $$S = \frac{1}{(1 - f) + f/s}$$

    The speedup is bounded by $1/(1-f)$ as $s \to \infty$.

**Implication.** An optimisation that only improves $10\%$ of the code can give at most a $1/(1-0.1) \approx 1.11$ speedup — no matter how fast the improved part becomes. Amdahl's law is the **fundamental argument for parallel computing**: as serial fractions approach zero, the potential speedup approaches the processor count.

### Worked example 1.1 — CPU performance

**Given.**
- IC = $10^8$ instructions.
- CPI = 2.5.
- Clock = 4 GHz.

**CPU time:**

$$
T = \frac{10^8 \times 2.5}{4 \times 10^9} = 0.0625\,\text{s} = 62.5\,\text{ms}.
$$

**MIPS:**

$$
\text{MIPS} = \frac{4 \times 10^9}{2.5 \times 10^6} = 1600\,\text{MIPS}.
$$

### Worked example 1.2 — Amdahl's law

**Given.** A program spends **60%** of its time in a routine made **3× faster**.

**Speedup:**

$$
S = \frac{1}{(1 - 0.6) + 0.6/3} = \frac{1}{0.4 + 0.2} = \frac{1}{0.6} \approx 1.67.
$$

The maximum possible speedup even if the routine became **infinitely fast** is:

$$
S_{\max} = \frac{1}{1 - 0.6} = 2.5.
$$

So even a **perfect** optimisation of that routine yields at most a 2.5× speedup.

### 1.5.5 Benchmarks

Real workloads vary enormously. To compare systems fairly, industry uses **standard benchmark suites**:

- **SPEC CPU** — integer and floating-point benchmarks.
- **SPECpower** — energy efficiency.
- **Geekbench** — mobile and desktop.
- **STREAM** — memory bandwidth.
- **Linpack** — floating-point (basis of the TOP500 supercomputer ranking).

Benchmarks are not perfect — vendors can "optimise for the benchmark" — but they are the least-bad way to compare systems.

---

## 1.6 Quiz

1. Distinguish computer architecture from computer organization. Give two examples of each.
2. State Moore's law. Why has it slowed since ~2010?
3. Describe the instruction cycle. What is stored in the PC and IR?
4. Compute the CPU time for a program with IC = $5 \times 10^8$, CPI = 1.8, clock = 3 GHz.
5. A program spends 40% of its time in a loop that can be made 2× faster. Use Amdahl's law to compute the speedup.

---

## Answer key

??? success "Click to reveal"

    1. Architecture: visible to programmer — instruction set, word size. Organization: implementation — SRAM vs DRAM, pipeline depth.
    2. Transistor count doubles every 18–24 months. Slowed because power density, manufacturing costs, and physical limits have caught up with the doubling.
    3. Fetch (PC → IR, PC increment), decode (opcode), execute (ALU/memory). PC holds the address of the next instruction; IR holds the current instruction.
    4. $T = (5 \times 10^8 \times 1.8) / (3 \times 10^9) = 0.3\,\text{s}$.
    5. $S = 1/(0.6 + 0.4/2) = 1/0.8 = 1.25$.

---

## Further reading

- Stallings, §1.1–§2.5.
- Mano, §1.1–§1.3.
- Patterson & Hennessy, §1.1–§1.9.