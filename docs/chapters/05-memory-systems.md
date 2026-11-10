# Chapter 5 — Memory Systems

!!! abstract "Chapter at a glance"
    **Coverage:** memory hierarchy · SRAM, DRAM, ROM, flash · main memory organization · cache organization · replacement and write policies · AMAT · virtual memory · TLB · HDD and SSD.

---

## Learning outcomes

1. Explain the memory hierarchy and the principle of **locality of reference**.
2. Compare SRAM, DRAM, ROM, and flash on speed, cost, and volatility.
3. Design a cache (direct-mapped, set-associative, fully associative) and compute AMAT.
4. Describe **virtual memory** and the role of the **TLB**.
5. Compare HDD and SSD on latency and throughput.

---

## 5.1 Memory hierarchy

Memory is organised as a hierarchy — smaller and faster technologies at the top, larger and slower at the bottom:

```
Registers          (< 1 KB, < 1 ns)
    ↓
L1 Cache           (32 KB, ~1 ns)
    ↓
L2 Cache           (256 KB, ~5 ns)
    ↓
L3 Cache           (8 MB, ~20 ns)
    ↓
Main Memory (DRAM) (8 GB, ~80 ns)
    ↓
SSD                (256 GB, ~100 µs)
    ↓
HDD                (4 TB, ~10 ms)
```

The hierarchy works because of **locality of reference**:

- **Temporal locality** — recently used items are likely to be used again.
- **Spatial locality** — items near recently used items are likely to be used soon.

---

## 5.2 Memory technologies

| Type | Volatile? | Speed | Cost | Typical use |
|---|---|---|---|---|
| **SRAM** | Yes | Fastest | Expensive | Cache |
| **DRAM** | Yes | Medium | Cheap | Main memory |
| **ROM** | No | Slow | Cheap | Firmware |
| **Flash** | No | Medium | Cheap | SSD, USB |
| **HDD** | No | Very slow | Very cheap | Bulk storage |

**DRAM refresh:** each cell stores a bit as charge on a capacitor. Charge leaks, so each row must be **refreshed** every 64 ms. SRAM uses flip-flops — no refresh needed.

---

## 5.3 Main memory organization

Memory is addressed by **word** or by **byte**. Access can be:

- **Word-at-a-time** — one word per access.
- **Interleaved** — multiple memory banks accessed in parallel, effectively multiplying bandwidth.

**Interleaving** with $k$ banks allows $k$ concurrent accesses, one per bank, amortising latency.

---

## 5.4 Cache organization

The three classic cache organisations differ in **where a given memory block may be placed**:

### 5.4.1 Direct-mapped

Each block has exactly one allowed cache line: $\text{line} = \text{block}\bmod N$.

**Pros:** simple, fast (one comparator).
**Cons:** conflict misses (two blocks mapping to the same line thrash).

### 5.4.2 Fully associative

Any block can go anywhere.

**Pros:** no conflict misses.
**Cons:** requires comparing the tag against every line — expensive.

### 5.4.3 Set-associative

A compromise: the cache has $S$ sets, each with $K$ ways. A block goes to one set, but within the set any way.

**Typical values:** 2-, 4-, 8-way set associative. Modern L1 caches are usually 4- or 8-way.

### 5.4.4 Address decomposition

For a **set-associative** cache, the address splits into:

```
| Tag | Index | Block offset |
```

- **Block offset** bits: $\log_2(\text{block size})$.
- **Index** bits: $\log_2(\text{number of sets})$.
- **Tag** bits: remaining bits.

### Worked example 5.1 — cache parameters

**Given:** 32 KB cache, 64-byte blocks, 4-way set associative, 32-bit address.

- Blocks = $32\text{ KB} / 64\text{ B} = 512$.
- Sets = $512 / 4 = 128$.
- Index bits = $\log_2 128 = 7$.
- Block offset bits = $\log_2 64 = 6$.
- Tag bits = $32 - 7 - 6 = 19$.

---

## 5.5 Cache replacement and write policies

### 5.5.1 Replacement policies

| Policy | Description |
|---|---|
| **LRU** | Evict the least-recently used line |
| **FIFO** | Evict the oldest line |
| **Random** | Evict a random line |
| **Optimal** | Evict the line used furthest in the future (theoretical) |

