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
  js: assets/lecture-02.js

---

# ECEG-3163

## Chapter 2 — Computer Arithmetic

---

## 2.1 — Signed Number Representation

???+ note "Four representations"
    | Scheme | Bit pattern for $-x$ | Zero? | Addition circuit |
    |---|---|---|---|
    | **Sign-magnitude** | flip MSB | two zeros (`0000`, `1000`) | requires sign handling |
    | **1's complement** | invert all bits | two zeros (`0000`, `1111`) | end-around carry |
    | **2's complement** | invert + add 1 | **one zero** | same adder as unsigned |
    | **Biased** | add bias $B$ | one | used for exponents |

<div class="fragment">

!!! tip "Why 2's complement won"
    - **One zero**.
    - **Same adder** as unsigned — no separate sign logic.
    - **Range** for $n$ bits: $[-2^{n-1}, 2^{n-1} - 1]$.

</div>

---

## Worked example 2.1 — −45 in 8 bits

!!! tip "Problem:"
    Represent $-45$ in 8 bits, in all four representations.

<div class="fragment">

???+ note "**Step 1 — positive magnitude.**"
    $+45 = \texttt{0010 1101}$.

</div>

<div class="fragment">

??? info "**Step 2 — sign-magnitude.**"
    Set the sign bit: `1_010 1101` = $\texttt{1010 1101}$.

</div>

<div class="fragment">

??? info "**Step 3 — 1's complement.**"
    Invert all bits: $\texttt{1101 0010}$.

</div>

<div class="fragment">

??? info "**Step 4 — 2's complement.**"
    Add 1: $\texttt{1101 0011}$.

</div>

<div class="fragment">

??? success "**Step 5 — biased (B=127).**"
    Stored = $127 - 45 = 82$ = $\texttt{0101 0010}$.

</div>

---

## 2.2 — Integer Addition and Subtraction

???+ note "2's-complement addition"
    Add the bit patterns as **unsigned** integers. Discard the carry out of the MSB. Interpret the result as 2's-complement.

<div class="fragment">

??? abstract "Overflow detection rule"
    Overflow occurs **iff**:
    
    - Both operands are **positive** and the result is **negative**, OR
    - Both operands are **negative** and the result is **positive**.
    
    Formally: $C_{\text{in}} \oplus C_{\text{out}}$ of the MSB.

</div>

<div class="fragment">

!!! warning "Why the rule works"
    Overflow means the true mathematical result is outside $[-2^{n-1}, 2^{n-1}-1]$. Two positives adding above $2^{n-1}-1$ wrap to a negative pattern — the bitwise signature of overflow.

</div>

---

## Worked example 2.2 — Overflow detection

!!! tip "Problem:"
    Add `0110` (+6) and `0101` (+5) in **4-bit** 2's complement.

<div class="fragment">

???+ note "**Step 1 — add as unsigned.**"
    ```
      0110
    + 0101
    ------
      1011
    ```

</div>

<div class="fragment">

??? info "**Step 2 — interpret result.**"
    `1011` has MSB = 1 → negative. 2's complement of `1011` is `0101` = 5. So `1011` = **−5**.

</div>

<div class="fragment">

??? warning "**Step 3 — check overflow.**"
    Both inputs positive; result negative → **overflow**.
    
    The true answer is +11, which exceeds the 4-bit 2's-complement range $[-8, +7]$.

</div>

<div class="fragment">

??? success "**Conclusion.**"
    Result = `1011`, overflow = **yes**. In hardware, this triggers the overflow flag (OF).

</div>

---

## 2.3 — Shift-Add Multiplication

???+ note "Unsigned shift-add"
    For $n$-bit operands:
    
    ```
    product = 0
    for i in 0..n-1:
        if multiplier[i] == 1:
            product += multiplicand << i
    ```
    
    Takes $n$ cycles; the multiplier is examined bit by bit.

<div class="fragment">

