## 🔐 Understanding the 7 Transformations in Quantum Conundrum Decryption

  Before the final XOR mask is applied, the N×N matrix undergoes 7 distinct transformation passes.
  These transformations operate at three different levels:

- Spatial transformations (positions change, values stay the same)
- Byte-level transformations (values change)
- Bit-level transformations (internal structure of each byte changes)
- Together, they create a layered and highly obfuscated encryption pipeline.

## 📘 Summary of All 7 Transformations

| # | Transformation                | Level   | Function        | Purpose                                  |
| - | ----------------------------- | ------- | --------------- | ---------------------------------------- |
| 1 | Ring Rotation (90° CW)        | Spatial | Inline block    | Global positional scrambling             |
| 2 | Add Constant (mod 256)        | Byte    | `FUN_140002070` | Uniform value diffusion                  |
| 3 | Subtract Constant (mod 256)   | Byte    | `FUN_1400021f0` | Additional byte confusion                |
| 4 | Row/Column Cyclic Shifts      | Spatial | `FUN_140002390` | Macro-scale permutation                  |
| 5 | Quadrant/Transpose Swaps      | Spatial | Inline block    | Geometric diffusion                      |
| 6 | Even/Odd Bit Swap (0x55 mask) | Bit     | `FUN_140002540` | Bit-level confusion                      |
| 7 | Position-Based Bit Rotation   | Bit     | Inline block    | Variable, position-dependent obfuscation |

After these 7 steps, the final XOR keystream is applied.

## 🧩 Detailed Explanation of Each Transformation

1. Ring Rotation (90° Clockwise)
   
- Type: Spatial
- Location: Inline code inside FUN_140002680

Treats the matrix as concentric rings ("layers") and rotates each of them 90 degrees clockwise.

**Example (4×4 matrix):**
```
Before:              After:
 1  2  3  4          13  9  5  1
 5  6  7  8    →     14 10  6  2
 9 10 11 12          15 11  7  3
13 14 15 16          16 12  8  4
```
Purpose: Initial large-scale scrambling of positions.

2. Add Constant (mod 256)

- Type: Byte-level
- Function: FUN_140002070
- Parameter: Derived from year

**Every byte becomes:**
```
new = (old + k1) mod 256
```

Purpose: Uniform byte diffusion.

3. Subtract Constant (mod 256)

- Type: Byte-level
- Function: FUN_1400021f0
- Parameter: Derived from month
```
new = (old - k2 + 256) mod 256
```
Purpose: Additional value confusion, partially counteracting step #2 but increasing entropy.

4. Row & Column Cyclic Shifts

- Type: Spatial
- Function: First half of FUN_140002390
- Parameter: Derived from day

Rows rotate left/right

**Columns rotate up/down**

```
[A B C D] → [C D A B]
```

Purpose: Positional permutation without modifying values.

5. Quadrant / Block Swaps + Local Transpositions

- Type: Spatial
- Location: Inline block after FUN_140002390

Swaps large matrix regions (quadrants) or performs local transpositions.

**Example (quadrant swap):**

```
[ TL | TR ]   →   [ BR | BL ]
[ BL | BR ]       [ TR | TL ]
```

Purpose: Large-scale geometric diffusion.

6. Even/Odd Bit Swap (0x55 Mask)

- Type: Bit-level
- Function: FUN_140002540

**Each byte has its even-numbered bits swapped with odd-numbered bits:**
```
((x >> 1) & 0x55) | ((x & 0x55) << 1)
```
**Example:**

```
Original:  10110011 (179)
Swapped:   01101101 (109)
```
Purpose: Confuses binary structure inside each byte.

7. Variable Bit Rotation (Per-Byte)

- Type: Bit-level
- Location: Inline in FUN_140002680 (final loop)

**Rotation amount depends on:**
```
(year + month + day + row + col) & 7
```
Each byte is rotated right by 0–7 bits.

**Example:**
```
Original:       10110011
Rotate right 5: 01101011
```
Purpose: Position-based bit scrambling with high entropy.

## 🔑 Final Step: XOR With Keystream

After all 7 transformations, a keystream (derived from seed values such as email, date, timestamp, salt) is generated and XORed with the matrix.

**Example keystream generator (Python):**
```
def keystream(seed, need):
    out = bytearray()
    idx = 0
    while len(out) < need:
        for ch in seed:
            out.append((ord(ch) * 7 + idx) & 0xFF)
            idx += 1
            if len(out) >= need:
                break
    return bytes(out)
```

This XOR step reveals the final plaintext during decryption.

## 🎯 Decryption Reversal Order

**During decryption, your script reverses the steps in the exact opposite order:**

- XOR with keystream
- Undo bit rotation
- Undo bit-pair swap
- Undo row/column shifts
- Undo subtract constant
- Undo add constant
- Undo ring rotation
