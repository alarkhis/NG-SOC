# Open-Source Next-Generation SOC (NG-SOC) Implementation

[![Security](https://img.shields.io/badge/Domain-Cybersecurity-red.svg)](https://github.com/)
[![SOC](https://img.shields.io/badge/Architecture-NG--SOC-blue.svg)](https://github.com/)
[![OpenSource](https://img.shields.io/badge/Stack-Open--Source-green.svg)](https://github.com/)

An end-to-end implementation of a Next-Generation Security Operations Center (NG-SOC) utilizing enterprise-grade open-source solutions. This project demonstrates centralized log management, automated incident detection, threat intelligence integration, and Security Orchestration, Automation, and Response (SOAR) playbooks to minimize Mean Time to Detect (MTTD) and Mean Time to Respond (MTTR).

---

## 🏛️ System Architecture

The architecture separates infrastructure telemetry from the ingestion, management, and automation planes to achieve modularity and scalability.

### Network & Security Architecture
* **Perimeter Defense & Monitoring:** pfSense Firewall integrated with Snort (IDS/IPS) deployed in a customized Network Topography (WAN, LAN, and isolated DMZ zones).
* **Detection & Ingestion Plane:** Wazuh SIEM/XDR acting as the centralized manager collecting telemetry from Windows and Linux endpoints.
* **Incident Management Plane (SIRP):** TheHive integrated with Cortex (Enrichment Engine) and MISP (Threat Intelligence Platform).
* **Automation Plane (SOAR):** Shuffle SOAR orchestrating actions across the entire pipeline.

![SOC Architecture](https://github.com/alarkhis/NG-SOC/assets/58915338/4716f2e6-119e-4760-8c8b-2ef5665a0532)

---

## 🛠️ Open-Source Ecosystem Stack

| Tool | Category | Role in this Architecture |
| :--- | :--- | :--- |
| **Wazuh** | SIEM / XDR | Log collection, aggregation, indexing, and anomaly/intrusion detection. |
| **pfSense** | Firewall / Router | Perimeter security, access control lists, and segmenting the network zones. |
| **Snort** | IDS / IPS | Deep packet inspection, real-time network traffic analysis, and signature-matching. |
| **TheHive** | SIRP | Distributed, collaborative Security Incident Response Platform. |
| **Cortex** | Security Analytics | Observable analysis and multi-engine enrichment (IPs, Hashes, Domains). |
| **Shuffle** | SOAR | Workflow automation, event orchestration, and response logic modeling. |
| **MISP** | Threat Intel (TIP) | Storing, sharing, and operationalizing Indicators of Compromise (IoCs). |

---

## 🛡️ Validation Scenarios & Incident Playbooks

### Scenario 1: Brute-Force SSH Intrusion Detection & Simulated SOAR Workflow

To validate the SIEM and perimeter logging fabric, a multi-stage SSH brute-force simulation was executed against the infrastructure.

1. **Intrusion Attempt:** Connection attempts to the `pfSense` firewall interface initiated from an external asset.
2. **Detection & Correlation:** 
   * **Wazuh** captured the authentication failure events (`sshd` rulesets triggered).
   * **Snort** concurrently generated alerts flagging outbound scanner activities matching malicious SSH threat profiles.
   * [Attacker Node] ---> (pfSense + Snort) ---> [Wazuh SIEM] ---> Alerts Triggered

 ![Wazuh Security Events](https://github.com/alarkhis/NG-SOC/assets/58915338/0f242ab4-824b-4d15-bc54-a8bf8da319eb)
 ![Snort Logs Interface](https://github.com/alarkhis/NG-SOC/assets/58915338/6f5c0247-a75c-4566-8387-6c4276ac75b4)

#### Automated Incident Response Pipeline Design (Orchestrated via Shuffle & ElastAlert)
While manual analysis identified these events as Medium Risk ("M"), relying on human analysis slows response windows. The following automated logic loop was designed to scale remediation:

[Wazuh Alert Generated]
│
▼
[ElastAlert Sync Engine] ────> [Create Incident Case in TheHive]
│
▼
[Shuffle SOAR Playbook]
│
┌────────────────┴────────────────┐
▼                                 ▼
[Query Cortex Analyzers]             [Check MISP TIP Base]
│                                 │
└────────────────┬────────────────┘
│
▼
{Is Threat Score > Threshold?}
│
┌────────┴────────┐
▼ YES             ▼ NO
[Auto-Block IP on pfSense]   [Log to Watchlist]

---

### Scenario 2: Automated Phishing Ingestion & Dynamic Triage (E-mail Analysis)

Phishing remains a top initial access vector. This playbook automates the heavy-lifting triage loop typically performed manually by Tier-1 Analysts.

#### Triage Architecture Workflow
The workflow ingests suspected emails, parses metadata observables, passes them to intelligence engines, and provides rapid verdict tracking:

![Phishing Ingest Flow](https://github.com/alarkhis/NG-SOC/assets/58915338/9dfdb5bb-968a-49e0-b6c9-96ff5ae14d7b)

1. **Ingestion & Alert Ingestion:** Incoming emails are automatically captured. Headers, text bodies, attachments, and URLs are parsed dynamically into dedicated artifacts.
2. **Case Creation:** An automated ticket is built inside **TheHive** listing all parsed metadata.

![TheHive Incident Case View](https://github.com/alarkhis/NG-SOC/assets/58915338/546e88f2-2a19-481e-8285-80f0c85ce1e7)

3. **Observable Extraction & Cortex Enrichment:**
   * **File Hashes:** Sent to automated analysis suites.
   * **URLs/Domains:** Evaluated live using the `Urlscan.io` analyzer.
   * **IP Addresses:** Queried against **AbuseIPDB** API profiles.

![Cortex Active Analyzers Execution](https://github.com/alarkhis/NG-SOC/assets/58915338/bccc4499-ffd7-4114-b10e-8d6ec339c07b)
![AbuseIPDB Flagging Malicious IP Profile](https://github.com/alarkhis/NG-SOC/assets/58915338/cbf69e07-8f80-4078-a27b-33f2dd9d75de)

4. **Remediation Action:** If Cortex analyzers confirm a malicious threshold (e.g., high abuse confidence score), Shuffle triggers active blocklistings across perimeter layers and purges matching email signatures from corporate inboxes.

---

## 📈 Key Outcomes & System Takeaways

* **Centralized Visibility:** Successfully integrated multi-platform logs into a singular pane of glass using Wazuh.
* **Minimized MTTR:** Deployed the combination of **TheHive + Cortex + Shuffle** to cut out repeatable analytical tasks, minimizing time-wasting overhead during high-priority incident triage.
* **Proactive Defense Stance:** Modeled remediation loops to transition the defensive strategy from simple alert logging into automated containment execution.

---

## 🚀 Roadmap & Future Milestones

* [ ] Deepen **MISP Integration** directly inside the Shuffle engine to enable live stateful IoC lookups and context syncs.
* [ ] Introduce active behavioral tracking baselines to lower overall False Positive Rates across network segment edges.
* [ ] Transition platform deployment definitions into an automated **Docker Compose / Kubernetes GitOps repository** for rapid lab bootstrapping.

