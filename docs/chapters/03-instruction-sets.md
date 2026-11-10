# Chapter 3 — Instruction Sets and CPU Structure

!!! abstract "Chapter at a glance"
    **Contact hours:** 2 lecture + 3 tutorial/lab · **Home study:** ~5 h

    **Coverage:** instruction set taxonomy · instruction formats · addressing modes · operand types and operations · CPU registers and datapath · register transfer notation · RISC vs CISC.

---

## Learning outcomes

1. Classify instruction sets by operand source (stack, accumulator, general-purpose).
2. Decode **R, I, and J** instruction formats.
3. Compute effective addresses for the eight classic addressing modes.
4. Write **register transfer notation** (RTN) for a simple datapath.
5. Compare RISC and CISC on code size, CPI, pipeline depth, and compiler complexity.

---

## 3.1 Instruction set taxonomy

Instruction sets differ in **where the operands come from**:

| Class | Operand source | Stack effect | Example |
|---|---|---|---|
| **Stack** | Top of a pushdown stack | Push/pop | Old HP calculators, Java bytecode |
| **Accumulator** | A single accumulator | Implicit | Early 8-bit CPUs |
| **General-purpose** | Many registers | Explicit | MIPS, x86, ARM |
| **Register-memory** | One operand in memory | Explicit | x86 |
| **Load-store** | All operands in registers; separate load/store | Explicit | MIPS, ARM, RISC-V |

The **load-store** model is the foundation of RISC (Section 3.6).

---

## 3.2 Instruction formats

### 3.2.1 MIPS R-type (register-to-register)

```
| opcode 6 | rs 5 | rt 5 | rd 5 | shamt 5 | funct 6 |
```

Example: `add rd, rs, rt` → `000000 rs rt rd 00000 100000`.

### 3.2.2 MIPS I-type (immediate)

```
| opcode 6 | rs 5 | rt 5 | immediate 16 |
```

Example: `addi rt, rs, imm16`.

### 3.2.3 MIPS J-type (jump)

```
| opcode 6 | target address 26 |
```

Target = `(PC+4)[31:28] || target << 2`.

### Worked example 3.1 — decode `0x012A4020`

Binary: `0000 0001 0010 1010 0100 0000 0010 0000`

Break into fields:

- opcode = `000000` → R-type.
- rs = `01001` = 9.
- rt = `01010` = 10.
- rd = `01000` = 8.
- shamt = `00000`.
- funct = `100000` = 0x20 = `add`.

**Decoded:** `add $8, $9, $10`.

---

## 3.3 Addressing modes

| Mode | Effective address (EA) | Memory accesses |
|---|---|---|
| **Immediate** | operand is in instruction | 0 |
| **Register** | operand is in register | 0 |
| **Direct** | EA = address field | 1 |
| **Indirect** | EA = M[address field] | 2 |
| **Register indirect** | EA = R[field] | 1 |
| **Displacement (base+offset)** | EA = R[base] + offset | 1 |
| **Indexed** | EA = R[index] + address field | 1 |
| **Relative** | EA = PC + offset | 1 |

The **base+offset** mode is the workhorse of modern ISAs — it supports array indexing, structure field access, and stack frame access with a single encoding.

---

## 3.4 CPU registers

A general-purpose CPU has these registers:

| Register | Purpose |
|---|---|
| **PC** | Program counter — address of the next instruction |
| **IR** | Instruction register — current instruction |
| **MAR** | Memory address register |
| **MBR / MDR** | Memory buffer / data register |
| **AC** | Accumulator (in accumulator ISAs) |
| **R0..Rn** | General-purpose registers |
| **SP** | Stack pointer |
| **FLAGS / PSW** | Condition codes (zero, carry, sign, overflow) |

### 3.4.1 Register transfer notation

RTN describes data movement and operations:

```
MAR ← PC
M[MAR] → MBR
IR ← MBR
PC ← PC + 4
```

Each line executes in one cycle (or a small number of cycles depending on the datapath).

### Worked example 3.2 — fetch cycle in RTN

```
T0: MAR ← PC
T1: MBR ← M[MAR], PC ← PC + 4
T2: IR  ← MBR
```

Three cycles, assuming a **single-bus datapath** where each transfer takes one cycle.

---

## 3.5 Datapath

The datapath is the collection of registers, ALU, and internal buses that carry out the operations.

A **single-cycle** datapath executes one instruction per cycle. Fetch, decode, execute, memory, and write-back all happen in the same cycle — the clock must be long enough for the slowest path.

A **multi-cycle** datapath breaks the instruction into 3–5 phases, each taking one cycle. This allows a shorter clock period but adds control complexity.

A **pipelined** datapath overlaps the phases of multiple instructions (Chapter 4).

---

## 3.6 RISC vs CISC

| Feature | RISC | CISC |
|---|---|---|
| **Instruction length** | Fixed | Variable |
| **Instruction count** | Simple, few | Complex, many |
| **Operands** | Registers only (load-store) | Registers or memory |
| **Registers** | Many (32+) | Few (8–16) |
| **Addressing modes** | Few | Many |
| **Control** | Hardwired | Microprogrammed |
| **CPI** | Near 1 | Higher (2–10) |
| **Code size** | Larger | Smaller |
| **Pipelining** | Easy | Hard |
| **Examples** | MIPS, ARM, RISC-V | x86 |

**The RISC argument (Patterson & Hennessy, 1980s).** Most CISC instructions were rarely used; compilers could generate efficient code from a small RISC subset; simple instructions pipeline better; register-only operands are faster.

**The modern synthesis.** Modern x86 (Intel Core, AMD Zen) internally decodes CISC instructions into **RISC-like micro-operations** — so today's distinction is largely about the **external ISA**, not the internal microarchitecture.

---

## 3.7 Quiz

1. Classify the MIPS instruction set (stack, accumulator, or general-purpose load-store).
2. Decode `0x012A4020`.
3. Give the effective address for `lw $t0, 8($sp)`.
4. Write RTN for `add $1, $2, $3`.
5. Compare RISC and CISC on pipeline depth and code size.

---

## Answer key

??? success "Click to reveal"

    1. Load-store general-purpose.
    2. `add $8, $9, $10`.
    3. `EA = R[sp] + 8`.
    4. `R[1] ← R[2] + R[3]`.
    5. RISC: shorter pipeline, larger code; CISC: longer pipeline, smaller code.

---

## Further reading

- Stallings, §12.1–§13.3.
- Mano, §5.1–§7.4.
- Patterson & Hennessy, §2.1–§2.9.