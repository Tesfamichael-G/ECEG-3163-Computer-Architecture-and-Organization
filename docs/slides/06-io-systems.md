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
  js: assets/lecture-06.js

---

# ECEG-3163

## Chapter 6 — I/O Systems

---

## 6.1 — I/O Device Classes

???+ note "Three classes"
    | Class | Examples | Transfer unit | Access |
    |---|---|---|---|
    | **Block** | HDD, SSD, USB drive | Block (512 B – 4 KB) | Random |
    | **Character** | Keyboard, serial | Byte | Sequential |
    | **Network** | Ethernet, Wi-Fi | Packet | Streaming |

<div class="fragment">

!!! tip "Each class has a natural transfer mechanism"
    - Block → **DMA** (large, contiguous).
    - Character → **interrupt** (small, unpredictable).
    - Network → **DMA + interrupt** (packet-driven).

</div>

---

## 6.2 — I/O Modules

???+ note "What is an I/O module?"
    An **I/O controller** sits between the bus and the device. It contains:
    - **Data registers** — hold the transferred data.
    - **Status register** — ready / busy / error.
    - **Control register** — start, direction.

<div class="fragment">

???+ info "Two addressing schemes"
    - **Memory-mapped I/O** — device registers appear at memory addresses; accessed via normal load/store.
    - **Isolated I/O** — separate address space; dedicated `IN`/`OUT` instructions.

</div>

<div class="fragment">

!!! tip "Memory-mapped wins in modern systems"
    Uniform addressing simplifies the ISA and lets the compiler use normal instructions to access device registers.

</div>

---

## 6.3 — Programmed I/O (Polling)

???+ note "The algorithm"
    ```
    while (!(status & READY)) ;
    data = device.data;
    ```
    The CPU **busy-waits** in a loop, checking the status register.

<div class="fragment">

???+ info "Cost"
    - 100% CPU utilisation while waiting.
    - Simple to implement.
    - Only acceptable when the device is **very fast** (ready on the next cycle) or the CPU has nothing else to do.

</div>

<div class="fragment">

!!! warning "When programmed I/O is a mistake"
    For a 10 ms disk, the CPU wastes 10⁷ cycles polling — billions of wasted cycles per second if the disk is active.

</div>

---

## 6.4 — Interrupt-Driven I/O

???+ note "The algorithm"
    1. CPU issues a read command to the device.
    2. Device transfers data in the background.
    3. When ready, the device raises an **interrupt**.
    4. The CPU enters an **interrupt handler**, which reads the data.

<div class="fragment">

???+ info "Why it wins"
    The CPU is **free between transfers** — it can run other processes. For slow devices (keyboard, mouse), the cost per transfer is negligible.

</div>

<div class="fragment">

???+ info "Interrupt latency"
    - Save registers, switch context, run handler, restore — ~100 to 1000 cycles per interrupt.
    - For **high-frequency** devices (1 Gbps NIC), interrupts would overwhelm the CPU.
    - Solution: **interrupt coalescing** — batch multiple events into one interrupt.

</div>

---

## 6.5 — Direct Memory Access (DMA)

???+ note "The algorithm"
    1. CPU programs the DMA controller: source, destination, length, direction.
    2. DMA controller takes ownership of the bus.
    3. Transfers data **directly** between memory and device.
    4. Raises **one interrupt** when done.

<div class="fragment">

???+ info "Why it wins"
    - The CPU does **no per-byte work** — it just configures the transfer once.
    - Throughput bounded by the bus and DMA controller, not the CPU.
    - Essential for disks, SSDs, and high-speed networking.

</div>

<div class="fragment">

??? warning "The trade-off"
    The DMA controller **competes with the CPU for the memory bus** — while DMA transfers, the CPU's own memory accesses are slowed (**cycle stealing**). Modern systems minimise this with **direct cache access** and **coherent DMA**.

</div>

---

## 6.6 — Programmed vs Interrupt vs DMA

