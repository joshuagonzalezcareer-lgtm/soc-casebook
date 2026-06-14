# SOC Casebook

A home Security Operations Center (SOC) lab where I document simulated and real-world
incident investigations — from initial detection through analysis, response, and lessons learned.

This repository is both a **working lab journal** and a **detection-engineering portfolio**:
custom rules, MITRE ATT&CK-mapped incident reports, and the architecture that produces the
telemetry behind them.

---

## Lab Architecture

A two-machine virtualized SOC built on VirtualBox, with a Wazuh SIEM collecting telemetry
from Windows and Linux victim endpoints while a Kali attacker generates detection data.

![Lab architecture](docs/architecture/lab-architecture.png)

> Full architecture notes, network topology, and build decisions: [`docs/architecture/`](docs/architecture/)

**Stack at a glance**

| Role | Component |
|------|-----------|
| SIEM | Wazuh (All-in-One) |
| Attacker | Kali Linux |
| Windows victim | Windows 10 LTSC + Sysmon (SwiftOnSecurity config) + Wazuh agent |
| Linux victim | Ubuntu Server + DVWA + Wazuh agent |
| Firewall | pfSense / OPNsense *(planned)* |
| Honeypot | Cowrie on Raspberry Pi Zero 2 W *(planned)* |
| IR automation | Shuffle SOAR + TheHive *(planned)* |

---

## Repository Map

| Folder | What's inside |
|--------|---------------|
| [`docs/`](docs/) | Architecture, network topology, and lab build documentation |
| [`detections/`](detections/) | Custom SIGMA rules, Wazuh rules, and YARA signatures |
| [`incidents/`](incidents/) | Investigation writeups, each mapped to MITRE ATT&CK |
| [`playbooks/`](playbooks/) | Response procedures and (later) SOAR automation workflows |
| [`setup/`](setup/) | Step-by-step build guides for reproducing the lab |

---

## Incident Investigations

Each case follows a consistent format: summary, timeline, detection logic, ATT&CK mapping,
and response actions.

| # | Investigation | Technique(s) | Status |
|---|---------------|--------------|--------|
| 01 | _(coming soon)_ | — | Planned |

---

## Detection Engineering

Custom detection content developed and validated in the lab.

- **SIGMA rules** — portable detections in [`detections/sigma/`](detections/sigma/)
- **Wazuh rules** — SIEM-native rules in [`detections/wazuh/`](detections/wazuh/)
- **YARA rules** — malware signatures in [`detections/yara/`](detections/yara/)

---

## Skills Demonstrated

SIEM configuration · log analysis · threat detection · detection engineering ·
incident response · MITRE ATT&CK mapping · adversary emulation · virtualization &
network segmentation

## Certifications

CompTIA Security+ · ISC2 Certified in Cybersecurity (CC) · Antisyphon SOC Core Skills V3
---

*This is a personal learning lab. All attacks are performed against systems I own,
in an isolated environment.*
