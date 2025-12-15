# Quantum Conundrum – Encryption System Investigation Report

This repository documents the full investigation and successful compromise of the Megacorp Quantum Encryption System, a system claimed to be “unbreakable” and “quantum-proof.”

The analysis focuses on breaking the protection surrounding the Obscuran Key, a high-value cyber-realm asset.

**Final Assessment:**  System Fully Compromised
(All findings sourced from the investigation report PDF.) 

---

## 🔍 Summary

Megacorp Quantum asserted their encryption architecture could not be broken.
However, through reverse engineering and cryptanalysis, the system was fully defeated.

**Key Findings:**

- Public key decoded
- Seed generation algorithm understood
- All 7 transformation layers reversed
- Encrypted file successfully decrypted

**Flag recovered: OS{BENDER}**

Protection of the Obscuran Key is no longer secure 

---

## 🔑 Public Key Analysis

**Task:** Analyze the file publickey.pubkey and review its contents. Decode the value and enter it as an answer to this exercise. Make sure to also include in your answer what encoding algorithm was used and what the decoded values represent.

The so-called “public key” was merely Base64-encoded text:

**Answer:**
`24.07.2025|megacorp@quantum.com`

## Issues

- Not an actual cryptographic key
- Provides zero security
- Contributes to predictable seed generation 

---

## 🧬 Seed Generation Breakdown

**Task:** Investigate how the Seed is calculated with the information from the publickey.pubkey. What other data is used to calculate the seed?

The encryption seed was built from:
- Email
- Date
- Timestamp
- Hardcoded salt: PublicSalt

**Answer:**
`Email + Date + Timestamp + PublicSalt`

## Weaknesses

- Easy to reproduce
- No key-derivation function (PBKDF2/Argon2, etc.)
- Predictable and low entropy inputs 

---

## 🔄 Transformation Layers

**Task:** Reverse engineer the decryption program and identify how many distinct transform passes are applied before the final XOR mask? Submit the number of distinct transformations and briefly explain what the first three transformations of the encryption process are doing. Please ensure that your explanations for the first three transformations are presented in the correct order in your answer.

The system applied seven reversible transformation passes to the encrypted matrix.

The first three:
- Ring Rotation
- Add Constant
- Subtract Constant

These operations add obfuscation but no real cryptographic strength. All transformations were fully reversed during analysis. 

**Answer:**

`Number of passes before the final XOR: 7
Ring rotation: Rotates each concentric layer of the N×N grid 90° clockwise. This physically rearranges the spatial layout by treating the matrix like onion rings, where each ring (outer, inner, etc.) rotates independently. Add constant pass: Adds a byte constant (derived from year: 2025 &amp; 0xFF = 225) to every cell modulo 256. This operation uniformly shifts all byte values across the entire matrix, providing initial value diffusion. Subtract constant pass: Subtracts a byte constant (derived from month: 7) from every cell modulo 256. This reverses some of the addition effect while maintaining byte-level confusion, creating a non-linear relationship between original and transformed values.`

---

## 🔓 Decryption & Flag Extraction

**Task:** Find a way to decrypt the decrypt_me.enc file and submit the flag found in the plaintext output.

The encrypted file was successfully decrypted.

**Recovered Flag:** `OS{BENDER}`

---

## ⚠️ Security Vulnerabilities

The system suffers from multiple critical weaknesses:
- Fake public key
- Hardcoded salt value
- Weak, predictable keystream
- No integrity or authentication mechanisms
- Deterministic encryption output

These flaws collectively make the system trivial to reverse and compromise.

---


## 🛡️ Recommended Fixes

To build a secure system, replace all custom cryptographic components with industry-standard tools:
- Real public/private key infrastructure
- Proper key-derivation (Argon2, PBKDF2)
- Random salt + IV per encryption
- Authenticated encryption (AES-GCM or ChaCha20-Poly1305)