???+ note "Comparison"
    | Mechanism | CPU involvement | Per-transfer latency | Best for |
    |---|---|---|---|
    | **Programmed** | 100% busy-wait | Device speed | Simple embedded; very fast devices |
    | **Interrupt** | Free between transfers | Interrupt overhead | Slow interactive devices |
    | **DMA** | Minimal (setup only) | Setup + DMA time | Bulk transfers (disk, network) |

<div class="fragment">

!!! tip "Decision rule"
    **Slow, rare** → interrupt. **Bulk, frequent** → DMA. **Simple, no interrupt controller** → polling.

</div>

---

## Worked example 6.1 — Disk I/O time

!!! tip "Problem:"
    9 ms seek, 7200 RPM, 150 MB/s transfer, 4 KB block.

<div class="fragment">

???+ note "**Step 1 — rotation.**"
    $T_{\text{rot}} = 60/7200 = 8.33$ ms. Average latency = half = **4.17 ms**.

</div>

<div class="fragment">

??? info "**Step 2 — transfer.**"
    $$
    T_{\text{xfer}} = \frac{4096}{150 \times 10^6} \approx 0.027\ \text{ms}.
    $$

</div>

<div class="fragment">

??? success "**Step 3 — total.**"
    $$
    T_{\text{service}} = 9 + 4.17 + 0.027 \approx \mathbf{13.2\ \text{ms}}.
    $$
    
    Random IOPS $\approx 1 / 0.0132 \approx \mathbf{76}$.

</div>

---

## 6.7 — Buses

???+ note "From shared bus to point-to-point"
    | Bus | Purpose | Speed |
    |---|---|---|
    | **Front-side** (obsolete) | CPU ↔ memory | ~1.6 GT/s |
    | **PCIe 4.0 ×16** | Peripheral expansion | ~32 GB/s |
    | **USB 3.2** | External | 20 Gb/s |
    | **SATA 3** | Storage | 6 Gb/s |
    | **NVMe ×4** | Storage | ~7 GB/s |

<div class="fragment">

???+ info "Why PCIe replaced the shared bus"
    A **shared bus** forces all devices to arbitrate for the same wires — one slow device blocks all others. **PCIe** is **point-to-point**: each device has its own lane. Bandwidth scales with lane count.

</div>

---

## 6.8 — Buffering

???+ note "Definition"
    **Buffering** holds data temporarily between producer and consumer to absorb speed mismatches.

<div class="fragment">

???+ note "Three types"
    - **Single** — one buffer; producer fills while consumer drains.
    - **Double** — two buffers alternate; producer works while consumer drains.
    - **Circular** — ring of buffers for streaming.

</div>

<div class="fragment">

!!! tip "Why buffering matters"
    - CPU is 1000× faster than disk — without buffering, the CPU would stall on every write.
    - Network delivers 1500-byte packets, but file systems write 4 KB blocks — buffering aggregates.
    - Buffering gives a **consistent snapshot** even if the source changes.

</div>

---

## Chapter 6 — key takeaways

<div class="fragment">

**1.** **I/O is 100–1000× slower than the CPU** — the whole I/O subsystem is designed to hide this gap.

</div>

<div class="fragment">

**2.** **Programmed I/O** is simple but wastes the CPU; use it only for very fast devices or bare-metal systems.

</div>

<div class="fragment">

**3.** **Interrupt-driven I/O** frees the CPU between transfers — the standard for slow interactive devices.

</div>

<div class="fragment">

**4.** **DMA** moves data without CPU involvement — essential for bulk transfers (disk, network).

</div>

<div class="fragment">

**5.** **PCIe** point-to-point replaced shared-bus architectures — bandwidth scales with lanes.

</div>

<div class="fragment">

**6.** **Buffering** absorbs the speed mismatch between producer and consumer.

</div>

---

## Next — Chapter 7

**Advanced Topics.**

<div class="fragment">

- **Flynn's taxonomy** (SISD, SIMD, MISD, MIMD).
- Multicore processors.
- **GPU** computing.
- Low-level data structures.
- Introduction to operating systems.

</div>

<div class="fragment">

Chapters 1–6 built a single fast CPU. Chapter 7 asks: what happens when we need **many** CPUs?

</div>