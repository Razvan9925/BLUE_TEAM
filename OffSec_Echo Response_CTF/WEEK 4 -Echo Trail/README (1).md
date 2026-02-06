# Echo Trail – Incident Response Challenge

**Status:** Completed  
**Category:** Incident Response • Cloud Security • Forensics  
**Difficulty:** Intermediate

---

## 📌 Overview

Echo Trail este un challenge de tip blue team care simulează compromiterea unei organizații NGO prin phishing și abuz de servicii cloud.  
Scopul a fost identificarea vectorului de atac, reconstruirea timeline-ului și evidențierea exfiltrării de date.

---

## 🎯 Objectives

- Identificarea emailului de phishing și a atașamentului malițios  
- Analiza paginii de phishing și a capturii de credențiale  
- Investigarea autentificărilor Azure și a MFA  
- Urmărirea accesului prin Azure Cloud Shell  
- Identificarea exfiltrării bazei de date  

Toate cele **10 obiective** ale challenge-ului au fost rezolvate.

---

## 🧪 Artifacts Analyzed

- Email-uri phishing (`.eml`)  
- Network capture (`.pcapng`)  
- Azure / Entra ID sign-in logs  
- Mail server logs  
- Cloud Shell session logs  
- Windows Event Logs (Sysmon, Security)  
- Database dump (`.sql`)  

---

## 🔍 Attack Summary

**Tip atac:** Phishing → furt credențiale → bypass MFA → acces cloud → exfiltrare date  

**Pași principali:**
1. Email de phishing cu link malițios  
2. Capturare credențiale prin pagină fake  
3. Autentificare Azure reușită după MFA attempts  
4. Acces prin Azure Cloud Shell  
5. Extragere bază de date cu `mysqldump.exe`  

---

## 🛠️ Tools & Techniques

- Email & header analysis  
- Network traffic analysis  
- Azure sign-in log analysis  
- Cloud Shell & Azure CLI  
- Windows event log forensics  
- Database forensics  

---

## 🧠 Key Learnings

- Phishing-ul rămâne vectorul principal de atac  
- MFA poate fi ocolit dacă nu este corect configurat  
- Monitorizarea cloud este esențială  
- Corelarea logurilor este critică în IR  

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

Challenge-ul oferă o imagine clară asupra unui incident real end-to-end și evidențiază importanța securității în medii cloud.

---

**Author:** MR. Umair  
**Completed:** October 2025
