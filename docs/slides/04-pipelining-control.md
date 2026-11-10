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
  js: assets/lecture-04.js

---

# ECEG-3163

## Chapter 4 — Pipelining and Control Unit

---

## 4.1 — Pipeline Concept

???+ note "Ideal pipeline speedup"
    A $k$-stage pipeline completes **one instruction per cycle** in steady state. Non-pipelined execution takes $k$ cycles per instruction.
    
    $$
    S_{\text{ideal}} = k
    $$

<div class="fragment">

!!! warning "Realistic speedup"
    Actual speedup is limited by:
    - **Hazards** (structural, data, control).
    - **Fill / drain** time.
    - **Stage imbalance** — clock period = max stage time.

</div>

<div class="fragment">

???+ info "Fill and drain"
    The first instruction takes $k$ cycles to traverse the pipeline; the last also takes $k$. For a program with $n$ instructions, total cycles $\approx k + (n-1)$. As $n \to \infty$, the overhead vanishes.

</div>

---

## 4.2 — The Five-Stage RISC Pipeline

???+ note "IF → ID → EX → MEM → WB"
    | Stage | Purpose |
    |---|---|
    | **IF** | Fetch instruction from memory |
    | **ID** | Decode, read registers |
    | **EX** | ALU operation or address calculation |
    | **MEM** | Read/write data memory |
    | **WB** | Write result to register |

<div class="fragment">

???+ info "Pipeline timing"
    ```
    cycle:   1   2   3   4   5   6   7   8
    Instr1: IF  ID  EX  MEM WB
    Instr2:     IF  ID  EX  MEM WB
    Instr3:         IF  ID  EX  MEM WB
    Instr4:             IF  ID  EX  MEM WB
    ```
    
    Five instructions overlap in the pipeline simultaneously.

</div>

---

## 4.3 — Structural Hazards

???+ note "Definition"
    Two instructions want the **same hardware** in the same cycle. Classic case: IF and MEM both need the (single) memory port in a Von Neumann machine.

<div class="fragment">

??? info "The fix"
    **Split instruction and data memories** (Harvard) — IF reads instruction memory, MEM reads data memory, no conflict.

</div>

<div class="fragment">

!!! tip "Other structural hazards"
    - One register-file write port in WB vs. one needed by ID — usually solved by write-then-read design.
    - Single ALU used by both EX and (some) addressing — solved by a second adder.

</div>

---

## 4.4 — Data Hazards

???+ note "Three types"
    | Type | Dependence | Meaning |
    |---|---|---|
    | **RAW** | read-after-write | Consumer needs a value the producer hasn't written yet |
    | **WAR** | write-after-read | Consumer's read must complete before producer's write (anti-dependence) |
    | **WAW** | write-after-write | Two writes to the same register (output dependence) |

<div class="fragment">

???+ info "Which need fixing?"
    - **RAW** — always present, resolved by **forwarding** and/or **stalling**.
    - **WAR**, **WAW** — resolved by **register renaming** in out-of-order CPUs.

</div>

<div class="fragment">

??? warning "Load-use is special"
    A `LOAD` produces its value only after the **MEM** stage. The next instruction's **EX** stage starts before that — so even forwarding cannot help. **One stall cycle is required.**

</div>

---

## 4.5 — Forwarding

???+ note "Definition"
    **Forwarding** (bypass) routes the ALU or memory output **directly** to a consumer's input, skipping the register file.

<div class="fragment">

???+ info "How it works"
    ```
    cycle:    1   2   3   4   5
    ADD R1:   IF  ID  EX  MEM WB
    SUB R4:       IF  ID  EX  MEM WB
                          ↑
                          EX reads R1 from ADD's ALU output
    ```
    
    The value is available at ADD's EX output and needed at SUB's EX input — same cycle, one path.

</div>

<div class="fragment">

!!! tip "What forwarding does NOT solve"
    **Load-use** — the load's data is only available after MEM, but the consumer needs it at EX. One stall is unavoidable.

</div>

---

## Worked example 4.1 — Hazards in ADD/SUB/AND

!!! tip "Problem:"
    ```
    ADD  R1, R2, R3
    SUB  R4, R1, R5
    AND  R6, R1, R7
    ```
    
    Identify all hazards.

<div class="fragment">

???+ note "**Step 1 — dependencies.**"
    - SUB uses R1 (produced by ADD) → **RAW hazard**.
    - AND uses R1 (produced by ADD) → **RAW hazard**.

</div>

<div class="fragment">

??? info "**Step 2 — stalls without forwarding.**"
    Each consumer must wait for ADD to reach **WB** (cycle 5):
    - SUB stalls 2 cycles.
    - AND stalls 2 cycles.
    - **Total: 4 stall cycles.**

</div>

<div class="fragment">

??? success "**Step 3 — with forwarding.**"
    - SUB: forward ALU output → **1 stall** (EX→EX not free).
    - AND: forward MEM/WB output → **0 stalls**.
    - **Total: 1 stall cycle.**

</div>

---

## 4.6 — Control Hazards

???+ note "Definition"
    The pipeline fetches instructions speculatively, but a **branch** decides the next PC only in EX (or later). If the branch is taken, the fetched instructions are wrong and must be flushed.

<div class="fragment">

