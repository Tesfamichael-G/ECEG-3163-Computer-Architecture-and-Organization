# Chapter 4 — Pipelining and Control Unit

!!! abstract "Chapter at a glance"
    **Coverage:** pipeline concept and speedup · five-stage RISC pipeline · hazards · forwarding and stalling · branch prediction · hardwired and microprogrammed control.

---

## Learning outcomes

1. Compute pipeline speedup under ideal and realistic conditions.
2. Identify structural, data, and control hazards in an instruction sequence.
3. Apply **forwarding** and **stalling** to resolve data hazards.
4. Evaluate **branch prediction** schemes.
5. Design a **hardwired** control unit for a simple ISA.

---

## 4.1 Pipeline concept

A **pipeline** overlaps the execution of multiple instructions by breaking each into stages. If each stage takes $\tau$ and there are $k$ stages, the first instruction takes $k\tau$; thereafter, one completes per cycle.

**Ideal speedup:** $S = k$.

**Realistic speedup:** limited by **hazards**, **pipeline fill/drain**, and **imbalanced stage lengths**.

### 4.1.1 Five-stage RISC pipeline

| Stage | Purpose |
|---|---|
| **IF** | Fetch instruction from memory |
| **ID** | Decode, read registers |
| **EX** | ALU operation or address calculation |
| **MEM** | Read/write data memory |
| **WB** | Write result to register |

Total cycle time = max stage time + register overhead.

---

## 4.2 Hazards

### 4.2.1 Structural hazards

Two instructions want the **same hardware** in the same cycle (e.g., IF and MEM both need memory in a single-memory design). Solved by **separate instruction/data memories** (Harvard) or by **stalling**.

### 4.2.2 Data hazards

An instruction needs a value that a previous instruction has not yet produced. Three types:

- **RAW** (read-after-write): the real one — always present.
- **WAR** (write-after-read): anti-dependence, eliminated by **register renaming**.
- **WAW** (write-after-write): output dependence, eliminated by renaming.

**Forwarding** (bypass) routes the ALU output directly to the input of a later instruction, avoiding the WB stage.

**Load-use hazard** cannot be resolved by forwarding — the value is only available after MEM. One stall cycle is required.

### 4.2.3 Control hazards

Caused by branches: the pipeline fetches the wrong instructions until the branch is resolved. Solutions:

- **Stall** until branch resolution (costly).
- **Predict not-taken** (cheap, wrong for taken branches).
- **Delayed branch** — execute one instruction from the delay slot.
- **Branch prediction** — static (compile-time) or dynamic (runtime, using branch history tables).

---

## Worked example 4.1 — detecting hazards

**Sequence:**
```
ADD  R1, R2, R3      (produces R1)
SUB  R4, R1, R5      (uses R1)  ← RAW
AND  R6, R1, R7      (uses R1)  ← RAW
```

Without forwarding: SUB stalls 2 cycles, AND stalls 2 cycles → **4 stall cycles**.

With forwarding:
- SUB gets R1 from ALU output → 1-cycle stall.
- AND can read the value from the forwarding path after the ADD's EX stage → no stall (its EX happens in cycle 6, ADD's result available in cycle 4).

**Result:** 1 stall cycle.

### Worked example 4.2 — branch penalty

**Given:**
- 20% branches.
- 2-cycle penalty for taken branches (in a 5-stage pipeline).
- Branch prediction accuracy 90%.
- Ideal CPI = 1.

**Average CPI:**

$$
\text{CPI} = 1 + (0.20 \times 0.10 \times 2) = 1 + 0.04 = 1.04.
$$

Misprediction penalty applies only to 10% of 20% = 2% of instructions, each costing 2 cycles.

---

## 4.3 Control unit design

### 4.3.1 Hardwired control

The control unit is a **finite-state machine** — combinational logic generating control signals from the opcode and state.

**Pros:** Fast (single-cycle decode), efficient for small, regular ISAs.
**Cons:** Difficult to modify — adding an instruction requires redesign.
**Used by:** RISC architectures (MIPS, RISC-V).

### 4.3.2 Microprogrammed control

The control unit is a **microprogram** stored in **control memory**. Each machine instruction is a sequence of **microinstructions** that assert the appropriate control signals.

**Pros:** Flexible, easy to add instructions (add microcode), good for complex ISAs.
**Cons:** Slower (each instruction requires multiple microinstruction fetches).
**Used by:** CISC architectures (classic x86, VAX).

### 4.3.3 Wilkes' control unit model

Maurice Wilkes (1951) proposed that the control unit is a **microprogram** executed by a **micro-sequencer**:

```
Control Memory (microcode)
        ↓
  Microinstruction Register
        ↓
  Micro-sequencer (next-address logic)
        ↓
  Control signals to datapath
```

**Horizontal microcode** uses one bit per control line — wide instructions but parallel.
**Vertical microcode** uses a compact encoding — narrow instructions but needs a decoder.

---

## 4.4 Quiz

1. What is the ideal speedup of a 5-stage pipeline? What limits the actual speedup?
2. Identify the hazard type in each of these pairs: (a) `ADD R1,R2,R3` then `SUB R4,R1,R5`; (b) `LW R1,0(R2)` then `ADD R3,R1,R4`.
3. Why can't forwarding resolve a load-use hazard?
4. Explain the difference between horizontal and vertical microcode.
5. Compute the average CPI given: ideal CPI = 1, 25% branches, prediction accuracy 85%, taken-branch penalty 3 cycles.

---

## Answer key

??? success "Click to reveal"

    1. Ideal $= 5$. Limited by hazards, fill/drain, and stage imbalance.
    2. (a) RAW, (b) RAW (load-use).
    3. The value only appears after the MEM stage; the next instruction's EX stage starts before the load's value is available.
    4. Horizontal: one bit per control signal, wide but parallel. Vertical: encoded, narrow but needs decoding.
    5. $\text{CPI} = 1 + 0.25 \times 0.15 \times 3 = 1.1125$.

---

## Further reading

- Stallings, §14.1–§14.5, §17.1–§17.4.
- Patterson & Hennessy, §4.1–§4.8.