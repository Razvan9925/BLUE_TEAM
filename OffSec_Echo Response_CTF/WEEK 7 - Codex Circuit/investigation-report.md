# Investigation Report: Codex Circuit -- Slack Data Exfiltration

------------------------------------------------------------------------

## Executive Summary

This investigation uncovered a deliberate data exfiltration incident
inside MegaCorp's Slack environment that resulted in the unauthorized
exposure of sensitive customer intelligence. Deep packet inspection
across 234,337 captured network packets revealed a definitive sequence
of internal misuse: an employee, Ava, uploaded a high-value customer
dataset which was then siphoned out of the organization's trusted
collaboration boundary and transferred into a rogue Slack workspace by
James Brown.

**Key Findings:** - **Compromised Asset:** `sensitive_customer_list.xls`
containing 3 high-value client records totaling \$300,000 -
**Originating User:** Ava (U09KA40P3F0) uploaded and shared the file at
2025-10-10 11:51:36 GMT - **Malicious Insider:** James Brown
(U09KRBDV8S1) - **Exfiltration Vector:** Unauthorized Slack workspace
`secret-ops-workspace.slack.com` - **Exposed Data:** Customer names,
email addresses, phone numbers, account valuations

------------------------------------------------------------------------

## Investigation Methodology

### 1. Initial Triage

**Objective:** Establish scope and behavioral footprint within the PCAP
dataset.

Analysis of the packet capture showed: - 234,337 total packets across
\~22 minutes of recorded traffic - 1,184 HTTP transactions - 446 Slack
API interactions tied to file movement and messaging activity

------------------------------------------------------------------------

### 2. Protocol Analysis

Slack communication was conducted exclusively over HTTPS, with forensic
indicators showing: - File transfer activity through the `files.upload`
API endpoint - Distribution via `file_shared` WebSocket events - Message
history accessible through `conversations.history`

------------------------------------------------------------------------

### 3. File Upload Timeline Reconstruction

Correlation of HTTP POST payloads containing embedded JSON metadata
exposed the following timeline:

  GMT Time   File                             Action
  ---------- -------------------------------- -------------------------------------
  11:44:57   ---                              Channel `company_documents` created
  11:46:58   architecture_diagram.png         Uploaded
  11:47:16   onboarding_checklist.docx        Uploaded
  11:47:25   meeting-minutes_2025-10-09.pdf   Uploaded
  11:51:32   sensitive_customer_list.xls      Uploaded
  11:51:36   sensitive_customer_list.xls      Shared internally
  11:57:48   sensitive_customer_list.xls      Exported to rogue workspace

A narrow six-minute window separated legitimate internal sharing from
confirmed external exfiltration, indicating rapid opportunistic abuse of
access permissions.

------------------------------------------------------------------------

## User Attribution

Contextual analysis of Slack message artifacts linked: - **U09KA40P3F0 →
Ava** (file originator) - **U09KRBDV8S1 → James Brown** (workspace
creator and data exporter)

Subsequent packet inspection confirmed that James Brown uploaded the
same customer dataset into an unauthorized Slack tenant under his
control.

------------------------------------------------------------------------

## Workspace Separation

**Authorized Environment** - `team-megacorp.slack.com` - Channel:
`company_documents`

**Unauthorized Environment** - `secret-ops-workspace.slack.com` -
Channel: `secret-ops-collaboration` - Created by: James Brown

Channel creation logs verify that the rogue collaboration space was
instantiated minutes before the stolen dataset appeared within it.

------------------------------------------------------------------------

## Data Sensitivity Assessment

Recovered spreadsheet contents included: - Direct customer identifiers -
Contact information (email, phone) - Financial account values totaling
\$300,000

This represents: - Exposure of personally identifiable information
(PII) - Financially material business intelligence - Potential GDPR and
privacy compliance violations

------------------------------------------------------------------------

## Attack Chain Summary

1.  Ava uploads `sensitive_customer_list.xls`
2.  Ava shares file to `#company_documents`
3.  James Brown accesses and downloads file
4.  James Brown uploads file to rogue workspace
5.  External availability achieved

**Total Attack Time:** 6 minutes 12 seconds

------------------------------------------------------------------------

## Root Cause Analysis

**Primary Driver:** Insider threat leveraging legitimate dual-workspace
access.

**Contributing Failures:** - Absence of Data Loss Prevention (DLP) -
Lack of real-time file transfer monitoring - Over-permissive workspace
membership policies - No document classification or sensitivity
tagging - Unmonitored outbound collaboration traffic

------------------------------------------------------------------------

## Recommendations

### Immediate

-   Disable account: James Brown (U09KRBDV8S1)
-   Audit historical file-sharing activity
-   Notify impacted customers
-   Preserve PCAP and Slack logs for evidentiary chain

### Mid-Term (1--3 Months)

-   Deploy Slack-integrated DLP controls
-   Restrict multi-workspace participation
-   Implement document classification framework
-   Enable exfiltration alerting

### Long-Term (3--12 Months)

-   Enforce Zero Trust access architecture
-   Deploy UEBA for anomalous behavior detection
-   Expand insider threat awareness training
-   Formalize collaboration-tool incident response playbooks

------------------------------------------------------------------------

## Conclusion

PCAP-driven forensic reconstruction confirmed that James Brown extracted
MegaCorp's internal customer dataset and redeployed it into an
unauthorized Slack workspace within minutes of internal publication. The
breach exploited trust relationships inside a collaboration platform
lacking adequate governance controls.

**Incident Status:** CLOSED -- Attribution confirmed, remediation
actions defined