???+ note "Solutions"
    | Technique | Idea | Cost |
    |---|---|---|
    | **Stall** | Wait for resolution | High |
    | **Predict not-taken** | Continue with fall-through | Low, wrong for taken branches |
    | **Delayed branch** | Execute 1 instruction from delay slot regardless | One wasted slot |
    | **Branch prediction** | Static or dynamic prediction | Depends on accuracy |

</div>

<div class="fragment">

!!! tip "Dynamic prediction"
    A **branch history table** records the outcome of recent branches. **Two-bit saturating counters** give 90%+ accuracy on typical code — the single biggest win for control-hazard mitigation.

</div>

---

## Worked example 4.2 — Branch penalty CPI

!!! tip "Problem:"
    20% branches, 2-cycle misprediction penalty, 90% prediction accuracy, ideal CPI = 1.

<div class="fragment">

???+ note "**Step 1 — misprediction rate per instruction.**"
    $$
    \text{mispredictions} = 0.20 \times 0.10 = 0.02.
    $$

</div>

<div class="fragment">

??? info "**Step 2 — expected penalty.**"
    $$
    \text{penalty} = 0.02 \times 2 = 0.04\ \text{cycles/instruction}.
    $$

</div>

<div class="fragment">

??? success "**Conclusion.**"
    $$
    \text{CPI}_{\text{avg}} = 1 + 0.04 = \mathbf{1.04}.
    $$
    
    The branch cost adds **4%** to the baseline CPI.

</div>

<div class="fragment">

??? tip "**Sensitivity.**"
    If prediction were 100% accurate → CPI = 1. If always wrong → CPI = 1 + 0.20 × 2 = 1.40.

</div>

---

## 4.7 — Hardwired Control

???+ note "Definition"
    The control unit is a **finite-state machine** — combinational logic generates each control signal from the opcode and current state.

<div class="fragment">

???+ info "Design process"
    1. Define the ISA.
    2. Draw the state diagram for each instruction type.
    3. Derive the truth table (state × opcode → control signals).
    4. Minimize with Karnaugh maps or a synthesis tool.
    5. Wire up the gates.

</div>

<div class="fragment">

!!! tip "Pros and cons"
    **Pros:** Fast — signals appear combinationally, no fetch overhead.
    **Cons:** Difficult to modify — adding an instruction means redesigning the logic.
    **Used by:** RISC (MIPS, ARM, RISC-V).

</div>

---

## 4.8 — Microprogrammed Control

???+ note "Wilkes' model (1951)"
    The control unit is a **microprogram** stored in **control memory**:
    
    ```
    Control Memory (microcode)
          ↓
    Microinstruction Register
          ↓
    Micro-sequencer (next-address logic)
          ↓
    Control signals to datapath
    ```
    
    Each machine instruction = a sequence of microinstructions.

<div class="fragment">

???+ info "Horizontal vs vertical microcode"
    | Aspect | Horizontal | Vertical |
    |---|---|---|
    | Encoding | One bit per control signal | Compressed (opcode + operands) |
    | Width | Wide (100+ bits) | Narrow (20–30 bits) |
    | Parallelism | Full — all signals in one word | Needs a decoder |
    | Speed | Fast | Slower |

</div>

<div class="fragment">

!!! tip "Pros and cons"
    **Pros:** Flexible — add instructions by appending microcode. Good for complex ISAs.
    **Cons:** Slower — each instruction needs a sequence of microinstruction fetches.
    **Used by:** CISC (classic x86, VAX).

</div>

---

## 4.9 — Hardwired vs Microprogrammed

???+ note "The trade-off"
    | Aspect | Hardwired | Microprogrammed |
    |---|---|---|
    | Implementation | Complex (FSM) | Simpler (microcode) |
    | Speed | **Faster** | Slower |
    | Flexibility | Low | **High** |
    | Modification | Redesign | Update microcode |
    | Cost | Higher design effort | Higher control-memory cost |
    | Best for | RISC | CISC |
    | Modern use | Yes (fast path) | Yes (complex instructions) |

<div class="fragment">

!!! tip "Modern x86 does both"
    Intel/AMD CPUs use **hardwired control for common instructions** and **microcode for rare/complex ones** — a hybrid that captures the best of both.

</div>

---

## Chapter 4 — key takeaways

<div class="fragment">

**1.** A $k$-stage pipeline gives ideal speedup $k$; real speedup is limited by hazards and stage imbalance.

</div>

<div class="fragment">

**2.** **Data hazards** (RAW) are the most common; **forwarding** resolves most without stalls, but **load-use** requires one stall cycle.

</div>

<div class="fragment">

**3.** **Control hazards** are addressed by **branch prediction**. A 90% accurate predictor adds only ~4% to CPI on typical workloads.

</div>

<div class="fragment">

**4.** **Hardwired control** is fast and used in RISC; **microprogrammed** is flexible and used in CISC.

</div>

<div class="fragment">

**5.** Modern CPUs are **hybrid**: hardwired for common instructions, microcoded for complex ones.

</div>

---

## Next — Chapter 5

**Memory Systems.**

<div class="fragment">

- Memory hierarchy and locality.
- SRAM, DRAM, ROM, flash.
- Cache organization: direct-mapped, set-associative, fully associative.
- Replacement and write policies.
- **AMAT** and cache performance.
- **Virtual memory** and TLB.
- HDD and SSD.

</div>

<div class="fragment">

The pipeline speedup of Chapter 4 is capped by the **memory wall** — unless the cache hides the 100× gap between registers and DRAM.

</div>