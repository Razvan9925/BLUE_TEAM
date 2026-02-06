# Echo Trail – Incident Response Challenge

**Status:** Completed  
**Category:** Incident Response • Cloud Security • Digital Forensics  
**Difficulty:** Intermediate

---

## 📌 Overview

Echo Trail is a blue team challenge that simulates the compromise of an NGO environment through phishing and cloud service abuse.  
The goal was to identify the attack vector, reconstruct the incident timeline, and confirm data exfiltration.

---

## 🎯 Objectives

- Identify the phishing email and malicious attachment  
- Analyze the phishing page and credential harvesting  
- Investigate Azure authentication and MFA activity  
- Track access via Azure Cloud Shell  
- Identify database exfiltration  

All **10 challenge objectives** were successfully completed.

---

## 🧪 Artifacts Analyzed

- Phishing emails (`.eml`)  
- Network capture (`.pcapng`)  
- Azure / Entra ID sign-in logs  
- Mail server logs  
- Cloud Shell session logs  
- Windows Event Logs (Sysmon, Security)  
- Database dump (`.sql`)  

---

## 🔍 Attack Summary

**Attack type:** Phishing → credential theft → MFA bypass → cloud access → data exfiltration  

**Main steps:**
1. Phishing email containing a malicious link  
2. Credential capture via fake login page  
3. Successful Azure authentication after multiple MFA attempts  
4. Access gained through Azure Cloud Shell  
5. Database extracted using `mysqldump.exe`  

---

## 🛠️ Tools & Techniques

- Email and header analysis  
- Network traffic analysis  
- Azure sign-in log analysis  
- Azure Cloud Shell & CLI review  
- Windows event log forensics  
- Database forensics  

---

## 🧠 Key Learnings

- Phishing remains a primary attack vector  
- MFA can be bypassed if not properly hardened  
- Cloud activity must be continuously monitored  
- Log correlation is critical in incident response  

---

## 📁 Repository Structure

```
.
├── README.md
├── INVESTIGATION_REPORT.md
├── analyze_logs.py
└── evidence/
```

---

## 🏁 Conclusion

This challenge demonstrates a full end-to-end security incident, from initial access to data exfiltration, highlighting the importance of cloud security monitoring and incident response.

---

**Author:** MR. Umair  
**Completed:** October 2025
