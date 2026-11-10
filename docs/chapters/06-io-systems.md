# Chapter 6 — I/O Systems

!!! abstract "Chapter at a glance"
    **Coverage:** I/O devices · I/O modules · programmed I/O · interrupt-driven I/O · DMA · buses · disk I/O performance · buffering.

---

## Learning outcomes

1. Classify I/O devices and interfaces.
2. Compare programmed, interrupt-driven, and DMA transfer on CPU involvement and speed.
3. Compute disk I/O throughput for a given workload.
4. Describe modern bus hierarchies (PCIe, USB).

---

## 6.1 I/O devices

| Class | Examples | Transfer unit | Access |
|---|---|---|---|
| **Block** | HDD, SSD, USB drive | Block (512 B – 4 KB) | Random |
| **Character** | Keyboard, serial port | Byte | Sequential |
| **Network** | Ethernet, Wi-Fi | Packet | Streaming |

---

## 6.2 I/O modules

An **I/O module** (or **controller**) sits between the bus and the device. It contains:

- **Data registers** — hold the transferred data.
- **Status register** — ready, busy, error.
- **Control register** — start, direction.

The CPU communicates with the module via **memory-mapped I/O** (device registers appear at memory addresses) or **isolated I/O** (separate address space).

---

## 6.3 Transfer techniques

### 6.3.1 Programmed I/O (polling)

The CPU continuously checks the status register:

```
while (!(status & READY)) ;
data = device.data;
```

**Pros:** simple, no interrupts.
**Cons:** wastes CPU cycles (100% CPU is spent polling).

### 6.3.2 Interrupt-driven I/O

The device raises an **interrupt** when ready. The CPU executes other instructions in the meantime.

**Pros:** efficient for slow devices (keyboard, mouse).
**Cons:** interrupt overhead per transfer — for high-speed devices, interrupts can overwhelm the CPU.

### 6.3.3 Direct Memory Access (DMA)

A **DMA controller** transfers data directly between memory and device **without CPU involvement**. The CPU sets up the transfer (source, destination, length) and continues execution; when done, the DMA controller raises an interrupt.

**Pros:** high throughput, minimal CPU load.
**Cons:** DMA controller competes with CPU for the memory bus.

!!! tip "Which to choose"
    - **Slow interactive devices** → interrupt-driven.
    - **Bulk transfers** (disk, network) → DMA.
    - **Simple, low-cost embedded systems** → programmed I/O.

---

## Worked example 6.1 — disk I/O time

**Given.** Disk with:
- Average seek time = 9 ms.
- Rotational speed = 7200 RPM (120 rev/s, period 8.33 ms).
- Transfer rate = 150 MB/s.
- Block size = 4 KB.

**Average rotational latency** = half a revolution = 4.17 ms.

**Transfer time for 4 KB** = $4096 / (150 \times 10^6) \approx 0.027\,\text{ms}$.

**Total service time per block** ≈ $9 + 4.17 + 0.027 \approx 13.2\,\text{ms}$.

**Random IOPS** ≈ $1 / 0.0132 \approx 76$ IOPS.

For **sequential** access, seek and rotation are amortised across many blocks — throughput approaches the interface limit.

---

## 6.4 Buses

A **bus** is a set of wires carrying data, addresses, and control signals.

| Bus | Purpose | Typical speed |
|---|---|---|
| **Front-side bus** | CPU ↔ memory | 800 MT/s – 1600 MT/s |
| **PCIe** | Peripheral expansion | PCIe 4.0 ×16: ~32 GB/s |
| **USB** | External devices | USB 3.2: 20 Gb/s |
| **SATA / NVMe** | Storage | SATA 6 Gb/s; NVMe ×4 ~7 GB/s |

Modern systems use a **point-to-point** interconnect (PCIe) rather than a shared bus.

---

## 6.5 I/O buffering

**Buffering** decouples producer and consumer speeds.

- **Single buffering** — one buffer; producer fills while consumer drains.
- **Double buffering** — two buffers alternate.
- **Circular buffering** — ring of buffers for streaming.

**Why buffering matters:**

- A CPU produces data far faster than a disk can absorb.
- A network delivers packets in small chunks, but the file system writes in blocks.
- Buffering provides a **consistent snapshot** even if the source changes.

---

## 6.6 Quiz

1. Compare programmed, interrupt-driven, and DMA I/O on CPU utilisation and per-transfer overhead.
2. Compute the average service time for a disk with 10 ms seek, 5400 RPM, and 100 MB/s transfer rate for an 8 KB block.
3. Why does a modern system use PCIe point-to-point rather than a shared bus?
4. Explain double buffering with an example.

---

## Answer key

??? success "Click to reveal"

    1. Programmed: high CPU, low complexity. Interrupt: low CPU for slow devices. DMA: minimal CPU, high throughput.
    2. Rotational period = 11.1 ms; half = 5.55 ms. Transfer = 8192/10^8 = 0.08 ms. Total ≈ 10 + 5.55 + 0.08 ≈ 15.6 ms.
    3. PCIe scales better — each device gets its own link, no contention.
    4. Two buffers alternate: while the producer fills one, the consumer drains the other.

---

## Further reading

- Stallings, §7.1–§7.8.
- Mano, §11.1–§11.4.