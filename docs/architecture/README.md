# Architecture

## Overview
A two-machine virtualized SOC lab built entirely in VirtualBox.

- **Laptop (32GB+)** — analyst/attacker station: Wazuh SIEM, Kali Linux, (later) pfSense, Shuffle SOAR, TheHive
- **Mini PC (16GB)** — victim network: Windows 10 LTSC, Ubuntu Server + DVWA, (later) Windows Server AD
- **Raspberry Pi Zero 2 W** — Cowrie honeypot + Wazuh-monitored endpoint *(planned)*

## Network
Detection loop: **Kali attacks the victims → victim Wazuh agents ship logs → Wazuh detects.**

Current build bridges both physical hosts onto the same network so VMs can reach each other.
The target design uses a 5-port Gigabit switch with host-only/internal segmentation.

## Diagrams
- `lab-architecture.png` — full target architecture
- `network-topology.png` — current network/traffic flow

> Replace the placeholder image files with your exported diagrams.
