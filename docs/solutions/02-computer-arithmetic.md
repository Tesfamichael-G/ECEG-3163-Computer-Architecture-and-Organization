# Solutions — Chapter 2: Computer Arithmetic

[:octicons-arrow-left-24: Back to Chapter 2](../chapters/02-computer-arithmetic.md)

Answers to **Section B** of the [Exam Bank](../exam-bank.md).

---

## B1 — Value of `11110110` in 2's complement [2]

**Answer: (a)** −10.

**Working.**

1. MSB = 1 → negative. Take 2's complement of the remaining bit pattern.
2. Invert all bits: `00001001`.
3. Add 1: `00001010` = 10.
4. So the value is **−10**.

**Sanity check.** 2's complement of `11110110` should sum to zero with `00001010`:
`11110110 + 00001010 = 100000000` → carry out, discarded. ✓

---

## B2 — IEEE-754 single-precision bias [2]

**Answer: (b)** 127.

**Why the others are wrong.**

- (a) 64 is the bias for the old IBM mainframe short-float format.
- (c) 128 is a common confusion (it's the number of positive exponents — 1 through 254, i.e. 254 values).
- (d) 255 is the maximum exponent field (reserved for infinities and NaN).

**Range:** exponent field $1$ to $254$ → unbiased exponent $-126$ to $+127$.

---

## B3 — Signed multiplication without sign handling [2]

**Answer: (b)** Booth's algorithm.

**Why.**

- (a) Shift-add requires separate sign handling — it multiplies unsigned magnitudes and applies the sign afterward.
- (b) **Booth's algorithm** examines adjacent multiplier bits and performs add/subtract/skip operations, so signed and unsigned cases are handled uniformly.
- (c) and (d) are **division** algorithms, not multiplication.

---

## B4 — −45 in 8-bit [3]

Positive 45 = `00101101`.

**(a) Sign-magnitude.** Set the sign bit to 1, keep magnitude unchanged:

```
1 0101101
```

**(b) 1's complement.** Invert all bits of the sign-magnitude pattern (or equivalently, invert the 8-bit positive form directly):

```
11010010
```

**(c) 2's complement.** Add 1 to the 1's complement:

```
11010011
```

**Sanity check.** Add `11010011` (−45) and `00101101` (+45): `11010011 + 00101101 = 100000000` → carry discarded = 0. ✓

---

## B5 — Add `0110` and `0101` in 4-bit 2's complement [3]

**Step 1 — add as unsigned.**

```
  0110
+ 0101
------
  1011
```

**Step 2 — interpret result.**

`1011` has MSB = 1, so it's negative. Take 2's complement: invert = `0100`, +1 = `0101` = 5. So `1011` = −5.

**Step 3 — overflow check.**

- Operand 1 (`0110`): MSB = 0 → positive.
- Operand 2 (`0101`): MSB = 0 → positive.
- Result (`1011`): MSB = 1 → negative.

**Rule:** if both operands are positive and the result is negative, overflow occurred.

**Conclusion.** Result = `1011` (= −5), and **overflow occurred**. The true answer is +11, which exceeds the 4-bit 2's-complement range [−8, +7].

**Why it happened.** The magnitude 11 is not representable in 4-bit 2's complement — the largest positive value is +7 (`0111`).

---

## B6 — Encode 6.75 in IEEE-754 single precision [3]

**Step 1 — binary of 6.75.**

- 6 = `110`
- 0.75 = `0.11` (since 0.5 + 0.25 = 0.75)
- Combined: `110.11`

**Step 2 — normalise.** Shift the point left by 2:

```
110.11 = 1.1011 × 2²
```

- Mantissa (hidden leading 1): `1.1011`
- Exponent: +2

**Step 3 — sign bit.** Positive → `0`.

**Step 4 — exponent field.** Add bias 127: $2 + 127 = 129 = 1000\,0001_2$.

**Step 5 — mantissa field.** Drop the hidden 1, pad to 23 bits:

```
1011 0000 0000 0000 0000 000
```

**Step 6 — assemble 32 bits.**

```
S | Exponent  | Mantissa
0 | 10000001  | 10110000000000000000000
```

**Step 7 — hex.**

Group into nibbles: `0100 0000 1101 1000 0000 0000 0000 0000`

$$
\boxed{\texttt{0x40D80000}}
$$

**Sanity check.** Decode back: exponent = 129 − 127 = 2; mantissa = 1 + 0.1011b = 1.6875; value = 1.6875 × 4 = 6.75. ✓