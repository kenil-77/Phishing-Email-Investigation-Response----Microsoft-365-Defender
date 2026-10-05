# Phishing Email Investigation & Response — Microsoft 365 Defender

> A hands-on SOC analyst exercise simulating a credential-harvesting phishing attack, investigating it using Microsoft 365 Defender, and executing a full incident response workflow.

---

## Table of Contents

- [Objective](#objective)
- [Environment Setup](#environment-setup)
- [Scenario](#scenario)
- [Investigation Workflow](#investigation-workflow)
  - [Phase 1 — Email Detection and Analysis](#phase-1--email-detection-and-analysis)
  - [Phase 2 — Response Actions](#phase-2--response-actions)
  - [Phase 3 — Automated Investigation and Response (AIR)](#phase-3--automated-investigation-and-response-air)
- [Incident Report (CAR Format)](#incident-report-car-format)
  - [Challenge](#challenge)
  - [Action](#action)
  - [Result](#result)
- [Who, What, When, Where, Why, How](#who-what-when-where-why-how)
- [Recommendations](#recommendations)
- [Tools and Technologies Used](#tools-and-technologies-used)
- [Key Artifacts and Evidence](#key-artifacts-and-evidence)
- [Skills Demonstrated](#skills-demonstrated)
- [Screenshots](#screenshots)
- [Lessons Learned](#lessons-learned)
- [References](#references)

---

## Objective

To simulate a real-world phishing attack within a controlled Microsoft 365 environment and demonstrate the end-to-end workflow a SOC Tier 1 analyst would follow — from initial detection and email analysis in Threat Explorer, through containment and remediation, to automated investigation using Defender's AIR capabilities.

---

## Environment Setup

| Component | Details |
|-----------|---------|
| **Tenant** | Microsoft 365 E5 Trial (30dayzoro.onmicrosoft.com) |
| **Attacker** | External Gmail account (ken***@gmail.com) |
| **Victim User** | Roronoa Zoro (zoroo@30dayzoro.onmicrosoft.com) |
| **Attack Vector** | Phishing email with malicious link |
| **Security Tools** | Microsoft Defender for Office 365, Threat Explorer, AIR |

### Pre-configured Security Policies

Before executing the simulation, the following security policies were configured in the tenant to establish a proactive defense posture:

- **Safe Links Policy** — URL detonation and time-of-click protection enabled for all users. Malicious URLs are scanned and blocked when the user clicks them, even if the email was delivered before the URL was identified as malicious.
- **Anti-Phishing Policy** — Impersonation protection, mailbox intelligence, and spoof intelligence configured to detect social-engineering techniques including display name spoofing and domain impersonation.
- **Attack Simulation Training** — Enabled in the tenant to support future phishing resilience testing and user awareness training.

---

## Scenario

An external attacker sends a phishing email with the subject line **"Banking details URGENT"** from a Gmail address to an employee (Roronoa Zoro) within the organization. The email leverages urgency-based social engineering — using a financial/banking theme to pressure the victim into clicking a malicious link embedded in the email body.

This simulates a common credential-harvesting phishing attack that SOC teams encounter daily in enterprise environments.

---

## Investigation Workflow

### Phase 1 — Email Detection and Analysis

**Tool:** Microsoft Defender → Email & collaboration → Explorer (Threat Explorer)

1. Navigated to Threat Explorer and filtered emails by date range (Oct 3–4, 2026).
2. Located the suspicious email "Banking details URGENT" sent from ken***@gmail.com to zoroo@30dayzoro.onmicrosoft.com.
3. Selected the email to open the delivery details panel and analyzed the following:

**Delivery Details:**

| Field | Value |
|-------|-------|
| Original Threats | Phish / High |
| Latest Threats | Phish / High |
| Original Location | Quarantine |
| Latest Delivery Location | Quarantine |
| Delivery Action | Blocked |
| Detection Technologies | Advanced filter |
| Primary Override Source | None |
| First Contact | Yes |

**Email Metadata:**

| Field | Value |
|-------|-------|
| Sender Display Name | Kenil Prajapati |
| Sender Address | ken***@gmail.com |
| Sender Mail From | ken***@gmail.com |
| Return Path | ken****@gmail.com |
| Sender IP | 2a00:1450:4864:30:e (Google Infrastructure) |
| Recipient | zoroo@30dayzoro.onmicrosoft.com |
| Time Received (UTC -04:00) | Oct 4, 2026 1:25 PM |
| Directionality | Inbound |
| Network Message ID | 5bef3d28-4185-4cde-9b39-08df223c7bde |
| Links | 1 |

**Key Indicators of Compromise (IOCs):**

- External sender with no prior communication history (First Contact: Yes)
- Urgency-based subject line targeting financial/banking theme
- Email classified as Phish / High confidence by Defender's Advanced filter
- Sender IP mapped to Google's mail infrastructure (Gmail)
- Single embedded link — potential credential harvesting URL

**Email Authentication Results (from header analysis):**

| Check | Result |
|-------|--------|
| SPF | Pass |
| DKIM | Pass (signature verified) |
| DMARC | Pass (p=none, sp=quarantine, pct=100) |
| ARC | Pass |

> **Note:** SPF/DKIM/DMARC all passed because the email was genuinely sent from Gmail — the attacker used a legitimate email service rather than spoofing a domain. This highlights why email authentication alone is insufficient to catch all phishing attacks. Defender's Advanced filter caught the threat through content and behavioral analysis, not authentication failures.

---

### Phase 2 — Response Actions

**Tool:** Microsoft Defender → Threat Explorer → Take action

After confirming the email as a phishing threat, the following containment and remediation actions were executed:

| # | Action | Purpose |
|---|--------|---------|
| 1 | **Soft delete email** | Removed the phishing email from the victim's mailbox (moved to soft deleted items) to eliminate any residual risk |
| 2 | **Submit to Microsoft as confirmed phishing** | Reported the email to Microsoft's global threat intelligence for analysis and to improve future detection |
| 3 | **Initiate Automated Investigation (Investigate email)** | Triggered Defender's AIR to automatically scan the tenant for similar phishing emails targeting other users |

**Remediation Details:**

- **Remediation Name:** Phishing Response - Banking Details URGENT
- **Description:** SOC response to credential phishing email targeting zoroo@30dayzoro.onmicrosoft.com. Actions: soft delete email, report as phishing to Microsoft, initiate automated investigation.
- **Target Entity:** Message ID 0da338b9-7463-4948-abe4-08df223cf2ce
- **Impacted Asset:** zoroo@30dayzoro.onmicrosoft.com

---

### Phase 3 — Automated Investigation and Response (AIR)

**Tool:** Microsoft Defender → Investigation & response → Investigations

After the manual response actions triggered the investigation, Defender's AIR automatically analyzed the threat:

| Field | Value |
|-------|-------|
| Investigation ID | bd2fa4 |
| Status | Remediated |
| Detection Source | Office365 |
| Investigation | Email investigation for message ID 0da338b9-7463-4948-abe4-08df223cf2ce |
| Users | zoroo@30dayzoro.onmicrosoft.com |
| Creation Time | Oct 4, 2026 2:55 PM |
| Last Changed Time | Oct 4, 2026 3:00 PM |
| Threat Count | 8 |
| Action Count | 6 |
| Duration | ~5 minutes |

> **Outcome:** AIR identified 8 threats related to the phishing email and automatically executed 6 remediation actions across the tenant. Status moved to **Remediated** — confirming the threat was fully contained through a combination of manual SOC response and automated investigation.

---

## Incident Report (CAR Format)

### Challenge

A suspicious inbound email was flagged in Microsoft Defender's Threat Explorer. The email originated from an external Gmail account with no prior communication history with the recipient — triggering the "First contact" indicator. The subject line used urgency-based social engineering ("Banking details URGENT") to pressure the victim into clicking a malicious link. As the SOC analyst on duty, I needed to determine the scope of the threat, whether the user was compromised, and contain the attack.

### Action

- Investigated the email in Threat Explorer — analyzed sender metadata, sender IP (Google infrastructure), delivery path, embedded URL, and email authentication headers (SPF, DKIM, DMARC all passed — Gmail was the legitimate sender, threat was content-based)
- Confirmed Defender classified the email as Phish / High confidence using its Advanced filter and blocked delivery to quarantine before it reached the victim's inbox
- Soft-deleted the phishing email from the victim's mailbox to eliminate any residual risk
- Submitted the email to Microsoft as a confirmed phishing threat for global threat intelligence enrichment
- Initiated Automated Investigation and Response (AIR) to scan the entire tenant for similar phishing emails targeting other users

### Result

- Threat fully contained — the email was blocked before the victim could interact with it
- AIR investigation completed with status **Remediated** — identified 8 threats and executed 6 remediation actions automatically
- Zero credentials compromised, zero user impact
- Proactive defenses (Safe Links policy and Anti-Phishing policy) were already configured and validated during this exercise
- End-to-end detection, investigation, and response completed within 30 minutes

---

## Who, What, When, Where, Why, How

| Question | Answer |
|----------|--------|
| **Who** | **Victim:** Roronoa Zoro (zoroo@30dayzoro.onmicrosoft.com). **Attacker:** External Gmail account ken***@gmail.com with display name "Kenil Prajapati" — no organizational affiliation. |
| **What** | A phishing email with subject "Banking details URGENT" containing 1 embedded link was sent to the victim's Exchange Online mailbox. Defender blocked delivery and quarantined the message before the user could interact with it. |
| **When** | Email received: Oct 4, 2026 1:25 PM (UTC -04:00). Duplicate attempt observed at 1:29 PM. Investigation and containment completed: Oct 4, 2026. AIR investigation (bd2fa4) completed with status "Remediated" at 3:00 PM. |
| **Where** | The attack targeted Roronoa Zoro's mailbox in the 30dayzoro.onmicrosoft.com tenant. The email originated from Google's mail infrastructure (sender IP 2a00:1450:4864:30:e). The email contained 1 embedded link targeting the victim. |
| **Why** | The email used urgent banking-related social engineering to trick the victim into clicking a malicious link, likely for credential harvesting or malware delivery. |
| **How** | The attacker sent a socially engineered email with an urgent banking theme from an external Gmail address. The email was the first contact from this sender to the victim (First contact: Yes), a common phishing pattern. Defender's Advanced filter detected the phishing indicators and blocked the email at delivery, routing it to quarantine with a Phish / High classification. |

---

## Recommendations

1. **Enforce MFA for all users** — Multi-factor authentication would block an attacker from using stolen credentials even after a successful harvest.
2. **Enforce Safe Links and Safe Attachments policies** — Ensure URL detonation is enabled so Defender can block or warn on malicious links at time of click. (Already configured in this environment.)
3. **Deploy Anti-phishing policies** — Configure impersonation protection, mailbox intelligence, and spoof settings to catch social-engineering attacks. (Already configured in this environment.)
4. **Conduct regular phishing simulations** — Use Attack Simulation Training monthly to measure and improve user resilience.
5. **Implement conditional access policies** — Restrict sign-ins from unfamiliar locations, devices, or risk levels using Microsoft Entra ID Conditional Access.
6. **Train users to report phish** — Deploy the Report Message add-in in Outlook so users can flag suspicious emails directly to the SOC.
7. **Block known malicious senders** — Add confirmed phishing senders to the Tenant Allow/Block List to prevent repeat attacks from the same address.
8. **Leverage Automated Investigation and Response (AIR)** — Trigger AIR on confirmed threats to automatically identify and remediate related emails across the tenant.

---

## Tools and Technologies Used

| Tool | Purpose |
|------|---------|
| Microsoft 365 Defender Portal | Central security management and investigation console |
| Threat Explorer | Email threat detection, analysis, and hunting |
| Email Entity Page | Deep-dive email header analysis and URL inspection |
| Take Action (Threat Explorer) | Email remediation — soft delete, report, investigate |
| Automated Investigation and Response (AIR) | Automated threat hunting and remediation across the tenant |
| Safe Links Policy | URL detonation and time-of-click protection |
| Anti-Phishing Policy | Impersonation, spoof, and mailbox intelligence protection |
| Attack Simulation Training | Phishing simulation and user awareness training |
| Microsoft Entra ID | Identity and access management |
| Exchange Online | Cloud email platform (victim mailbox) |

---

## Key Artifacts and Evidence

| Artifact | Value |
|----------|-------|
| Network Message ID | 5bef3d28-4185-4cde-9b39-08df223c7bde |
| Internet Message ID | \<CAGG6bpas=TdfTkUY79+wn_zvPZpRkUN5vNB53UFjyyy1p9z6w@mail.gmail.com\> |
| Target Entity (Email) | 0da338b9-7463-4948-abe4-08df223cf2ce |
| AIR Investigation ID | bd2fa4 |
| Sender IP | 2a00:1450:4864:30:e |
| Remediation Name | Phishing Response - Banking Details URGENT |

---

## Skills Demonstrated

- **Email Threat Analysis** — Analyzed email headers, sender metadata, delivery path, authentication results (SPF/DKIM/DMARC), and embedded URLs using Threat Explorer
- **Alert Triage and Investigation** — Identified phishing indicators including First Contact flag, urgency-based social engineering, and external sender with no organizational affiliation
- **Incident Response** — Executed containment actions (soft delete, Microsoft submission, AIR initiation) following a structured response workflow
- **Security Policy Configuration** — Pre-configured Safe Links and Anti-Phishing policies to establish proactive defenses before the attack occurred
- **Automated Investigation** — Leveraged Defender's AIR to automatically scan and remediate related threats across the tenant
- **Structured Reporting** — Documented findings using CAR format and the Who/What/When/Where/Why/How framework for clear stakeholder communication

---

## Screenshots

> Place your screenshots in a `/screenshots` folder and update the paths below.

| # | Description | File |
|---|-------------|------|
| 1 | Threat Explorer — Email detected with Delivery Details panel (Phish/High, Blocked, Quarantine, First Contact) 
<img width="952" height="470" alt="Screenshot 2026-10-04 140343" src="https://github.com/user-attachments/assets/5145090f-0b67-4e24-addb-21e8838641b0" />

| 2 | Take Action — Review and Submit page showing 3 remediation actions 
<img width="958" height="467" alt="Screenshot 2026-10-04 145211" src="https://github.com/user-attachments/assets/2254319e-ab99-4c55-af61-0112cd2c98a6" />

| 3 | Automated Investigation (AIR) — Remediated status with 8 threats and 6 actions 
<img width="1117" height="567" alt="Screenshot 2026-10-04 172604" src="https://github.com/user-attachments/assets/dff62063-f152-4d25-af6a-d54b48d54c8e" />


---

## Lessons Learned

1. **Email authentication alone is not enough.** SPF, DKIM, and DMARC all passed because the attacker used a legitimate Gmail account. Defender's Advanced filter caught the threat through content and behavioral analysis — reinforcing the need for layered detection beyond authentication.

2. **"First Contact" is a high-value indicator.** The First Contact flag immediately signaled that this sender had no prior communication history with the recipient — a common and reliable phishing indicator that should always be investigated.

3. **Proactive policy configuration matters.** Having Safe Links and Anti-Phishing policies configured before the attack meant Defender could block the email at delivery rather than relying on user judgment. Prevention is always preferable to detection after the fact.

4. **AIR amplifies manual response.** Triggering Automated Investigation after manual containment allowed Defender to find and remediate 8 related threats with 6 actions — far more than a single analyst could review manually in the same timeframe.

5. **Urgency-based social engineering remains effective.** The "Banking details URGENT" subject line is a textbook example of how attackers exploit urgency to bypass critical thinking. Regular phishing simulations and user awareness training are essential countermeasures.

---

## References

- [Microsoft Defender for Office 365 Documentation](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/)
- [Threat Explorer in Microsoft Defender](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/threat-explorer-about)
- [Automated Investigation and Response (AIR)](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/air-about)
- [Safe Links in Microsoft Defender for Office 365](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/safe-links-about)
- [Anti-Phishing Policies](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/anti-phishing-policies-about)
- [Attack Simulation Training](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/attack-simulation-training-get-started)

---

## Author

**Kenil Prajapati**
Cybersecurity Professional | CompTIA Security+ | ISC2 CC | AWS Cloud Security Foundations

---

> **Disclaimer:** This project was conducted in a controlled lab environment using a Microsoft 365 E5 trial tenant. No real users, organizations, or data were targeted or compromised. All email addresses and tenant names used are for demonstration purposes only.
