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
  js: assets/lecture-05.js

---

# ECEG-3163

## Chapter 5 — Memory Systems

---

## 5.1 — Memory Hierarchy

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

<div class="fragment">

???+ note "Why it works — locality"
    - **Temporal locality** — recently used items are likely to be reused.
    - **Spatial locality** — items nearby in memory are likely to be used soon.

</div>

<div class="fragment">

!!! tip "The hierarchy delivers fast-memory speed at cheap-memory cost"
    A 1% miss rate with a 40 ns miss penalty and 1 ns hit time gives AMAT = 1.4 ns — nearly as fast as pure SRAM, at 1/100 the cost.

</div>

---

## 5.2 — Memory Technologies

???+ note "SRAM vs DRAM vs flash"
    | Type | Volatile? | Speed | Cost | Refresh? | Typical use |
    |---|---|---|---|---|---|
    | **SRAM** | Yes | Fastest | Expensive | No | Cache |
    | **DRAM** | Yes | Medium | Cheap | **Yes** (~64 ms) | Main memory |
    | **ROM** | No | Slow | Cheap | No | Firmware |
    | **Flash** | No | Medium | Cheap | No | SSD, USB |
    | **HDD** | No | Very slow | Very cheap | No | Bulk storage |

<div class="fragment">

???+ info "Why DRAM needs refresh"
    A DRAM cell is **one transistor + one capacitor**. The capacitor leaks charge, so each row must be refreshed every ~64 ms. SRAM uses **flip-flops** — no leakage.

</div>

---

## 5.3 — Cache Organization

???+ note "Three organizations"
    | Type | Placement | Pros | Cons |
    |---|---|---|---|
    | **Direct-mapped** | one line per block | Simple, fast | Conflict misses |
    | **Fully associative** | any line | No conflict misses | Expensive (many comparators) |
    | **Set-associative** | one set, K ways | Good compromise | Slightly more complex |

<div class="fragment">

???+ info "Address decomposition"
    For a set-associative cache:
    
    ```
    | Tag | Index | Block offset |
    ```
    
    - **Block offset** = $\log_2(\text{block size})$.
    - **Index** = $\log_2(\text{number of sets})$.
    - **Tag** = remaining bits.

</div>

---

## Worked example 5.1 — Cache parameters

!!! tip "Problem:"
    32 KB cache, 64-byte blocks, 4-way set associative, 32-bit address. Compute blocks, sets, and bit fields.

<div class="fragment">

???+ note "**Step 1 — blocks and sets.**"
    $$
    \text{blocks} = \frac{32\text{KB}}{64\text{B}} = 512, \quad
    \text{sets} = \frac{512}{4} = 128.
    $$

</div>

<div class="fragment">

??? info "**Step 2 — bit fields.**"
    - Index = $\log_2 128 = 7$ bits.
    - Offset = $\log_2 64 = 6$ bits.
    - Tag = $32 - 7 - 6 = 19$ bits.

</div>

<div class="fragment">

??? success "**Summary.**"
    | Tag | Index | Offset |
    |:---:|:---:|:---:|
    | 19 bits | 7 bits | 6 bits |

</div>

---

## 5.4 — Replacement Policies

???+ note "Four policies"
    | Policy | Description | When used |
    |---|---|---|
    | **LRU** | Evict least-recently used line | Best but expensive |
    | **FIFO** | Evict oldest line | Simple |
    | **Random** | Evict a random line | Cheap for high associativity |
    | **Optimal** | Evict line used furthest in future | Theoretical only |

<div class="fragment">

!!! warning "LRU is expensive"
    Tracking the recency order requires an ordered list per set. Real caches use **pseudo-LRU** (approximate, using a few bits per set) or **random** for high associativity.

</div>

---

## 5.5 — Write Policies

???+ note "Write-through vs write-back"
    | Policy | Behaviour | Traffic | Complexity |
    |---|---|---|---|
    | **Write-through** | Write cache **and** memory on every store | High | Simple |
    | **Write-back** | Write cache only; update memory on eviction | Low | Needs dirty bit |

<div class="fragment">

???+ info "Why write-back wins"
    Programs that repeatedly write the same line (loops updating an array) would flood memory under write-through. Write-back **amortises** many writes into one — dramatically reducing memory bandwidth.

</div>

<div class="fragment">

!!! tip "The cost of write-back"
    **Cache coherence** in multicore systems is harder — another core may read stale data from memory if a dirty line hasn't been written back. Modern CPUs use **MESI/MOESI** snooping protocols.

</div>

---

## 5.6 — AMAT

???+ abstract "Average memory access time"
    $$
    \text{AMAT} = \text{Hit time} + (\text{miss rate} \times \text{miss penalty})
    $$

<div class="fragment">

