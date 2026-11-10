# Assessment

---

## Breakdown

| Component | Weight | Timing |
|---|:---:|---|
| **Assignments (8)** | **20%** | Weeks 2, 4, 6, 7, 9, 10, 12, 14, 16 |
| **Tests (2)** | **15%** | Week 8 (7.5%) + Week 15 (7.5%) |
| **Project (Logisim CPU)** | **15%** | Weeks 12, 16, 17 |
| **Final examination** | **50%** | Week 18 |
| **Total** | **100%** | |

---

## Assignments (20%)

Each assignment covers one chapter. Format: PDF report plus source files (Logisim `.circ`, MIPS `.asm`).

| Criterion | Weight |
|---|:---:|
| Correctness of answers | 40% |
| Rigour of reasoning | 25% |
| Implementation (Logisim / MARS) | 20% |
| Interpretation | 10% |
| Presentation | 5% |

**Late policy:** 10% per calendar day; zero after 3 days.

---

## Tests (15%)

Two tests, each 1.5 hours, closed book.

- **Test 1 (week 8, 7.5%)** — Chapters 1–3.
- **Test 2 (week 15, 7.5%)** — Chapters 4–6.

---

## Project — Logisim CPU (15%)

### Overview

Design and simulate a simple CPU in **Logisim**. Work individually.

| Deliverable | Week | Weight |
|---|:---:|:---:|
| Design proposal (ISA specification) | 12 | 10% |
| Single-cycle CPU implementation | 16 | 50% |
| Test programs + demonstration | 17 | 25% |
| Written report | 17 | 15% |

### ISA requirements

The CPU must implement at minimum:

- **Arithmetic:** ADD, SUB, INC, DEC
- **Logic:** AND, OR, XOR, NOT
- **Memory:** LOAD, STORE
- **Control:** JUMP, BEQ, BNE
- **Registers:** at least 4 general-purpose 8-bit or 16-bit registers, plus PC and IR

### Optional extensions (extra credit)

- **Pipelined** implementation (IF/ID/EX/MEM/WB).
- **Interrupt support**.
- **Additional instructions** (multiply, divide, shift).

### Project rubric

| Criterion | Weight |
|---|:---:|
| Correct datapath and control | 30% |
| ISA completeness and correctness | 20% |
| Test programs demonstrably working | 25% |
| Clarity of report | 15% |
| Innovation (pipelining, extensions) | 10% |

### Submission

A `.zip` containing:

- The Logisim `.circ` file.
- Test program files (machine code or an assembler script).
- A `README.md` explaining how to load and run.
- The report PDF.

---

## Final examination (50%)

- **Format.** 3 hours, closed book.
- **Content.** Whole syllabus, weighted by chapter:

| Chapter | Weight |
|---|:---:|
| 1 Introduction and Performance | 10% |
| 2 Computer Arithmetic | 15% |
| 3 Instruction Sets | 15% |
| 4 Pipelining and Control | 20% |
| 5 Memory Systems | 20% |
| 6 I/O Systems | 10% |
| 7 Advanced Topics | 10% |

- **Structure.** 6–8 questions combining numerical problems (performance, arithmetic), design problems (pipeline, cache, control), and short-answer theory.

---

## Grade boundaries

| Grade | Range |
|---|---|
| A | 90–100 |
| A− | 85–89 |
| B+ | 80–84 |
| B | 75–79 |
| B− | 70–74 |
| C+ | 65–69 |
| C | 60–64 |
| F | < 60 |

---

## Academic integrity

All submitted work must be your own. Logisim circuits are submitted individually; collaboration on *understanding* is encouraged, but sharing circuit files is plagiarism.