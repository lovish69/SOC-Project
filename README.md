SOC Project - Security Operations Center (SOC) Simulation

📌 Overview

This project is a Security Operations Center (SOC) simulation, where I set up a Windows victim machine and a Linux attacker machine to analyze security logs, detect threats, and simulate adversary tactics.

🔹 Key Objectives:

✅ Learn SOC fundamentals and security monitoring.
✅ Set up a Windows & Linux lab environment.
✅ Use Sysmon, LimaCharlie (EDR), and Sliver (C2) for threat detection.
✅ Perform malware simulations and analyze detections.

| Category          | Tools Used |
|------------------|------------------|
| **Operating Systems** | Windows 11, Ubuntu Server |
| **Security Monitoring** | Sysmon, Windows Event Logs |
| **EDR (Endpoint Detection & Response)** | LimaCharlie |
| **Command & Control (C2)** | Sliver |
| **Forensics & Detection** |  Regedit |

📂 Project Structure
🔹 Part 1: Setting Up the SOC Lab

✅ Installed Ubuntu Attacker Machine & Windows 11 Victim Machine.
✅ Disabled Windows Defender & Security Services (via Registry & GPO).
✅ Installed Sysmon to collect detailed event logs.
✅ Installed LimaCharlie (EDR) to monitor endpoint activity.

🔹 Part 2: Establishing Command & Control (C2)

✅ Set up Sliver C2 Framework on Ubuntu.
✅ Generated a malicious payload and transferred it to Windows.
✅ Executed the payload and established a C2 connection.
✅ Interacted with Windows using Sliver’s implants (process list, privilege escalation).
✅ Observed real-time detections in LimaCharlie EDR.

🔹 Part 3: Advanced Threat Simulation & Log Analysis
✅ Attacking on victim machine with sliver
✅ Taking on credentials
✅ detection 
