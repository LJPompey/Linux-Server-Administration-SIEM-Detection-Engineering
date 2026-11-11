# Linux-Server-Administration-SIEM-Detection-Engineering

📖 Overview
In enterprise environments, local administration skills must be paired with centralized security and operational monitoring. This project demonstrates practical Linux server administration, intentional service breakage and troubleshooting, and the engineering of a centralized SIEM pipeline to automatically alert on critical outages.

[Read the Full Incident Report on Medium](https://medium.com/@pompey.lamont01/local-linux-administrator-troubleshooting-isnt-enough-building-siem-detection-pipelines-15d6cad80ccf?postPublishedType=initial)

🎯 Objectives
Linux Troubleshooting: Intentionally misconfigure core services (Apache2, DNS) and utilize native CLI tools (journalctl, dig, ping) to diagnose and resolve the failures.

Detection Engineering: Configure a Wazuh SIEM to ingest system journals and generate high-severity alerts when daemon crashes or crash-loops occur.

File Integrity Monitoring (FIM): Monitor critical network configuration files (e.g., /etc/resolv.conf) for unauthorized or accidental modifications.

The Visibility Gap: Demonstrate a "security blind spot" by executing the exact same service outages on an unmonitored server to highlight the necessity of endpoint agents.

🏗️ Architecture & Environment
The project was executed within a locally virtualized enterprise homelab:

Wazuh Manager (sec-box): Ubuntu 22.04 Desktop hosting the centralized SIEM and alerting engine.

Monitored Endpoint (linux-client): Ubuntu 22.04 Desktop running the Wazuh agent, Apache2, and systemd-resolved.

Unmonitored Endpoint (corp-svr): Ubuntu Server 22.04 acting as the blind-spot demonstration environment.

🛠️ Tools & Technologies
OS: Ubuntu Server 22.04, Ubuntu Desktop 22.04

SIEM/Security: Wazuh, File Integrity Monitoring (FIM)

Services: Apache2, systemd-resolved, UFW (Uncomplicated Firewall)

CLI Tools: systemctl, journalctl, dig, ping, nano, openssl

🚀 Key Scenarios Executed
1. Service Crash & Alert Generation
Action: Modified /etc/apache2/ports.conf to bind to an invalid port (999999), causing the Apache daemon to fail on startup.

Diagnosis: Utilized journalctl -xeu apache2.service to isolate the binding error.

Detection: Engineered a custom Wazuh monitor filtering for Rule ID 30101 (Apache: error restarting) specifically on the linux-client agent, triggering a centralized dashboard alert.

2. Network Outage & File Integrity Monitoring
Action: Overwrote the nameserver in /etc/resolv.conf to a dead IP (192.0.2.0), breaking external DNS resolution.

Diagnosis: Verified the drop using ping and isolated the unreachable nameserver using dig @192.0.2.0.

Detection: Configured local ossec.conf overrides to force real-time File Integrity Monitoring (FIM) on /etc/resolv.conf, successfully generating an alert upon file modification.

3. The Visibility Gap
Action: Replicated the Apache2 crash on corp-svr, which intentionally lacks a Wazuh agent.

Result: Proved that without proper endpoint telemetry, critical service failures remain invisible to the Security Operations Center (SOC).
