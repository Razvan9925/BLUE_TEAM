# Week 1 - ProtoVault Breach Investigation 🔐

**Challenge Name:** ProtoVault Database Leak Investigation  
**Difficulty:** Beginner  
**Category:** Incident Response, Digital Forensics, OSINT  


---

## 📖  Overview
This challenge focused on investigating a database leak at **ProtoVault**, a secure data facility. An attacker claimed they had stolen the company's database and would release it unless a ransom was paid.

As the investigator, my goals were to:
- Review the application code for security issues
- Identity how the databse was leaked
- Find where the leaked database was publicly hosted
- Confirm that the stolen data was real

---

## Challenge Questions & Answers

### **1. Database Connection String**
**Task:** Check if the database connection details were secure.

**Answer:**
```text
postgresql://assetdba:8d631d2207ec1debaafd806822122250@pgsql_prod_db01.protoguard.local/pgamgt?sslmode=verify-full
```

**Location:** source_code/app/app.py (line 10)
**Issue:** Credidentials were hardcoded in the source code.

### **2. File Responsible for the Leak**
**Task:** Identify which file exposed the database.
**Answer:**
```text
backup_db.py
```
Discovered by reviewing Git commit history and restoring the deleted file.

### **3. Password Hash Verification**
**Task:** Provide Naomi Adler’s password hash from the leaked database.
**Answer:**
```text
pbkdf2:sha256:600000$YQqIvcDipYLzzXPB$598fe450e5ac019cdd41b4b10c5c21515573ee63a8f4881f7d721fd74ee43d59
```
### **4. Public Leak URL**
**Task:** Give the public link where the leaked database was hosted.
**Answer:**
```text
https://protoguard-asset-management.s3.us-east-2.amazonaws.com/db_backup.xyz
```
File was stored in a fully public Amazon S3 bucket using only weak ROT13 encoding.

## Investigation Summary

**Source Code Review**

- Found database credentials directly in code
- No secure secrets management
- No proper protection for backup scripts

**Git History Analysis**
- Located previously deleted backup_db.py file
- File uploaded DB backups to a public S3 bucket
- ROT13 encoding used instead of real encryption

**Leak Verification**

Used Python to download and decode the leaked file:
```text
#!/usr/bin/env python3
import requests
import codecs

# Download the leaked database from S3
url = "https://protoguard-asset-management.s3.us-east-2.amazonaws.com/db_backup.xyz"
print(f"[*] Downloading from: {url}")

try:
    response = requests.get(url)
    response.raise_for_status()
    
    # Save the encoded file
    with open("leaked_db_encoded.xyz", "wb") as f:
        f.write(response.content)
    print("[+] Downloaded successfully")
    
    # Decode from ROT13
    encoded_text = response.content.decode('utf-8', errors='ignore')
    decoded_text = codecs.decode(encoded_text, 'rot_13')
    
    # Save the decoded file
    with open("leaked_db_decoded.sql", "w", encoding='utf-8') as f:
        f.write(decoded_text)
    print("[+] Decoded using ROT13")
    
    # Search for Naomi Adler
    print("\n[*] Searching for Naomi Adler...")
    lines = decoded_text.split('\n')
    for i, line in enumerate(lines):
        if 'naomi' in line.lower() and 'adler' in line.lower():
            print(f"\n[!] Found at line {i}:")
            print(line)
            # Print surrounding lines for context
            for j in range(max(0, i-2), min(len(lines), i+3)):
                print(f"  {j}: {lines[j]}")
    
except Exception as e:
    print(f"[!] Error: {e}")
```
Confirmed real user records and password hashes.

**Key Security Issues Found**

| Severity    | Issue                                        |
| ----------- | -------------------------------------------- |
| 🔴 Critical | Hardcoded database credentials               |
| 🔴 Critical | Public S3 bucket with no authentication      |
| 🟡 High     | Weak ROT13 encoding                          |
| 🟡 High     | Sensitive files recoverable from Git history |
| 🟠 Medium   | No secrets management                        |
| 🟠 Medium   | Insufficient logging and monitoring          |

**Tools Used:**
- Git (history and commit analysis)
- Python scripting
- AWS S3 investigation
- ROT13 decoding
- Source code review
- OSINT


**Key Lessons Learned:**

- Never store passwords directly in code
- Cloud buckets should be private by default
- ROT13 is not security
- Deleted Git files can still be recovered
- Always use proper secrets management tools


**Challenge Status:**
- Status: ✔ Completed
- Questions Answered: 4/4
- Evidence Verified: Yes
- Report Generated: Yes
