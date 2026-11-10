# Solutions — Chapter 1: Introduction and Performance

[:octicons-arrow-left-24: Back to Chapter 1](../chapters/01-introduction-performance.md)

Answers to **Section A** of the [Exam Bank](../exam-bank.md).

---

## A1 — Computer architecture [2]

**Answer: (b)** — attributes visible to a programmer (instruction set, data types, addressing).

**Why the others are wrong.**

- (a) Physical implementation is **organization**, not architecture.
- (c) Clock speed is an organizational detail (implementation).
- (d) The operating system is software built **on top of** the ISA, not the ISA itself.

**Reminder.** Architecture = the specification (what a programmer sees). Organization = the implementation (how the hardware realizes it).

---

## A2 — Moore's law [2]

**Answer: (b)** — the number of transistors per chip doubles approximately every 18–24 months.

**Why the others are wrong.**

- (a) Clock speed does **not** double every year; it has plateaued since ~2005 due to power density.
- (c) Memory latency improves much more slowly than transistor count.
- (d) Power consumption has been roughly flat since Dennard scaling broke down.

---

## A3 — Von Neumann bottleneck [2]

**Answer: (b)** — the transfer of instructions and data over a single bus.

**Why it matters.** The CPU cannot fetch an instruction and read/write data simultaneously because both go through the same bus and memory. The Von Neumann bottleneck is why we have caches, prefetching, and split instruction/data L1 caches (Harvard-style).

---

## A4 — CPU time and MIPS [3]

**Given.** IC = $10^8$, CPI = 2.5, $f = 4$ GHz $= 4 \times 10^9$ Hz.

**(a) CPU time.**

$$
T = \frac{\text{IC} \times \text{CPI}}{f} = \frac{10^8 \times 2.5}{4 \times 10^9} = \frac{2.5 \times 10^8}{4 \times 10^9} = 0.0625\ \text{s} = \mathbf{62.5\ \text{ms}}.
$$

**(b) MIPS rating.**

$$
\text{MIPS} = \frac{f}{\text{CPI} \times 10^6} = \frac{4 \times 10^9}{2.5 \times 10^6} = \mathbf{1600\ \text{MIPS}}.
$$

**Sanity check.** $1600$ MIPS $\times$ 62.5 ms $= 1600 \times 10^6 \times 0.0625 = 10^8$ instructions. ✓

---

## A5 — Amdahl's law [3]

**Given.** $f = 0.60$ (fraction improved), $s = 3$ (speedup of the improved part).

$$
S = \frac{1}{(1 - f) + \dfrac{f}{s}} = \frac{1}{(1 - 0.60) + \dfrac{0.60}{3}} = \frac{1}{0.40 + 0.20} = \frac{1}{0.60} \approx \mathbf{1.667}.
$$

**Interpretation.** Even though the routine itself is **3× faster**, the whole program is only **~1.67× faster** — because the other 40% dominates the new execution time.

**Upper bound.** Even if the improved part became **infinitely fast** ($s \to \infty$):

$$
S_{\max} = \frac{1}{1 - f} = \frac{1}{0.40} = 2.5.
$$

So no optimisation of that routine can exceed a 2.5× speedup of the whole program.