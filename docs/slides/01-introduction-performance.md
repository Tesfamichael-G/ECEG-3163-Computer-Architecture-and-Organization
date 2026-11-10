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
  js: assets/lecture-01.js

---

# ECEG-3163

## Chapter 1 — Introduction and Performance

---

## 1.1 — Organization vs Architecture

???+ note "Definition (Stallings §1.1)"
    **Computer architecture** = attributes **visible to a programmer**: instruction set, data types, addressing modes, I/O mechanisms.
    
    **Computer organization** = the **operational units and their interconnections** that realize the architecture: control signals, memory technology, interfaces.

<div class="fragment">

| Aspect | Architecture | Organization |
|---|---|---|
| Visible to programmer? | **Yes** | **No** |
| Example | Does `MUL` exist? | Is `MUL` a hardware multiplier or repeated add? |
| Example | Word size (32 / 64 bits) | SRAM vs. DRAM for main memory |
| Example | Number of registers | Physical wiring of the register file |

</div>

<div class="fragment">

!!! tip "Why the distinction matters"
    Two CPUs can be **architecturally identical** but **organizationally different** — the same x86 binaries run on Intel and AMD CPUs, but their microarchitectures, power envelopes, and performance profiles differ enormously.

</div>

<aside class="notes">
  <div style="font-size: 16px; line-height: 1.6;">
Open by anchoring the two words: architecture is the *contract*, organization is the *implementation*. Analogy: architecture is the Java language spec, organization is the JIT compiler. Every subsequent chapter alternates between the two — Chapter 3 is mostly architecture, Chapters 4–5 are mostly organization.
  </div>
</aside>

---

## 1.2 — Evolution of Computing

???+ note "The four generations"
    **1st (1945–1955):** vacuum tubes, ENIAC, ~5000 additions/s.
    **2nd (1955–1965):** transistors, IBM 7094.
    **3rd (1965–1980):** integrated circuits, IBM System/360, PDP-11.
    **4th (1980–present):** VLSI, PCs, mobile, servers.

<div class="fragment">

!!! abstract "Moore's law"
    The number of transistors on a chip **doubles approximately every 18–24 months**, and the cost per transistor falls proportionally.

</div>

<div class="fragment">

??? warning "Moore's law has slowed since ~2010"
    - **Power density** (Dennard scaling) broke down — smaller transistors no longer run cooler.
    - **Manufacturing costs** rise steeply at 7 nm / 5 nm / 3 nm.
    - **Physical limits** — transistor size and wire delay.

    The industry's response: shift from **higher clock speed** to **more parallelism** (multicore, GPUs, accelerators).

</div>

---

## 1.3 — The Von Neumann Bottleneck

???+ note "Von Neumann model"
    Instructions and data share **the same memory** and **the same bus** between CPU and memory. The CPU cannot fetch an instruction and a data word in the same cycle.

<div class="fragment">

!!! warning "The bottleneck"
    The bus between CPU and memory is a shared resource. Memory bandwidth — not ALU speed — limits throughput for many workloads.

</div>

<div class="fragment">

??? info "The Harvard alternative"
    **Harvard architecture** separates instruction and data memories, doubling the effective bandwidth. Modern CPUs use a **modified Harvard** design — separate L1 instruction and data caches, unified L2/L3.

</div>

---

## 1.4 — The Von Neumann Model

```
┌─────────────┐         ┌──────────────┐
│             │◄───────►│              │
│    CPU      │  Bus    │    Memory    │
│             │◄───────►│              │
└─────┬───────┘         └──────────────┘
      │
┌─────▼───────┐
│  I/O        │
│  Devices    │
└─────────────┘
```

<div class="fragment">

???+ note "Four components"
    1. **CPU** — ALU, control unit, registers.
    2. **Memory** — stores both instructions and data.
    3. **I/O** — outside-world interface.
    4. **Bus** — carries addresses, data, and control signals.

</div>

---

## 1.5 — The Instruction Cycle

???+ note "Fetch → Decode → Execute"
    The CPU repeats three phases for every instruction.

<div class="fragment">

???+ info "**Fetch**"
    1. CPU puts the value of the **PC** on the address bus.
    2. Memory returns the instruction at that address.
    3. CPU loads the instruction into the **IR** and increments the **PC**.

</div>

<div class="fragment">

??? info "**Decode**"
    1. Control unit reads the **opcode** and determines the instruction type.
    2. Source operands are read from the register file.

</div>

<div class="fragment">

??? info "**Execute**"
    1. ALU performs the operation (or memory access, or branch).
    2. Result is written back to a register (or memory).

</div>

<div class="fragment">

!!! tip "Five stages, five cycles"
    Modern CPUs split memory access and write-back into separate stages, yielding the **five-stage pipeline** (Chapter 4): IF, ID, EX, MEM, WB.

</div>

---

## 1.6 — Performance Metrics

???+ abstract "The CPU performance equation (Patterson & Hennessy)"
    $$
    \text{CPU time} = \text{IC} \times \text{CPI} \times \tau
    = \frac{\text{IC} \times \text{CPI}}{f}
    $$
    
    where $\text{IC}$ = instruction count, $\text{CPI}$ = cycles per instruction, $f$ = clock frequency.

<div class="fragment">

