# Echo Trail – Incident Response Investigation

## 🎯 Investigation Goals

Review the supplied evidence to determine the following:

- Identify the attachment used in the phishing email that initiated the breach  
- Determine the full phishing URL used to capture credentials  
- Identify the PHP file used to harvest user credentials  
- Recover the valid Azure password obtained via phishing  
- Identify the hostname used by the attacker during the SMTP EHLO phase  
- Determine the specific Azure error message shown when MFA failed  
- Pinpoint the exact timestamp of the attacker’s successful login  
- Identify the Azure CLI subcommand used to connect from Cloud Shell  
- Determine which database table was accessed and extracted  
- Identify the process image responsible for executing `mysqldump.exe`  

---

## 📦 Evidence Provided

The investigation package (`echo_trail.zip`, password: `EchoTrail123`) includes:

| Artifact | Type | Description |
|--------|------|-------------|
| network_capture.pcapng | Network Capture | Packet data from the incident window |
| Cache.zip | Browser Data | Raw Chrome cache files |
| InteractiveSignIns_2025-08-14_2025-08-15.xlsx | Azure Logs | Entra ID authentication logs |
| hmailserver_2025-08-15.log | Mail Logs | SMTP message tracking logs |
| Security Verification *.eml | Email Files | Phishing email samples |
| cloudshell_session.log | Session Logs | Azure Cloud Shell activity |
| db_dump.sql | Database Dump | Extracted database contents |
| sysmon.evtx | Event Logs | Process execution monitoring |
| ssh.evtx | Event Logs | SSH-related activity |
| event_logs.evtx | Event Logs | Windows security events |

---

## 🔑 Key Findings

### Attack Overview

- **Victim:** Elena Nygaard (elena.nygaard@ngohubcloud.onmicrosoft.com)  
- **Organization:** Empathreach / NGO-Hub  
- **Attack Flow:** Phishing → Credential Theft → MFA Bypass → Cloud Access → Data Exfiltration  

---

### Attack Timeline

**1. Initial Access (Phishing)**  
- Email contained `ngo_update.png` attachment  
- Malicious URL: `http://login.mcrosoft.com/login.html`  
- Credentials harvested via `login.php`  

**2. Authentication Bypass**  
- Multiple MFA failures between 08:05–08:07 UTC  
- Successful login at **08:15:49 UTC**  
- Azure Portal access obtained  

**3. Lateral Movement**  
- Azure Cloud Shell launched at **08:48:26 UTC**  
- Command used: `az ssh arc --resource-group ngo1 --name db`  
- Targeted database server: `DB.ngo-hub.com`  

**4. Data Exfiltration**  
- Tool used: `mysqldump.exe` (MariaDB 12.0)  
- Data source: `donorrecords` table  
- Output file: `db_dump.sql`  

---

## 🚨 Indicators of Compromise

**Malicious Domain**
- login.mcrosoft.com  

**Attacker IP**
- 203.0.113.10  

**Compromised Credentials**
- Username: elena.nygaard@ngohubcloud.onmicrosoft.com  
- Password: Jopa373424  

**SMTP Identifier**
- EHLO hostname: attacker01  

---

## 🛠️ Investigation Methods

- Email and header analysis  
- Network traffic analysis  
- Azure sign-in log review  
- SMTP log inspection  
- Azure Cloud Shell command analysis  
- SQL dump review  
- Windows event log correlation  

---

## 📊 MITRE ATT&CK Mapping

| Tactic | Technique | Evidence |
|------|----------|----------|
| Initial Access | T1566.001 – Phishing Attachment | ngo_update.png |
| Initial Access | T1566.002 – Phishing Link | Malicious URL |
| Credential Access | T1056.001 – Input Capture | login.php |
| Credential Access | T1621 – MFA Request Generation | MFA abuse |
| Defense Evasion | T1656 – Impersonation | Typosquatting |
| Lateral Movement | T1021.004 – SSH | az ssh arc |
| Collection | T1005 – Local Data | Database access |
| Collection | T1119 – Automated Collection | mysqldump.exe |
| Exfiltration | T1041 – Exfiltration Over C2 | SQL dump |

---

## 💡 Lessons Learned

- Phishing and typosquatting remain effective attack vectors  
- MFA can be abused if not phishing-resistant  
- Cloud access requires strict monitoring  
- Log correlation is critical in incident response  

---

## 🛡️ Recommended Improvements

### Immediate
- Reset compromised credentials  
- Revoke Azure sessions  
- Block malicious indicators  
- Enable database auditing  

### Short-Term
- Deploy phishing-resistant MFA (FIDO2)  
- Enforce Conditional Access policies  
- Improve email security controls  
- Conduct security awareness training  

### Long-Term
- Adopt Zero Trust architecture  
- Implement Privileged Access Management  
- Deploy Data Loss Prevention (DLP)  
- Establish continuous SOC monitoring  

---

## 📚 Skills Demonstrated

- Email forensics and phishing analysis  
- Network traffic analysis (PCAP)  
- Azure cloud security investigation  
- Multi-source log correlation  
- Timeline reconstruction  
- Database forensics  
- Incident response execution  
- MITRE ATT&CK mapping  
- Python scripting for analysis  
- Technical reporting  

---
