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
  js: assets/lecture-03.js

---

# ECEG-3163

## Chapter 3 — Instruction Sets and CPU Structure

---

## 3.1 — Instruction Set Taxonomy

???+ note "Where do operands come from?"
    | Class | Operands live in... | Example |
    |---|---|---|
    | **Stack** | top of a pushdown stack | Java bytecode |
    | **Accumulator** | one special register | early 8-bit CPUs |
    | **General-purpose** | many named registers | x86, ARM, MIPS |
    | **Register-memory** | one operand in memory | x86 |
    | **Load-store** | all operands in registers | MIPS, ARM, RISC-V |

<div class="fragment">

!!! tip "Load-store is the RISC foundation"
    Only `LOAD` and `STORE` touch memory; all ALU operations are register-to-register. Simple to pipeline, simple to optimize.

</div>

---

## 3.2 — MIPS Instruction Formats

???+ note "R-type — register-to-register"
    ```
    | opcode 6 | rs 5 | rt 5 | rd 5 | shamt 5 | funct 6 |
    ```
    
    Example: `add rd, rs, rt` → `000000 rs rt rd 00000 100000`.

<div class="fragment">

???+ note "I-type — immediate"
    ```
    | opcode 6 | rs 5 | rt 5 | imm16 |
    ```
    
    Example: `addi rt, rs, imm`, `lw rt, offset(rs)`, `beq rs, rt, offset`.

</div>

<div class="fragment">

???+ note "J-type — jump"
    ```
    | opcode 6 | target 26 |
    ```
    
    Target = `(PC+4)[31:28] || target << 2` — 28 bits of address from 26-bit field.

</div>

---

## Worked example 3.1 — Decode `0x012A4020`

!!! tip "Problem:"
    Decode the MIPS instruction `0x012A4020`.

<div class="fragment">

???+ note "**Step 1 — binary.**"
    $\texttt{0000\,0001\,0010\,1010\,0100\,0000\,0010\,0000}$

</div>

<div class="fragment">

??? info "**Step 2 — split fields.**"

    | Field | Bits | Value |
    |---|---|---|
    | opcode | `000000` | R-type |
    | rs | `01001` | 9 |
    | rt | `01010` | 10 |
    | rd | `01000` | 8 |
    | shamt | `00000` | 0 |
    | funct | `100000` | `add` |

</div>

<div class="fragment">

??? success "**Step 3 — decode.**"
    $\texttt{add\ \$8,\ \$9,\ \$10}$ — register 8 = register 9 + register 10.

</div>

---

## 3.3 — Addressing Modes

???+ note "Eight classic modes"
    | Mode | Effective address (EA) | Memory accesses |
    |---|---|---|
    | **Immediate** | operand embedded in instruction | 0 |
    | **Register** | operand in register | 0 |
    | **Direct** | EA = address field | 1 |
    | **Indirect** | EA = M[address field] | 2 |
    | **Register indirect** | EA = R[field] | 1 |
    | **Displacement** (base+offset) | EA = R[base] + offset | 1 |
    | **Indexed** | EA = R[index] + address field | 1 |
    | **Relative** | EA = PC + offset | 1 |

<div class="fragment">

!!! tip "The base+offset workhorse"
    Base+offset handles **arrays** (base = array start, offset = index×stride), **structs** (base = struct, offset = field), and **stack frames** (base = frame pointer, offset = local) — all with the same encoding.

</div>

---

## Worked example 3.2 — Effective addresses

!!! tip "Problem:"
    Suppose `R[5] = 0x1000`. Compute the effective address for each mode.

<div class="fragment">

???+ note "**Immediate `lw $t0, 100`**"
    The value 100 is an immediate, not an address. There is no memory access for the operand itself.

</div>

<div class="fragment">

??? info "**Direct (address field 100)**"
    EA = 100. One memory access.

</div>

<div class="fragment">

??? info "**Register indirect `lw $t0, 0($5)`**"
    EA = R[5] + 0 = 0x1000. One memory access.

</div>

<div class="fragment">

??? info "**Base+offset `lw $t0, 8($5)`**"
    EA = R[5] + 8 = **0x1008**. One memory access.

</div>

<div class="fragment">

??? success "**Indirect `lw $t0, @100`**"
    Read M[100] → gives address A; read M[A] → gives operand. **Two memory accesses.**

</div>

---

## 3.4 — CPU Registers

