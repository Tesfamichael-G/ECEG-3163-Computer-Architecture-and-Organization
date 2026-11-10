# Solutions — Chapter 3: Instruction Sets

[:octicons-arrow-left-24: Back to Chapter 3](../chapters/03-instruction-sets.md)

Answers to **Section C** of the [Exam Bank](../exam-bank.md).

---

## C1 — Base+offset addressing mode [2]

**Answer: (c)** indexed / base+offset.

**Why.**

- **Immediate** — the operand is encoded inside the instruction itself; no address computation.
- **Direct** — the address in the instruction is the effective address; no base register.
- **Register indirect** — the effective address is held in a register.
- **Indexed / base+offset** — EA = R[base] + offset. This is exactly the wording of the question.

---

## C2 — MIPS R-type fields [2]

**Answer: (c)** opcode, rs, rt, rd, shamt, funct.

**MIPS R-type encoding (32 bits):**

| Field | Bits | Purpose |
|---|---|---|
| opcode | 6 | Always `000000` for R-type |
| rs | 5 | First source register |
| rt | 5 | Second source register |
| rd | 5 | Destination register |
| shamt | 5 | Shift amount (0 for non-shift) |
| funct | 6 | Operation selector (e.g. `100000` = add) |

**Why the other options are wrong.**

- (a) opcode + address is roughly a jump (J-type).
- (b) opcode, rs, rt, immediate is I-type.
- (d) opcode + target address — incomplete for J-type (which has 26-bit target).

---

## C3 — RISC characteristics [2]

**Answer: (b)** fixed-length instructions, load-store architecture, many registers.

**Why RISC matches (b):**

- **Fixed-length** instructions decode in a single cycle.
- **Load-store** keeps all ALU operands in registers.
- **Many registers** reduce memory traffic.
- **Simple addressing modes** — designed for pipelining.

**Why the other options are wrong.**

- (a) Variable-length instructions + microcode are **CISC** traits (x86).
- (c) Complex addressing modes are a **CISC** trait.
- (d) Microcoded execution is a **CISC** implementation choice.

---

## C4 — Decode `0x012A4020` [4]

**Step 1 — binary.**

```
0x012A4020 = 0000 0001 0010 1010 0100 0000 0010 0000
```

**Step 2 — split into R-type fields.**

| Field | Bits | Value |
|---|---|---|
| opcode | `000000` | R-type |
| rs | `01001` | 9 |
| rt | `01010` | 10 |
| rd | `01000` | 8 |
| shamt | `00000` | 0 |
| funct | `100000` | `add` |

**Step 3 — decode the operation.**

- funct `100000` = `add`.
- Opcode `000000` confirms it is an R-type arithmetic/logic instruction.

**Step 4 — assembly.**

$$
\boxed{\texttt{add\ \$8,\ \$9,\ \$10}}
$$

**Meaning.** `$8 = $9 + $10`.

---

## C5 — Memory accesses per addressing mode [3]

The operand must be fetched. Count memory reads to obtain it:

| Mode | Effective address | Memory reads to fetch operand | Why |
|---|---|:---:|---|
| **Immediate** | operand embedded in instruction | **0** | The operand is already in the IR after fetch. |
| **Register** | operand in a register | **0** | Read from register file, not memory. |
| **Register indirect** | EA = R[r] | **1** | One memory read at the address in R[r]. |
| **Direct** | EA = address field | **1** | One memory read at the address encoded in the instruction. |
| **Indirect** | EA = M[address field] | **2** | First read fetches the address, second read fetches the operand. |

**Summary.** Immediate and register = 0 memory accesses; register-indirect and direct = 1; indirect = 2.

---

## C6 — PC and IR in the instruction cycle [2]

The **program counter (PC)** stores the address of the next instruction to fetch; it is incremented after each fetch so the CPU knows where to continue. The **instruction register (IR)** stores the instruction just fetched so the control unit can decode it and generate the correct control signals. Without these two registers, the CPU would have no way to sequence through memory systematically or to hold an instruction long enough to execute it.

**In one sentence.** The PC sequences the fetch, the IR holds what was fetched — together they realise the Von Neumann fetch-decode-execute loop.