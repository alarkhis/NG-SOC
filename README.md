# Open-Source Next-Generation SOC (NG-SOC) Architecture & Deployment

![Domain - Cybersecurity](https://img.shields.io/badge/Domain-Cybersecurity-red.svg)
![Architecture - NG-SOC](https://img.shields.io/badge/Architecture-NG--SOC-blue.svg)
![Infrastructure - OpenSource](https://img.shields.io/badge/Stack-Open--Source-green.svg)

---

## 📝 Project Overview

This engineering project focuses on the end-to-end architectural design, network segmentation, and deployment of an open-source **Next-Generation Security Operations Center (NG-SOC)**. The platform is engineered to transition legacy security monitoring from reactive alert logging to an automated, intelligence-driven, and proactive defense system. By consolidating decentralized infrastructure telemetry into a unified analysis plane, this project successfully demonstrates a scalable framework to drastically minimize **Mean Time to Detect (MTTD)** and **Mean Time to Respond (MTTR)**.

---

## 🏛️ System & Network Architecture

The architecture relies on crisp network segmentation implemented via a **pfSense** perimeter firewall, dividing the infrastructure into dedicated operational zones to guarantee defense-in-depth:

* **Perimeter & Edge Plane (WAN Zone - 172.16.1.0/16):** Manages external traffic flows and intercepts boundary interactions.
* **Protected Production Segment (LAN Zone - 172.16.1.0/16):** Simulates internal corporate assets running active end-user endpoints (Windows 7 and Windows 10).
* **Isolated Defensive Operations Segment (DMZ Zone - 192.168.146.0/24):** Isolates the core detection, ingestion, and automation layers to prevent security tool compromise from cross-segment attacks.

### Network Flow Diagram
* External WAN Zone (172.16.1.0/16) connects directly down to the **pfSense FW + Snort IPS** core routing layer.
* From the firewall, the traffic splits securely into two distinct directions:
  1. Down to the **Isolated Operational DMZ (192.168.146.0/24)** which hosts the Wazuh Manager, TheHive (SIRP), Cortex, MISP, and the Shuffle SOAR Orchestrator.
  2. Across to the **Internal Corporate LAN (172.16.1.0/16)** which safely contains the target Windows 7 and Windows 10 end-user monitoring agents.

---

## 🛠️ Open-Source Ecosystem Stack

The architectural design utilizes best-in-class open-source defensive components. The table below lists the individual responsibilities and hardware specifications engineered to support this environment:

| Security Component | Tool | Functional Domain | Role within the Infrastructure | Minimum Resource Profile |
| :--- | :--- | :--- | :--- | :--- |
| **SIEM / XDR** | **Wazuh** | Detection & Collection Plane | Centralized log ingestion, correlation, behavioral tracking, and endpoint integrity inspection. | 8 GB RAM / 4-8 vCPUs / 50-200 GB Storage |
| **SIRP** | **TheHive** | Incident Management Plane | Distributed case management, tracking events from initial creation to final remediation closure. | 4-32 GB RAM / 2-8 vCPUs |
| **Analytics Engine**| **Cortex** | Observable Enrichment Plane | Executes rapid automated analysis via specialized API connectors against suspicious observables. | 4-32 GB RAM / 2-8 vCPUs |
| **Threat Intelligence**| **MISP** | Contextual Intelligence Plane| Decentralized storage and replication of structured Indicators of Compromise (IoCs) and global threat feeds. | 4 GB RAM / 2 vCPUs / 20 GB Storage |
| **SOAR** | **Shuffle** | Process Automation Plane | Cross-platform playbook modeling, alert routing, and event orchestration. | 4 GB RAM / 2 vCPUs / 20 GB Storage |
| **IDS / IPS** | **Snort** | Network Telemetry Plane | Deep packet inspection and real-time network traffic signature matching. | Integrated inline within the pfSense Core |
| **Firewall / Router**| **pfSense**| Network Access Control Plane | Dynamic packet routing, stateful firewall rulesets, and strict zone isolation. | 2 GB RAM / 1 vCPU / 10 GB Storage |

---

## 🛡️ Validation Scenarios & Incident Playbooks

### Scenario 1: Brute-Force SSH Intrusion & Proposed Playbook Orchestration

To stress-test perimeter visibility, targeted password-spraying and connection attempts were directed against the pfSense firewall management layer from an external node.

#### Phase 1: Detection & Ingestion
* **Wazuh SIEM Ingestion:** The endpoint agent residing on the firewall intercepted the authentication loop, triggering an immediate level 5 security alert under **Rule ID 5760 (sshd: authentication failed)**.
* **Snort Network Correlation:** Simultaneously, the integrated network engine captured the traffic anomalies and generated outbound telemetry flagging active threat scanning footprints matching standardized attack signatures.
* **Data Flow:** Attacker Node > pfSense FW + Snort IPS > Logs Generated > Wazuh Manager Dashboard.

#### Phase 2: Playbook Design for Automated Countermeasures
While manual analysis properly categorized this threat as a Medium Risk event ("M") within the case management interface, relying exclusively on humans delays remediation windows. The following automated logic loop was designed using **ElastAlert** and **Shuffle SOAR** to accelerate defense loops:

* **Step 1:** Wazuh Security Event is successfully generated.
* **Step 2:** The ElastAlert Ingestion Rules Engine intercepts the metric.
* **Step 3:** An active ticket automatically opens a Security Case inside TheHive.
* **Step 4:** This instantly triggers the Shuffle SOAR Playbook.
* **Step 5:** The playbook runs parallel queries to Cortex Analyzers and consults the MISP IOC Database.
* **Step 6:** A decision condition checks: "Does it match a known Malicious Signature?"
* **Step 7 [YES]:** Auto-Inject an active Block-Rule via the pfSense API Layer to drop the traffic.
* **Step 7 [NO]:** Securely route the task to an on-duty Security Analyst for manual evaluation.

---

### Scenario 2: Automated E-mail Phishing Ingestion & Enrichment Triage

Phishing remains the most prevalent vector for initial network entry. This validation scenario maps how the platform removes the manual overhead of phishing triage from Tier-1 Security Analysts by automating the ingestion, parsing, and analysis lifecycle.

#### Playbook Architecture & Workflow Execution
The workflow captures incoming emails, extracts crucial artifacts, passes them to specialized analysis systems, and flags malicious indicators:

* **Step 1: Observable Extraction:** An incoming malicious email containing complex parameters (embedded URIs, tracking items, and attached artifacts) is intercepted. The system processes the raw string data and divides it into clean, distinct observable objects.
* **Step 2: Case Ingestion:** TheHive parses the context fields to build a unified security case ticket tracking the following parsed parameters:
  * **Sender Account:** Target domain profile (.com)
  * **Recipient Vector:** Target destination profile (.fr)
  * **Tracked Observables:** 23 distinct indicators (Network IPs, Domain Lookups, File Hashes)
  * **Associated URI:** Explicit references redirected toward external resources
* **Step 3: Multi-Engine Intelligence Enrichment:** The parsed indicators are transmitted automatically to **Cortex**, which fires parallel asynchronous API search queries across external threat intelligence databases:
  * **Network IPs:** Queried using **AbuseIPDB**, returning definitive evidence of active malicious reputation records (e.g., Target IP 209.85.214.171 returned clean confirmation hits from active abuse logs).
  * **Links & Domains:** Verified by running live sandboxed lookups using the **Urlscan.io** connector.
* **Step 4: Remediation Loop:** Once the system detects an infrastructure confirmation threshold from AbuseIPDB or Urlscan.io, the **Shuffle** workflow handles mitigation instantly—blocking source addresses, neutralizing active connections, and removing the threat envelope from internal mail infrastructure.

---

## 📈 Engineering Takeaways & Strategic Architecture

* **Unified Visibility:** Aggregated distributed log signatures, network packet traces, and operating system events into a single dashboard using **Wazuh**.
* **Operational Optimization:** Connected **TheHive + Cortex + Shuffle** to cut out repeatable analytical tasks, minimizing analyst burnout and dramatically reducing incident confirmation windows.
* **Scalability & Active Defense:** Transformed traditional passive observation frameworks into a dynamic security infrastructure capable of orchestrating automated mitigation workflows at the network boundary.

---

## 🚀 Future Implementation Milestones

* [ ] Fully automate stateful threat lookups by embedding deeper **MISP synchronization logic** directly into the core **Shuffle SOAR** process loops to minimize false-positive occurrences.
* [ ] Introduce behavioral baselines along the perimeter to lower the signal-to-noise ratio across network edges.
* [ ] Export the entire network definition as a modular **Docker Compose / Infrastructure-as-Code (IaC)** blueprint to facilitate instantaneous deployment bootstrapping.