???+ note "The essential registers"
    | Register | Purpose |
    |---|---|
    | **PC** | Program counter — address of next instruction |
    | **IR** | Instruction register — current instruction |
    | **MAR** | Memory address register |
    | **MBR / MDR** | Memory buffer / data register |
    | **R0..Rn** | General-purpose registers |
    | **SP** | Stack pointer |
    | **FLAGS / PSW** | Condition codes (Z, C, S, V) |

<div class="fragment">

!!! tip "PC + IR = the Von Neumann loop"
    The **PC** sequences the fetch; the **IR** holds the fetched instruction long enough to be decoded. Without either, there is no instruction cycle.

</div>

---

## 3.5 — Register Transfer Notation

???+ note "RTN describes data movement and operations"
    ```
    MAR ← PC
    MBR ← M[MAR]
    IR  ← MBR
    PC  ← PC + 4
    ```

<div class="fragment">

???+ note "Fetch cycle in RTN"
    ```
    T0: MAR ← PC
    T1: MBR ← M[MAR],  PC ← PC + 4
    T2: IR  ← MBR
    ```
    
    Three cycles on a **single-bus datapath** — one transfer per cycle.

</div>

<div class="fragment">

!!! tip "RTN is the bridge between ISA and hardware"
    Each line of RTN corresponds to one or more control signals. The control unit (Chapter 4) translates the RTN into actual microoperations.

</div>

---

## 3.6 — Datapath

???+ note "Three datapath designs"
    | Design | Cycles per instruction | Clock period | Trade-off |
    |---|---|---|---|
    | **Single-cycle** | 1 | Longest path | Simple, slow |
    | **Multi-cycle** | 3–5 | Short | Balanced, more control |
    | **Pipelined** | ~1 (steady state) | Short | Fast, complex |

<div class="fragment">

!!! warning "The single-cycle cost"
    In a single-cycle datapath, every instruction waits for the **slowest** one — usually `LOAD` (memory read). Fast instructions like `ADD` waste most of the cycle.

</div>

---

## 3.7 — RISC vs CISC

???+ note "The two philosophies"
    | Feature | RISC | CISC |
    |---|---|---|
    | Instruction length | Fixed | Variable |
    | Instruction count | Few, simple | Many, complex |
    | Operand sources | Registers only | Registers or memory |
    | Registers | Many (32+) | Few (8–16) |
    | Addressing modes | Few | Many |
    | Control | Hardwired | Microprogrammed |
    | CPI | Near 1 | 2–10 |
    | Code size | Larger | Smaller |
    | Pipelining | Easy | Hard |
    | Examples | MIPS, ARM, RISC-V | x86 |

<div class="fragment">

??? info "The RISC argument (1980s)"
    Most CISC instructions were **rarely used**; compilers could generate efficient code from a small RISC subset; simple instructions pipeline better; register-only operands are faster.

</div>

<div class="fragment">

!!! tip "The modern synthesis"
    Modern x86 CPUs decode CISC instructions into **RISC-like micro-operations** internally. Today's RISC/CISC distinction is about the **external ISA**, not the microarchitecture.

</div>

---

## Chapter 3 — key takeaways

<div class="fragment">

**1.** **Load-store** ISAs keep ALU operands in registers — only `LOAD`/`STORE` touch memory.

</div>

<div class="fragment">

**2.** **MIPS** has three formats: **R** (register), **I** (immediate), **J** (jump). Decode by splitting the 32-bit word.

</div>

<div class="fragment">

**3.** **Base+offset** addressing is the workhorse — it handles arrays, structs, and stack frames with the same encoding.

</div>

<div class="fragment">

**4.** **RTN** is the bridge between ISA and hardware: each line corresponds to one or more control signals.

</div>

<div class="fragment">

**5.** **RISC** = simple, fixed-length, load-store, easy to pipeline. **CISC** = complex, variable-length, memory operands, microcoded. The boundary is blurred in modern microarchitectures.

</div>

---

## Next — Chapter 4

**Pipelining and Control Unit.**

<div class="fragment">

- The five-stage RISC pipeline.
- Hazards: structural, data, control.
- **Forwarding**, **stalling**, **branch prediction**.
- **Hardwired** vs **microprogrammed** control.
- **Wilkes'** microcode model.
- Horizontal vs vertical microcode.

</div>

<div class="fragment">

Chapter 3 defined *what* the CPU does; Chapter 4 defines *how* it does it **faster** — by overlapping instructions and centralizing control.

</div>