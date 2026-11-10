# Solutions — Chapter 4: Pipelining and Control Unit

[:octicons-arrow-left-24: Back to Chapter 4](../chapters/04-pipelining-control.md)

Answers to **Section D** of the [Exam Bank](../exam-bank.md).

---

## D1 — Maximum ideal pipeline speedup [2]

**Answer: (b)** $k$.

**Reason.** In an ideal $k$-stage pipeline with perfectly balanced stages and no hazards, one instruction completes per cycle after the pipeline fills. Non-pipelined execution takes $k$ cycles per instruction; pipelined execution takes $\approx 1$ cycle per instruction in steady state → speedup $k$.

**Caveats.** Fill/drain time, hazards, and unbalanced stages reduce the actual speedup below $k$.

---

## D2 — Structural hazard [2]

**Answer: (a)** two instructions want the same hardware resource.

**Examples.** IF and MEM both needing the single memory port in a Von Neumann datapath; two instructions wanting to write to the register file in the same cycle.

**Why the others are wrong.**

- (b) Two instructions with a data dependence = **data hazard**.
- (c) A taken branch = **control hazard**.
- (d) A page fault = a memory-management event, not a pipeline hazard.

---

## D3 — Branch prediction addresses [2]

**Answer: (c)** control hazards.

**Why.** Control hazards occur when the pipeline does not know which instruction to fetch next (fall-through vs branch target). Branch prediction guesses the outcome so the pipeline can keep fetching without stalling; on a correct prediction there is no penalty, on a misprediction the pipeline flushes and refetches.

---

## D4 — Data hazards in ADD/SUB/AND sequence [4]

**Sequence.**

```
ADD  R1, R2, R3
SUB  R4, R1, R5
AND  R6, R1, R7
```

**Step 1 — identify hazards.**

- **SUB uses R1**, produced by ADD → **RAW hazard**.
- **AND uses R1**, produced by ADD → **RAW hazard**.

(No WAR or WAW hazards here because ADD writes R1 first and both SUB/AND only read it.)

**Step 2 — stalls without forwarding.**

Without forwarding, an instruction must wait until the producer has written the register in **WB** before its consumer can read it in **ID**.

- ADD writes R1 at end of its WB (cycle 5 if fetched at cycle 1).
- SUB would need R1 in ID (cycle 3); it must wait until cycle 5 → **2 stall cycles**.
- AND likewise must wait until cycle 5 for R1 → **2 stall cycles**.

**Total: 4 stall cycles.**

**Step 3 — how forwarding resolves each hazard.**

- **SUB**: forwarding from ADD's EX output directly to SUB's EX input reduces the stall to **1 cycle** (or **0** if the datapath allows EX→EX forwarding in the same cycle). Standard MIPS implementations use 1.
- **AND**: by the time AND reaches EX, ADD's result has been available for two cycles; the value is forwarded from MEM/WB, and **no stall** is needed.

**Total with forwarding: 1 stall cycle.**

---

## D5 — Hardwired vs microprogrammed control [4]

**Step 1 — implementation complexity.**

- **Hardwired:** combinational logic (FSM). Design is **more complex** because every control signal must be hand-derived from the truth table. **Difficult to modify** — adding an instruction requires redesign.
- **Microprogrammed:** control memory (ROM) holding a sequence of microinstructions. Design is **simpler**; adding an instruction means appending a microprogram.

**Step 2 — speed.**

- **Hardwired:** **faster.** Control signals are combinational — no microinstruction fetch.
- **Microprogrammed:** **slower.** Each instruction requires a sequence of microinstruction fetches (like a tiny program).

**Step 3 — flexibility.**

- **Hardwired:** **less flexible.** Any change means new silicon (or a major revision).
- **Microprogrammed:** **more flexible.** Update the microcode to fix bugs or add instructions.

**Step 4 — architecture class.**

- **RISC** (MIPS, ARM, RISC-V) typically uses **hardwired** control — the small, regular ISA suits it.
- **CISC** (classic x86, VAX) typically uses **microprogrammed** control — the complex ISA needs the flexibility.

**Bonus.** Modern x86 has *both*: a hardwired fast path for common instructions plus a microcode engine for complex ones.

---

## D6 — Average CPI with branch penalty [4]

**Given.**

- Branch frequency = $20\%$.
- Prediction accuracy = $90\%$.
- Taken-branch penalty = 2 cycles.
- Ideal CPI = 1.

**Step 1 — compute the misprediction rate per instruction.**

Only 20% of instructions are branches; of those, 10% are mispredicted:

$$
\text{Misprediction rate per instruction} = 0.20 \times 0.10 = 0.02.
$$

**Step 2 — add the expected penalty.**

$$
\text{CPI}_{\text{avg}} = \text{Ideal CPI} + (\text{misprediction rate per instruction}) \times (\text{penalty})
$$

$$
= 1 + 0.02 \times 2 = 1 + 0.04 = \mathbf{1.04}.
$$

**Interpretation.** Every instruction takes, on average, $1.04$ cycles. The branch cost adds 4% to the baseline CPI.

**If prediction accuracy were 100%.** $\text{CPI} = 1$ — no penalty.
**If prediction were always wrong.** $\text{CPI} = 1 + 0.20 \times 2 = 1.40$.