??? warning "Limitation"
    This is for **unsigned** operands. For signed operands you would need to (a) take magnitudes, (b) multiply, (c) apply the sign. Booth's algorithm avoids this.

</div>

---

## 2.4 — Booth's Algorithm

??? abstract "Booth's algorithm (signed)"
    Examine the **current and previous** multiplier bit:

    | $(Q_i, Q_{i-1})$ | Action |
    |---|---|
    | 0 0 | no arithmetic |
    | 0 1 | **add** multiplicand |
    | 1 0 | **subtract** multiplicand |
    | 1 1 | no arithmetic |

<div class="fragment">

!!! tip "Why it works"
    Booth's algorithm rewrites a signed multiplication as a sequence of additions and subtractions. The pair $(Q_i, Q_{i-1})$ detects **transitions** — a string of 1s in the multiplier becomes one subtraction and one addition, saving operations on average.

</div>

<div class="fragment">

???+ note "Why it handles signs uniformly"
    Negative numbers are just 2's-complement bit patterns. The arithmetic shift right **preserves the sign bit**, so negative results propagate correctly.

</div>

---

## Worked example 2.3 — Booth's algorithm

!!! tip "Problem:"
    Multiply $+3$ by $-2$ in 4-bit 2's complement using Booth's algorithm.

<div class="fragment">

???+ note "**Step 1 — set up.**"
    $M = 0011$ (+3), $-M = 1101$, $Q = 1110$ (−2), $Q_{-1} = 0$, $A = 0000$.

</div>

<div class="fragment">

??? info "**Step 2 — iterate four times.**"

    | Step | $Q_0 Q_{-1}$ | Op | $A$ | $Q$ |
    |:---:|:---:|---|---|---|
    | 0 | — | init | 0000 | 1110 · 0 |
    | 1 | 1 0 | $A \leftarrow A-M$; ASR | 1110 | 1111 · 0 |
    | 2 | 1 0 | $A \leftarrow A-M$; ASR | 1101 | 1111 · 1 |
    | 3 | 1 1 | no-op; ASR | 1110 | 1111 · 1 |
    | 4 | 1 1 | no-op; ASR | 1111 | 1111 · 1 |

</div>

<div class="fragment">

??? success "**Step 3 — result.**"
    $AQ = \texttt{1111\,1111}$ = **−1** = $(+3)\times(-2)$. ✓

</div>

---

## 2.5 — Division

???+ note "Restoring division"
    1. Shift left $A$ (partial remainder).
    2. Subtract divisor from $A$.
    3. If $A < 0$: **restore** (add divisor back), $Q_0 = 0$.
    4. Else: $Q_0 = 1$.
    5. Repeat $n$ times.

<div class="fragment">

??? note "Non-restoring division"
    Avoids restore: alternate subtraction and addition based on the sign of $A$.
    
    - If $A \geq 0$: $A \leftarrow 2A - M$.
    - If $A < 0$: $A \leftarrow 2A + M$.
    
    At the end, a single restore if $A$ is negative. **Faster** (no restore per iteration) but requires careful sign tracking.

</div>

---

## 2.6 — IEEE-754 Floating Point

???+ note "Single precision — 32 bits"
    ```
    | S | EEEEEEEE | MMMMMMMMMMMMMMMMMMMMMMM |
      1      8                  23
    ```
    
    Value = $(-1)^S \times 1.M \times 2^{E - 127}$ (bias 127, hidden leading bit).

<div class="fragment">

??? info "Double precision — 64 bits"
    1 sign + 11 exponent (bias 1023) + 52 mantissa. Range ~$10^{308}$, precision ~15–17 decimal digits.

</div>

<div class="fragment">

??? warning "Special values"
    | Exponent | Mantissa | Meaning |
    |---|---|---|
    | 0 | 0 | $\pm 0$ |
    | 0 | $\neq 0$ | Subnormal |
    | 1–254 | any | Normal |
    | 255 | 0 | $\pm\infty$ |
    | 255 | $\neq 0$ | NaN |

</div>

---

## Worked example 2.4 — Encode 6.75

