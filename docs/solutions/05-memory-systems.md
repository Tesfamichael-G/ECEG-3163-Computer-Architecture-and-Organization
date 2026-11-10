# Solutions — Chapter 5: Memory Systems

[:octicons-arrow-left-24: Back to Chapter 5](../chapters/05-memory-systems.md)

Answers to **Section E** of the [Exam Bank](../exam-bank.md).

---

## E1 — Justification for memory hierarchy [2]

**Answer: (c)** locality of reference.

**Why.** Both temporal and spatial locality mean that the data used by a program is clustered in time and space. This clustering is what allows a small, fast top of the hierarchy to serve most accesses, delivering the **average** performance of a fast memory at the **cost** of a cheap memory.

---

## E2 — DRAM refresh [2]

**Answer: (b)** it stores each bit as charge on a capacitor that leaks.

**Why.** A DRAM cell is one transistor plus one capacitor. The capacitor holds charge that represents the bit, but leakage drains it. Each row must be refreshed every ~64 ms or the bits are lost.

**Why the others are wrong.**

- (a) DRAM does not use flip-flops — SRAM does.
- (c) Speed is not the reason for refresh.
- (d) Volatility is a **consequence** of the same physics, not the mechanism.

---

## E3 — Direct-mapped set selection [2]

**Answer: (c)** the index bits.

**Why.** A direct-mapped cache has exactly one allowed line per block:

$$
\text{line} = \text{block}\bmod N,
$$

where $N$ is the number of lines. The block offset selects the byte; the tag confirms a hit; the index (bits between offset and tag) selects the line.

---

## E4 — Write-back advantage [2]

**Answer: (b)** writes accumulate in the cache and reach memory only when the block is evicted.

**Why.** With write-back, each write updates the cache line (and sets a dirty bit); the memory is only updated when the dirty line is evicted. For programs that repeatedly update the same line, this **amortises many memory writes into one**.

**Why the others are wrong.**

- (a) Every write going to memory is exactly **write-through**.
- (c) Both policies cache reads.
- (d) Block size is independent of the write policy.

---

## E5 — Cache parameters [4]

**Given.** 32 KB total, 64-byte blocks, 4-way set associative, 32-bit address.

**Step 1 — number of blocks.**

$$
\text{Blocks} = \frac{32 \text{ KB}}{64 \text{ B}} = \frac{32 \times 1024}{64} = \frac{32768}{64} = 512.
$$

**Step 2 — number of sets.**

With 4-way associativity, each set holds 4 lines:

$$
\text{Sets} = \frac{512}{4} = 128.
$$

**Step 3 — index bits.**

$$
\text{Index bits} = \log_2 128 = 7.
$$

**Step 4 — block-offset bits.**

$$
\text{Offset bits} = \log_2 64 = 6.
$$

**Step 5 — tag bits.**

$$
\text{Tag bits} = 32 - (\text{index} + \text{offset}) = 32 - 7 - 6 = \mathbf{19}.
$$

**Summary table.**

| Component | Bits | Count |
|---|:---:|:---:|
| Tag | 19 | — |
| Index | 7 | 128 sets |
| Block offset | 6 | 64 B blocks |
| **Total** | **32** | |

---

## E6 — AMAT at two hit rates [4]

**Formula.**

$$
\text{AMAT} = \text{Hit time} + (\text{Miss rate} \times \text{Miss penalty})
$$

**Case 1 — 95% hit rate.**

- Hit time = 2 ns
- Miss rate = 0.05
- Miss penalty = 40 ns

$$
\text{AMAT}_{0.95} = 2 + 0.05 \times 40 = 2 + 2 = \mathbf{4\ \text{ns}}.
$$

**Case 2 — 98% hit rate.**

- Miss rate = 0.02

$$
\text{AMAT}_{0.98} = 2 + 0.02 \times 40 = 2 + 0.8 = \mathbf{2.8\ \text{ns}}.
$$

**Interpretation.** A **3 percentage-point** improvement in hit rate reduces AMAT by **30%** (from 4.0 ns to 2.8 ns). This is why cache hit rate dominates memory performance — the miss penalty is so large that small changes in hit rate produce large changes in AMAT.

---

## E7 — Paging vs segmentation [4]

**Step 1 — paging.**

- Fixed-size **pages** (logical) and **frames** (physical), e.g. 4 KB.
- Any page fits in any frame → **no external fragmentation**.
- The last page of a process may be partially filled → **internal fragmentation** up to page size − 1.

**Step 2 — segmentation.**

- Variable-size **logical segments** (code, data, stack).
- Segments are allocated with exact size → **no internal fragmentation**.
- Free memory between segments is scattered in variable-size holes → **external fragmentation**.

**Step 3 — summary.**

| Scheme | Internal frag | External frag |
|---|:---:|:---:|
| **Paging** | **Yes** (last page) | **No** |
| **Segmentation** | **No** | **Yes** |

**Modern systems** use **segmentation with paging** — each segment is paged, combining logical structure with fragmentation-free allocation.

---

## E8 — SSD has no seek time [2]

An **SSD** stores data in NAND flash cells with **no moving parts** — there is no head to move and no platter to rotate. Therefore:

- **Seek time = 0** (no head movement).
- **Rotational latency = 0** (no rotation).

The access time is determined purely by the flash-controller latency and bus transfer, typically ~50–100 µs for reads.

**Effect on random vs sequential.**

- **HDD**: random reads are dominated by seek + rotation (~10 ms), so random is ~100× slower than sequential (which amortises seek across the transfer).
- **SSD**: random reads cost almost the same as sequential reads at the block level; throughput for random may still be somewhat lower due to smaller per-request transfer, but there is **no order-of-magnitude penalty**.

This is why databases, filesystems, and virtual-memory swap became dramatically faster when SSDs replaced HDDs — random-access workloads are exactly where the HDD's mechanics hurt most.