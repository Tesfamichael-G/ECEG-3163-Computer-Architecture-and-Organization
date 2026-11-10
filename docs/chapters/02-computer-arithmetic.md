# Chapter 2 — Computer Arithmetic

!!! abstract "Chapter at a glance"
    **Contact hours:** 2 lecture + 3 tutorial/lab · **Home study:** ~5 h

    **Coverage:** signed number representation · integer addition and subtraction · overflow detection · multiplication (shift-add, Booth) · division (restoring, non-restoring) · IEEE-754 floating point · floating-point arithmetic · rounding.

---

## Learning outcomes

1. Convert between sign-magnitude, 1's complement, 2's complement, and biased representations.
2. Perform integer addition and subtraction and **detect overflow** at the bit level.
3. Trace **Booth's multiplication algorithm** on a small example.
4. Trace **restoring and non-restoring division** on a small example.
5. Encode and decode **IEEE-754 single and double precision** values.
6. Perform floating-point addition and identify rounding sources.

---

## 2.1 Number representation

### 2.1.1 Sign-magnitude

The **most-significant bit** is the sign ($0$ for positive, $1$ for negative); the rest is the magnitude.

**Problem:** two representations of zero (`0000` and `1000`), and **addition** requires sign handling.

### 2.1.2 1's complement

Negative numbers are the **bitwise complement** of their positive counterpart.

**Still two zeros** — `0000` (positive) and `1111` (negative) — and **end-around carry** in addition.

### 2.1.3 2's complement

!!! note "2's complement (dominant today)"
    To negate a number, **complement all bits and add 1**.

**Properties:**

- **One** zero.
- Addition and subtraction **use the same circuit**.
- Range for $n$ bits: $-2^{n-1}$ to $2^{n-1} - 1$.

### 2.1.4 Biased (offset) representation

Add a bias $B$ to the true value: stored = $x + B$. Used in **floating-point exponents** (IEEE 754 uses bias $127$ for single precision).

### Worked example 2.1 — −45 in 8 bits

| Representation | Bits |
|---|---|
| Sign-magnitude | `1_0101101` |
| 1's complement | `11010010` |
| 2's complement | `11010011` |
| Biased (B=127) | $127 - 45 = 82$ = `01010010` |

---

## 2.2 Integer addition and subtraction

### 2.2.1 2's-complement addition

Add the bits as **unsigned**, discard the carry out of the MSB, and interpret the result as 2's-complement.

### 2.2.2 Overflow detection

**Overflow** occurs when the true mathematical result is outside the representable range.

!!! tip "The sign rule for 2's-complement addition"
    Overflow occurs **if and only if**:
    - **Both operands are positive** and the **result is negative**, OR
    - **Both operands are negative** and the **result is positive**.

    Formally: $C_{\text{in}} \oplus C_{\text{out}}$ of the MSB.

### Worked example 2.2 — overflow detection

Add `0110` (+6) and `0101` (+5) in **4-bit** 2's complement:

```
  0110
+ 0101
------
  1011   (result = -5)
```

Both operands are positive, result is negative → **overflow**.

The correct mathematical answer is 11, which cannot be represented in 4-bit 2's complement (max = +7).

---

## 2.3 Multiplication

### 2.3.1 Shift-add (unsigned)

For two $n$-bit unsigned numbers:

```
product = 0
for i in 0..n-1:
    if multiplier[i] == 1:
        product += multiplicand << i
```

Takes $n$ cycles (or fewer with a multiplier array).

### 2.3.2 Booth's algorithm (signed)

Booth's algorithm handles **signed** multiplication uniformly. It examines the **current and previous** multiplier bits:

| $(Q_i, Q_{i-1})$ | Action |
|---|---|
| 0 0 | No arithmetic |
| 0 1 | Add multiplicand |
| 1 0 | Subtract multiplicand |
| 1 1 | No arithmetic |

### Worked example 2.3 — Booth's algorithm

Multiply $+3$ (multiplicand) by $-2$ (multiplier), both in 4-bit 2's complement:

- Multiplicand $M = 0011$ (+3), $-M = 1101$.
- Multiplier $Q = 1110$ (−2), $Q_{-1} = 0$.

