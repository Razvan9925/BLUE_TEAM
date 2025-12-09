## **Quantum Conundrum – Encryption System Investigation Report**

This repository documents the full investigation and successful compromise of the Megacorp Quantum Encryption System, a system claimed to be “unbreakable” and “quantum-proof.”

The analysis focuses on breaking the protection surrounding the Obscuran Key, a high-value cyber-realm asset.

Final Assessment: ✔️ System Fully Compromised
(All findings sourced from the investigation report PDF.) 


## 🔍 Summary

Megacorp Quantum asserted their encryption architecture could not be broken.
However, through reverse engineering and cryptanalysis, the system was fully defeated.

## Key Findings

- Public key decoded

- Seed generation algorithm understood


All 7 transformation layers reversed

Encrypted file successfully decrypted

## Flag recovered: OS{BENDER}

Protection of the Obscuran Key is no longer secure 


## 🔑 Public Key Analysis

The so-called “public key” was merely Base64-encoded text:

24.07.2025|megacorp@quantum.com

## Issues

Not an actual cryptographic key

Provides zero security

Contributes to predictable seed generation 



🧬 Seed Generation Breakdown

The encryption seed was built from:

- Email

- Date

- Timestamp

- Hardcoded salt: PublicSalt

## Weaknesses

- Easy to reproduce

- No key-derivation function (PBKDF2/Argon2, etc.)

- Predictable and low entropy inputs 


## 🔄 Transformation Layers

The system applied seven reversible transformation passes to the encrypted matrix.

The first three:

- Ring Rotation

- Add Constant

- Subtract Constant

These operations add obfuscation but no real cryptographic strength. All transformations were fully reversed during analysis. 



## 🔓 Decryption & Flag Extraction

The encrypted file was successfully decrypted.

Recovered Flag

OS{BENDER}



## ⚠️ Security Vulnerabilities

The system suffers from multiple critical weaknesses:

- Fake public key
- Hardcoded salt value
- Weak, predictable keystream
- No integrity or authentication mechanisms
- Deterministic encryption output

These flaws collectively make the system trivial to reverse and compromise.


## 🛡️ Recommended Fixes

To build a secure system, replace all custom cryptographic components with industry-standard tools:
- Real public/private key infrastructure
- Proper key-derivation (Argon2, PBKDF2)
- Random salt + IV per encryption
- Authenticated encryption (AES-GCM or ChaCha20-Poly1305)