??? warning "The three factors are interdependent"
    - Fewer instructions (CISC-style) → higher CPI.
    - Higher clock (shorter cycles) → higher CPI (harder pipeline balance).
    - Optimising one factor often worsens another.

</div>

<div class="fragment">

???+ note "**MIPS** — millions of instructions per second"
    $$
    \text{MIPS} = \frac{f}{\text{CPI} \times 10^6}
    $$
    
    Misleading: ignores instruction complexity. A CISC machine can have higher MIPS but lower throughput than a RISC machine.

</div>

---

## Worked example 1.1 — CPU time and MIPS

!!! tip "Problem:"
    Given IC = $10^8$ instructions, CPI = 2.5, clock = 4 GHz.

<div class="fragment">

???+ note "**Step 1 — CPU time.**"
    $$
    T = \frac{10^8 \times 2.5}{4 \times 10^9} = 0.0625\ \text{s} = \mathbf{62.5\ \text{ms}}
    $$

</div>

<div class="fragment">

??? info "**Step 2 — MIPS.**"
    $$
    \text{MIPS} = \frac{4 \times 10^9}{2.5 \times 10^6} = \mathbf{1600\ \text{MIPS}}
    $$

</div>

<div class="fragment">

??? success "**Conclusion.**"
    The program takes 62.5 ms and the CPU sustains 1600 MIPS — a consistent pair.

</div>

<aside class="notes">
  <div style="font-size: 16px; line-height: 1.6;">
Walk through the arithmetic. Emphasise that MIPS is derivable from the CPU-time formula — the same information in a different unit. Then ask: if we double the clock and CPI rises to 4, what happens? T stays the same. This motivates Amdahl's law and pipeline awareness.
  </div>
</aside>

---

## 1.7 — Amdahl's Law

???+ abstract "Amdahl's law"
    If a fraction $f$ of the execution time is improved by a factor $s$, the overall speedup is
    
    $$
    S = \frac{1}{(1 - f) + \dfrac{f}{s}}
    $$
    
    As $s \to \infty$, $S \to \dfrac{1}{1 - f}$.

<div class="fragment">

!!! warning "The fundamental limit"
    An optimisation that only touches $10\%$ of the code can give **at most** a $1.11\times$ speedup — regardless of how fast the improvement is.

</div>

<div class="fragment">

??? tip "The argument for parallelism"
    As the serial fraction approaches zero, the potential speedup approaches the processor count. This is why multicore exists: it attacks the **serial bottleneck**.

</div>

---

## Worked example 1.2 — Amdahl's law

!!! tip "Problem:"
    A program spends **60%** of its time in a routine made **3× faster**. Compute the speedup, and the maximum possible speedup.

<div class="fragment">

???+ note "**Step 1 — apply the formula.**"
    $$
    S = \frac{1}{(1 - 0.60) + 0.60/3}
    = \frac{1}{0.40 + 0.20}
    = \frac{1}{0.60} \approx \mathbf{1.667}
    $$

</div>

<div class="fragment">

??? info "**Step 2 — maximum possible speedup.**"
    Even if the routine became **infinitely fast** ($s \to \infty$):
    
    $$
    S_{\max} = \frac{1}{1 - 0.60} = \mathbf{2.5}
    $$

</div>

<div class="fragment">

??? success "**Interpretation.**"
    The whole program only gets **1.67× faster**, because the other 40% now dominates. No improvement of that routine — no matter how clever — can exceed 2.5×.

</div>

---

## 1.8 — Benchmarks

???+ note "Standard benchmark suites"
    - **SPEC CPU** — integer and floating-point.
    - **SPECpower** — energy efficiency.
    - **Geekbench** — mobile and desktop.
    - **STREAM** — memory bandwidth.
    - **Linpack** — floating-point (TOP500 supercomputer ranking).

<div class="fragment">

??? warning "Benchmarks are imperfect"
    Vendors can optimise for the benchmark. Real workloads vary enormously. Use benchmarks as a **starting point**, never the whole answer — always test with **your** workload.

</div>

---

## Chapter 1 — key takeaways

<div class="fragment">

**1.** **Architecture** is what the programmer sees; **organization** is how it's built. The same architecture has many organizations.

</div>

<div class="fragment">

**2.** **Moore's law** drove 40 years of exponential improvement. It has slowed since ~2010 — the response is **parallelism**, not clock speed.

</div>

<div class="fragment">

**3.** The **Von Neumann bottleneck** — a shared bus for instructions and data — is why caches, prefetch, and Harvard-style L1s exist.

</div>

<div class="fragment">

**4.** **CPU time = IC × CPI × τ**. The three factors trade off against each other.

</div>

<div class="fragment">

**5.** **Amdahl's law** bounds every optimisation by the serial fraction it doesn't touch. The limit is $1/(1-f)$.

</div>

---

## Next — Chapter 2

**Computer Arithmetic.**

<div class="fragment">

- Signed number representations: sign-magnitude, 1's complement, 2's complement, biased.
- Integer addition and subtraction; overflow detection at the bit level.
- Multiplication: shift-add, **Booth's algorithm**.
- Division: restoring, non-restoring.
- **IEEE-754** floating point: single and double precision, special values, rounding.

</div>

<div class="fragment">

Every arithmetic operation you will meet inside a CPU is an instance of one of these algorithms — usually with a hardware accelerator.

</div>