| Step | $Q_0 Q_{-1}$ | Operation | $A$ | $Q$ |
|:---:|:---:|---|---|---|
| 0 | — | initialise | 0000 | 1110 · 0 |
| 1 | 1 0 | $A \leftarrow A - M = 1101$; arith shift right | 1110 | 1111 · 0 |
| 2 | 1 0 | $A \leftarrow A - M = 1011$; arith shift right | 1101 | 1111 · 1 |
| 3 | 1 1 | no op; arith shift right | 1110 | 1111 · 1 |
| 4 | 1 1 | no op; arith shift right | 1111 | 1111 · 1 |

Result: $AQ = 1111\,1111_{2}$ = $−1$ = $(+3) \times (−2)$. ✓

---

## 2.4 Division

### 2.4.1 Restoring division

1. Shift left $A$ (partial remainder).
2. Subtract divisor from $A$.
3. If $A < 0$: **restore** (add divisor back), set $Q_0 = 0$.
4. Else: set $Q_0 = 1$.
5. Repeat $n$ times.

### 2.4.2 Non-restoring division

Avoids the restore step by alternating between subtraction and addition:

- If $A \geq 0$: $A \leftarrow 2A - M$.
- If $A < 0$: $A \leftarrow 2A + M$.

At the end, a final restore is done only if $A$ is negative.

Non-restoring division is faster (no restore each iteration) but requires careful sign tracking.

---

## 2.5 Floating-point representation: IEEE 754

### 2.5.1 Format

IEEE 754 single precision is **32 bits**:

```
| S | EEEEEEEE | MMMMMMMMMMMMMMMMMMMMMMM |
 1    8               23
```

- **S** = sign bit.
- **E** = biased exponent (bias 127 for single).
- **M** = mantissa (fractional part).

The value is

$$
(-1)^S \times 1.M \times 2^{E - 127}
$$

where $1.M$ is the **hidden leading bit** convention.

Double precision: **64 bits** = 1 sign + 11 exponent (bias 1023) + 52 mantissa.

### 2.5.2 Special values

| Exponent | Mantissa | Meaning |
|---|---|---|
| 0 | 0 | $\pm 0$ |
| 0 | $\neq 0$ | Subnormal numbers |
| 1–254 | any | Normal numbers |
| 255 | 0 | $\pm \infty$ |
| 255 | $\neq 0$ | NaN |

### Worked example 2.4 — encode 6.75

- Sign: $0$ (positive).
- Binary: $6.75 = 110.11 = 1.1011 \times 2^2$.
- Exponent field: $2 + 127 = 129 = 10000001_2$.
- Mantissa: $1011$ followed by zeros = `10110000000000000000000`.

**Final:** `0 10000001 10110000000000000000000` = `0x40D80000`.

### 2.5.3 Floating-point arithmetic

**Addition** requires **alignment**: the exponent with the smaller value is shifted right until the exponents match, then the mantissas are added.

**Multiplication** multiplies the mantissas and **adds** the exponents.

**Rounding modes** (IEEE 754):

- round to nearest, ties to even (default);
- round toward zero;
- round toward $+\infty$;
- round toward $-\infty$.

### 2.5.4 Common pitfalls

- **Floating-point addition is not associative**: `(a+b)+c` may differ from `a+(b+c)`.
- **Comparisons with `==` are dangerous** — use a tolerance.
- **Subtracting nearly-equal numbers** can cause **catastrophic cancellation**.
- **Accumulated sums** can drift; Kahan summation mitigates this.

---

## 2.6 Quiz

1. Represent −45 in 8-bit sign-magnitude, 1's complement, and 2's complement.
2. Add `0110` and `0101` in 4-bit 2's complement. Did overflow occur? Why?
3. Explain how Booth's algorithm handles negative multipliers without a separate sign step.
4. Encode 6.75 in IEEE-754 single precision.
5. Add $1.5 \times 10^{38}$ and $-1.5 \times 10^{38}$ (both single precision, both normal). What is the result, and what happens if you then add $1.0$?

---

## Answer key

??? success "Click to reveal"

    1. Sign-magnitude `10101101`, 1's complement `11010010`, 2's complement `11010011`.
    2. Result `1011` = −5; overflow because both inputs are positive but result is negative.
    3. Booth's algorithm examines adjacent multiplier bits and performs add/subtract/skip accordingly — the sign of the multiplier is naturally handled.
    4. `0x40D80000` (see worked example).
    5. Result 0. Adding 1.0 to 0 gives 1.0; the original precision of the operands is lost — an example of catastrophic cancellation.

---

## Further reading

- Stallings, §10.1–§11.5.
- Mano, §2.1–§2.8, §10.1–§10.6.
- IEEE 754-2019 standard.