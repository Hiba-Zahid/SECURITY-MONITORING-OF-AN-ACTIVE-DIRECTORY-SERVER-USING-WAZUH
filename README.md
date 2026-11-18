# 🛡️ Active Directory Security Monitoring using Wazuh SIEM

![Platform](https://img.shields.io/badge/Platform-Oracle_VirtualBox-blue)
![SIEM](https://img.shields.io/badge/SIEM-Wazuh_v4.14.8-orange)
![Target](https://img.shields.io/badge/Target-Windows_Server_AD_DC-red)
![Attacker](https://img.shields.io/badge/Attacker-Kali_Linux-black)

## 📌 Executive Summary
This repository documents a complete, end-to-end security monitoring lab that implements a **Wazuh SIEM** platform to monitor an Active Directory (AD) domain controller for malicious activity[cite: 22, 24]. Conducted entirely inside **Oracle VirtualBox**, the lab covers everything from environment deployment and advanced Windows audit policy hardening to targeted attack simulations and SIEM alert verification[cite: 24].

---

## 🏗️ Lab Architecture & Network Topology

The lab environment consists of three virtual machines communicating over a unified network via **Bridged Adapters** with promiscuous mode enabled[cite: 24, 25]:

| Virtual Machine | Role / Operating System | IP Address Scope |
| :--- | :--- | :--- |
| **Wazuh Server** | Wazuh v4.14.8 OVA Appliance (SIEM Manager, Indexer, Dashboard)[cite: 24, 25] | `192.168.100.12`[cite: 24] |
| **AD Server (`AD-DC-001`)** | Windows Server 2022 (Domain Controller, `corp.local`, Wazuh Agent)[cite: 24, 28] | `192.168.100.6`[cite: 24] |
| **Kali Linux** | Attacker Machine (Impacket, Samba utilities)[cite: 24, 33, 34] | Dynamically assigned via bridge[cite: 24] |

---

## ⚙️ Setup & Configuration Highlights

### 1. Wazuh Manager & Agent Deployment
* Deployed the official Wazuh OVA appliance and verified active services (`wazuh-indexer`, `wazuh-manager`, `wazuh-dashboard`)[cite: 25, 26, 27].
* Installed and registered the **Wazuh Agent (`AD-DC-001`)** silently on the Windows Server domain controller via PowerShell (`Invoke-WebRequest` and `msiexec`)[cite: 28, 29].

### 2. Windows Audit Policy Hardening (Group Policy)
To capture targeted events, advanced auditing was configured via the **Default Domain Controllers Policy**:
* **Process Creation Auditing:** Enabled *Audit Process Creation* (`Success and Failure`) under *Detailed Tracking* to generate **Windows Event ID 4688**[cite: 30].
* **Command-Line Logging:** Enabled *'Include command line in process creation events'* to capture full command-line execution arguments in plain text[cite: 31].
* **System Event Auditing:** Enabled *'Audit Other System Events'* (`Success and Failure`) to track system startup, shutdown, and security state alterations[cite: 29].

---

## 🎯 Attack Simulation Scenarios

Two primary threat categories were tested using a dual-method approach (Method A: Direct execution on the DC; Method B: Remote execution from Kali Linux)[cite: 31, 32]:

### Scenario 1: Suspicious Process Execution & Reconnaissance
* **Method A (Local):** Executed stealthy PowerShell web requests using execution-policy bypass parameters (`cmd.exe /c powershell.exe -ExecutionPolicy Bypass...`) and ran privilege enumeration commands (`whoami /priv`)[cite: 32].
* **Method B (Remote):** Utilized **Impacket's `psexec`** from Kali Linux to authenticate as Administrator, upload a service binary (`hLqSopxs.exe`), and execute remote processes over SMB[cite: 33].

### Scenario 2: Unplanned / Unauthorized System Shutdown
* **Method A (Local):** Triggered a forced, immediate shutdown with a non-standard comment (`shutdown /s /f /t 0 /c "Unplanned System Halt Triggered"`)[cite: 33].
* **Method B (Remote):** Executed a remote network shutdown from Kali Linux using Samba's `net rpc shutdown` utility (`net rpc shutdown -I 192.168.100.6 -U Administrator`)[cite: 34].

---

## 🔍 SIEM Verification & Results

Events were verified inside the Wazuh Dashboard under **Threat Hunting > Events**[cite: 34]:

* **Process Creation Alerts:** Filtering by `agent.name: AD-DC-001 AND 4688` surfaced matching hits mapped to **Rule ID 67027** (*"A process was created"*)[cite: 34, 35].
* **Shutdown Alerts:** Captured **Rule ID 67018** (*"System shutdown initiated"*) alongside native Windows events (IDs 1074/6008)[cite: 35].
* **MITRE ATT&CK Mapping:** Summary dashboards correlated simulated behaviors to recognized adversary tactics, including **Valid Accounts**, **Domain Policy Modification**, **System Shutdown/Reboot**, and **Service Execution**[cite: 36].

---

## 👤 Author Information
* **Analyst:** Hiba Zahid[cite: 22]
* **Institution:** Dawood University of Engineering & Technology (DUET), Karachi[cite: 22]
* **Faculty:** Faculty of Information & Computing Sciences (Department of Cybersecurity)[cite: 22]
* **Instructor:** Sir Hammad Raza[cite: 22]
```[cite: 22]
