# Solutions — Chapter 6: I/O Systems

[:octicons-arrow-left-24: Back to Chapter 6](../chapters/06-io-systems.md)

Answers to **Section F** of the [Exam Bank](../exam-bank.md).

---

## F1 — Programmed I/O [2]

**Answer: (c)** continuously polls the device status register.

**Why.** In programmed I/O, the CPU issues a request then **busy-waits** in a loop, checking the device's ready status. No interrupt is generated; the CPU does all the work.

**Why the others are wrong.**

- (a) Being interrupted is **interrupt-driven I/O**.
- (b) DMA is a distinct transfer mechanism.
- (d) An I/O processor is a separate unit, not part of the CPU.

---

## F2 — DMA reduces CPU load [2]

**Answer: (b)** the DMA controller transfers data between memory and device without CPU intervention.

**Why.** Once the CPU configures the DMA controller (source address, destination, length, direction) and starts the transfer, the DMA controller **takes ownership of the bus** and moves data directly. The CPU resumes executing other instructions and receives a **single interrupt** when the transfer finishes.

**Why the others are wrong.**

- (a) The CPU is not slower; the point is to free it from an active role.
- (c) Fewer interrupts is a **consequence**, not the mechanism.
- (d) Whole-sector transfers are a property of the device, not of DMA.

---

## F3 — Programmed vs interrupt vs DMA [4]

**Step 1 — CPU involvement.**

- **Programmed:** 100% CPU — the CPU busy-waits, checking the status register in a tight loop until data is ready.
- **Interrupt-driven:** CPU is free between transfers; the device raises an interrupt per completed transfer. Per-transfer CPU overhead is moderate.
- **DMA:** minimal CPU involvement. The CPU sets up the transfer once and can do other work.

**Step 2 — latency per transfer.**

- **Programmed:** latency equals the wait time (potentially long for slow devices). The CPU is blocked.
- **Interrupt-driven:** latency is the interrupt-handling time plus the wait. Better than programmed, but per-transfer.
- **DMA:** latency is the setup time plus the DMA transfer time. Highest per-setup latency, but throughput dominates for bulk transfers.

**Step 3 — suitability.**

| Device class | Programmed | Interrupt | DMA |
|---|:---:|:---:|:---:|
| Slow interactive (keyboard) | Poor | **Best** | Poor |
| Medium-speed (network) | Poor | Good | **Best** |
| High-speed bulk (disk, SSD) | Terrible | Bad | **Best** |
| Simple embedded | **Best** | Okay | Not available |

**Rule of thumb.** Slow devices → interrupt. Bulk devices → DMA. Simple, low-cost systems → programmed polling.

---

## F4 — Disk service time [4]

**Given.**

- Average seek time = **9 ms**
- Rotational speed = **7200 RPM**
- Transfer rate = **150 MB/s**
- Block size = **4 KB**

**Step 1 — average rotational latency.**

One revolution takes

$$
T_{\text{rot}} = \frac{60\ \text{s}}{7200\ \text{RPM}} = 8.333\ \text{ms}.
$$

Average rotational latency = **half a revolution**:

$$
\overline{L_{\text{rot}}} = \frac{T_{\text{rot}}}{2} = \frac{8.333}{2} = \mathbf{4.17\ \text{ms}}.
$$

**Step 2 — transfer time for 4 KB.**

$$
T_{\text{transfer}} = \frac{4096}{150 \times 10^6}\ \text{s} \approx 2.73 \times 10^{-5}\ \text{s} \approx \mathbf{0.027\ \text{ms}}.
$$

**Step 3 — total average service time.**

$$
T_{\text{service}} = \text{seek} + \text{rotation} + \text{transfer} = 9 + 4.17 + 0.027 \approx \mathbf{13.2\ \text{ms}}.
$$

**Sanity check — IOPS.**

$$
\text{IOPS} = \frac{1}{0.0132\ \text{s}} \approx 76.
$$

So a typical 7200-RPM HDD delivers ~75–80 random IOPS. An SSD delivers ~100,000+ random IOPS on the same workload — a factor of ~1000× improvement, exactly the difference that made SSDs transformative for database and VM workloads.