???+ note "The sensitivity"
    Because the **miss penalty** is often 20–100× the hit time, small changes in miss rate have **huge** effect on AMAT.

</div>

---

## Worked example 5.2 — AMAT

!!! tip "Problem:"
    Hit time = 2 ns, miss penalty = 40 ns, hit rate 95% and 98%.

<div class="fragment">

???+ note "**Step 1 — 95% hit rate.**"
    $$
    \text{AMAT} = 2 + 0.05 \times 40 = 2 + 2 = \mathbf{4\ \text{ns}}.
    $$

</div>

<div class="fragment">

??? info "**Step 2 — 98% hit rate.**"
    $$
    \text{AMAT} = 2 + 0.02 \times 40 = 2 + 0.8 = \mathbf{2.8\ \text{ns}}.
    $$

</div>

<div class="fragment">

??? success "**Interpretation.**"
    A **3 percentage-point** improvement in hit rate reduces AMAT by **30%**. Cache hit rate dominates memory performance — which is why L1 caches are sized carefully.

</div>

---

## 5.7 — Virtual Memory

???+ note "Definition"
    **Virtual memory** gives each process its own address space, mapped by the **MMU** to physical memory. Pages not resident in physical memory are fetched from disk (or SSD) on demand.

<div class="fragment">

???+ note "Paging vs segmentation"
    | Scheme | Size | Internal frag | External frag |
    |---|---|---|---|
    | **Paging** | Fixed (pages/frames) | **Yes** (last page) | **No** |
    | **Segmentation** | Variable | **No** | **Yes** |

</div>

<div class="fragment">

!!! tip "The TLB"
    The **translation lookaside buffer** caches recent page-table entries. With $\alpha \approx 99\%$ hit rate, virtual memory is effectively free — the extra cost is one TLB lookup per access.

</div>

---

## 5.8 — TLB and AMAT

???+ abstract "Effective access time with TLB"
    $$
    \text{EAT} = \alpha (t_{\text{TLB}} + t_{\text{mem}}) + (1-\alpha)(t_{\text{TLB}} + 2 t_{\text{mem}})
    $$
    
    where $\alpha$ is the TLB hit rate.

<div class="fragment">

???+ note "Typical numbers"
    - $t_{\text{TLB}} = 1$ ns, $t_{\text{mem}} = 50$ ns, $\alpha = 0.99$.
    - $\text{EAT} = 0.99 \times 51 + 0.01 \times 101 = 50.5 + 1.01 = \mathbf{51.5\ \text{ns}}$.
    
    TLB miss adds only 1 ns to the 50 ns base — **2%**.

</div>

---

## 5.9 — HDD vs SSD

???+ note "HDD — mechanical"
    - Platters, heads, tracks, sectors.
    - **Seek** (~9 ms) + **rotation** (~4 ms) + **transfer** (~0.03 ms for 4 KB).
    - Random IOPS ≈ **75–200**.
    - Sequential throughput ~100–200 MB/s.

<div class="fragment">

???+ note "SSD — flash"
    - NAND cells, no moving parts.
    - **No seek**, **no rotation**.
    - Random access ≈ **50–100 µs**.
    - Random IOPS ≈ **100,000+**.
    - Sequential throughput 500 MB/s – 7 GB/s (NVMe).

</div>

<div class="fragment">

!!! tip "Why SSDs changed everything"
    Databases, virtual memory, and filesystems are **random-access** workloads — exactly where HDDs hurt most. SSDs give ~1000× the random IOPS at similar cost-per-byte.

</div>

---

## Chapter 5 — key takeaways

<div class="fragment">

**1.** The memory hierarchy is justified by **locality** — it delivers fast-memory speed at cheap-memory cost.

</div>

<div class="fragment">

**2.** Cache **organization** (direct-mapped / set-associative / fully associative) trades simplicity against conflict misses.

</div>

<div class="fragment">

**3.** **AMAT** = hit time + miss rate × miss penalty. Hit rate dominates because the miss penalty is 20–100× the hit time.

</div>

<div class="fragment">

**4.** **Write-back** reduces memory traffic at the cost of a dirty bit and coherence complexity.

</div>

<div class="fragment">

**5.** **Virtual memory** gives every process a private address space; the **TLB** makes it near-free.

</div>

<div class="fragment">

**6.** **SSDs** eliminate seek and rotation; random IOPS is 1000× an HDD.

</div>

---

## Next — Chapter 6

**I/O Systems.**

<div class="fragment">

- I/O devices and modules.
- **Programmed I/O**, **interrupt-driven I/O**, **DMA**.
- Buses: PCIe, USB, SATA.
- Disk I/O performance.
- Buffering.

</div>

<div class="fragment">

Memory is fast; I/O is slow. Chapter 6 shows how to hide that gap — with DMA, buffering, and interrupt-driven design.

</div>