LRU is best but expensive to implement for high associativity. Practical caches use approximations (pseudo-LRU, or random for high associativity).

### 5.5.2 Write policies

- **Write-through** — write both cache and memory on every write. Simple, but heavy traffic.
- **Write-back** — write to cache only; memory is updated when the line is evicted (dirty line). Less traffic, but requires a dirty bit and complicates coherence in multicore systems.

---

## 5.6 AMAT — average memory access time

!!! abstract "AMAT formula"
    $$\text{AMAT} = \text{Hit time} + \text{Miss rate} \times \text{Miss penalty}$$

**Worked example 5.2 — AMAT**

**Given:** Hit time = 2 ns, miss penalty = 40 ns, hit rate 95%.

$$
\text{AMAT} = 2 + 0.05 \times 40 = 2 + 2 = 4\,\text{ns}.
$$

With hit rate 98%:

$$
\text{AMAT} = 2 + 0.02 \times 40 = 2 + 0.8 = 2.8\,\text{ns}.
$$

A 3% improvement in hit rate reduces AMAT by 30% — cache hit rate is **critical**.

---

## 5.7 Virtual memory

!!! note "Virtual memory"
    **Virtual memory** gives each process its own address space, mapped by hardware (the **MMU**) to physical memory. Blocks (pages) not resident in physical memory are fetched from disk on demand.

### 5.7.1 Paging vs. segmentation

- **Paging** — fixed-size pages and frames. No external fragmentation; internal fragmentation on the last page of a process.
- **Segmentation** — variable-size logical segments. No internal fragmentation; external fragmentation of physical memory.

### 5.7.2 The TLB

The **translation lookaside buffer (TLB)** caches recent page-table entries. On a TLB hit, translation is fast; on a miss, the page table is consulted (possibly multiple levels).

**Effective access time:**

$$
\text{EAT} = \alpha (t_{\text{TLB}} + t_{\text{mem}}) + (1-\alpha)(t_{\text{TLB}} + 2 t_{\text{mem}})
$$

where $\alpha$ is the hit ratio. With $\alpha$ near 99%, virtual memory is effectively free.

---

## 5.8 Secondary storage

### 5.8.1 HDD (hard disk drive)

- Rotating platters, read/write heads, tracks, sectors.
- **Seek time** (~5–10 ms) — move the head.
- **Rotational latency** (~4 ms at 7200 RPM) — wait for the sector.
- **Transfer time** — read the block.

**Random I/O is dominated by seek + rotation**, so HDD performance for random small reads is ~100–200 IOPS.

### 5.8.2 SSD (solid-state drive)

- NAND flash, no moving parts.
- **No seek time** — random access is ~100 µs, sequential is ~50 µs.
- **Wear-leveling** — flash cells wear out after ~10⁴–10⁵ erases.
- **Write amplification** — a small write may trigger a larger internal write.

**Modern systems** use SSDs for the primary store and HDDs (or cloud object storage) for bulk/backup.

---

## 5.9 Quiz

1. Explain the principle of locality and its two forms.
2. Why does DRAM need refresh? Why does SRAM not?
3. Compute the number of tag bits for a 32-bit address in a 64 KB, 4-way set-associative cache with 32-byte blocks.
4. Compute AMAT for a cache with hit time 1 ns, miss penalty 20 ns, and hit rate 97%.
5. Compare HDD and SSD for random-read workloads.

---

## Answer key

??? click "Click to reveal"

    1. Temporal (reuse of recent items), spatial (reuse of nearby items).
    2. DRAM stores charge on a capacitor that leaks; SRAM uses flip-flops, no leakage.
    3. Blocks = 64KB/32 = 2048; sets = 2048/4 = 512; index = 9; offset = 5; tag = 32-9-5 = 18.
    4. AMAT = 1 + 0.03 × 20 = 1.6 ns.
    5. SSD has no seek/rotation, so random reads are ~100× faster than HDD.

---

## Further reading

- Stallings, §4.1–§4.5, §5.1–§5.4, §6.1–§6.6.
- Patterson & Hennessy, §5.1–§5.8.