!!! tip "Problem:"
    Encode $6.75$ in IEEE-754 single precision.

<div class="fragment">

???+ note "**Step 1 — binary.**"
    $6.75 = 110.11 = 1.1011 \times 2^{2}$.

</div>

<div class="fragment">

??? info "**Step 2 — sign and exponent.**"
    Sign = 0. Exponent field = $2 + 127 = 129 = \texttt{1000\,0001}$.

</div>

<div class="fragment">

??? info "**Step 3 — mantissa.**"
    Drop hidden 1, pad to 23 bits: $\texttt{1011\,0000\,0000\,0000\,0000\,000}$.

</div>

<div class="fragment">

??? success "**Step 4 — assemble and convert to hex.**"
    $\texttt{0\,1000\,0001\,1011\,0000\,\ldots} = \mathbf{\texttt{0x40D80000}}$.

</div>

<div class="fragment">

??? tip "**Sanity check.**"
    Decode: exponent = 129 − 127 = 2, mantissa = $1.6875$, value = $1.6875 \times 4 = 6.75$. ✓

</div>

---

## 2.7 — Floating-Point Arithmetic

???+ note "Addition algorithm"
    1. **Align** the exponents: shift the smaller operand right until exponents match.
    2. **Add** mantissas.
    3. **Normalise** the result.
    4. **Round** to fit the mantissa.
    5. **Check** for overflow / underflow.

<div class="fragment">

???+ note "Multiplication algorithm"
    1. **Multiply** mantissas.
    2. **Add** exponents.
    3. **Normalise** and **round**.
    4. Sign = XOR of signs.

</div>

<div class="fragment">

!!! warning "Common pitfalls"
    - **Floating-point addition is not associative**: `(a+b)+c` may differ from `a+(b+c)`.
    - **Comparisons with `==` are dangerous** — use a tolerance.
    - **Catastrophic cancellation**: subtracting nearly equal numbers loses precision.
    - **Accumulated sums drift** — Kahan summation mitigates.

</div>

---

## 2.8 — Rounding Modes

???+ note "IEEE-754 rounding modes"
    | Mode | Behaviour |
    |---|---|
    | Round to nearest, ties to even | Default — least biased |
    | Round toward zero | Truncate |
    | Round toward $+\infty$ | Ceiling |
    | Round toward $-\infty$ | Floor |

<div class="fragment">

!!! tip "Why ties-to-even?"
    Simple "round half up" biases results upward when repeated. Ties-to-even breaks the tie by choosing the neighbour with an even LSB — long-run bias approaches zero.

</div>

---

## Chapter 2 — key takeaways

<div class="fragment">

**1.** **2's complement** is the universal integer representation — one zero, same adder as unsigned, clean overflow detection.

</div>

<div class="fragment">

**2.** **Overflow** in 2's complement = both operands same sign, result opposite sign. Can be detected as $C_{\text{in}} \oplus C_{\text{out}}$ of the MSB.

</div>

<div class="fragment">

**3.** **Booth's algorithm** handles signed multiplication uniformly — add, subtract, or skip based on the $(Q_i, Q_{i-1})$ pair.

</div>

<div class="fragment">

**4.** **IEEE-754** uses sign + biased exponent + hidden-bit mantissa. Single precision: 1 + 8 + 23 bits, bias 127.

</div>

<div class="fragment">

**5.** **Floating-point arithmetic is not associative** — numerical code must be written with this in mind.

</div>

---

## Next — Chapter 3

**Instruction Sets and CPU Structure.**

<div class="fragment">

- Instruction set taxonomy: stack, accumulator, general-purpose, load-store.
- Instruction formats: **R, I, J** (MIPS).
- Eight addressing modes.
- Register transfer notation (RTN).
- Datapath design.
- **RISC vs CISC**.

</div>

<div class="fragment">

Every arithmetic and logic operation from Chapter 2 is exposed to software through **instructions** — Chapter 3 decides what that interface looks like.

</div>