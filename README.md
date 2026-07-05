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
