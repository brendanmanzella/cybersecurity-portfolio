# Microsoft Defender for Endpoint – Ransomware Simulation Lab

![Platform](https://img.shields.io/badge/Platform-Microsoft%20Defender%20for%20Endpoint-blue)
![Lab Type](https://img.shields.io/badge/Lab%20Type-Evaluation%20Lab-green)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

## Overview

This project documents a hands-on ransomware detection and response lab performed inside the **Microsoft Defender for Endpoint (MDE) Evaluation Lab** using the built-in attack simulation environment. The goal was to simulate a realistic ransomware incident, observe EDR detections, triage alerts, review automated investigation results, and perform KQL-based threat hunting — all skills directly applicable to a Tier 1 SOC Analyst role.

---

## Objectives

- Provision and configure evaluation lab devices (Windows 11 desktop + Windows Server)
- Execute a simulated ransomware attack using MDE's built-in attack scenarios
- Observe and triage a High severity incident in the Defender portal
- Review automated investigation and remediation actions
- Perform threat hunting with KQL in Advanced Hunting
- Document findings in a format aligned with SOC analyst workflows

---

## Environment

| Component | Details |
|---|---|
| Platform | Microsoft Defender for Endpoint Plan 2 (Trial) |
| Portal | https://security.microsoft.com |
| Lab Type | Built-in Evaluation Lab (Azure-hosted VMs) |
| Devices | Windows 11 Desktop + Windows Server 2019 |
| Attack Scenario | Ransomware Simulation (MDE built-in) |
| Additional Sim | Living off the Land / Fileless Attack |
| Hunting Tool | Advanced Hunting (KQL) |

---

## Lab Setup

### Step 1 – Provision the Evaluation Lab

1. Log into the Microsoft Defender portal at `security.microsoft.com`
2. Navigate to **Endpoints > Evaluation & tutorials > Evaluation lab**
3. Click **Setup lab** and accept the terms
4. Select **2 devices** (Windows 11 Desktop + Windows Server 2019)
5. Click **Deploy** — devices provision within ~10 minutes
6. Record RDP credentials shown on the Devices tab

---

## Attack Simulation

### Step 2 – Run the Ransomware Simulation

1. In the Evaluation Lab, navigate to the **Attack scenarios** tab
2. Select **Ransomware attack** and click **Run attack**
3. Download the provided PowerShell script (`.ps1`)
4. RDP into the Windows 11 device using the provided credentials
5. Open PowerShell as Administrator and run:

```powershell
Set-ExecutionPolicy Bypass -Scope Process
.\ransomware.ps1
```

6. Observe file encryption behavior and the ransom note dropped by the sim
7. Note: This is a **safe, benign simulation** — no real malware is executed

---

## Incident Triage

### Step 3 – Investigate the Incident in the Portal

1. Navigate to **Incidents & alerts > Incidents**
2. Identify the new **High severity** ransomware incident
3. Click the incident to open the full investigation view
4. Review:
   - **Alert chain** (process tree showing script → encryption → alert)
   - **Affected devices and users**
   - **MITRE ATT&CK technique mappings**

---

## Automated Investigation

### Step 4 – Review Automated Investigation & Remediation

1. Open the **Automated investigations** tab within the incident
2. Review the investigation graph (malware node, affected files, processes)
3. Check the **Action center** for pending or completed remediation actions (e.g., quarantine, process termination)
4. Approve any pending actions if present

**Key Observations:**
- MDE detected ransomware-like behavior within seconds of script execution
- Automated investigation identified malicious process tree and quarantined artifacts
- No manual remediation required — behavioral blocking stopped lateral spread

---

## Threat Hunting (KQL)

### Step 5 – Advanced Hunting Queries

Navigate to **Advanced hunting** in the portal and run the following queries. See [`queries/threat-hunting.kql`](queries/threat-hunting.kql) for all saved queries.

**Detect ransomware-related PowerShell execution:**
```kql
DeviceProcessEvents
| where Timestamp > ago(1h)
| where FileName =~ "powershell.exe"
| where ProcessCommandLine contains "ransom" or ProcessCommandLine contains "encrypt"
| project Timestamp, DeviceName, FileName, ProcessCommandLine, FolderPath
| take 20
```

**Find file modification events during sim window:**
```kql
DeviceFileEvents
| where Timestamp > ago(1h)
| where ActionType == "FileModified" or ActionType == "FileRenamed"
| where FolderPath contains "Documents" or FolderPath contains "Desktop"
| project Timestamp, DeviceName, FileName, FolderPath, ActionType
| take 20
```

---

## Findings & Outcomes

| Finding | Detail |
|---|---|
| Detection Time | < 30 seconds from script execution |
| Severity | High |
| MITRE Techniques | T1486 (Data Encrypted for Impact), T1059.001 (PowerShell) |
| Automated Response | File quarantine + process termination |
| EDR Block Rate | 100% — behavioral monitoring prevented lateral spread |

---

## Incident Report

See [`incidents/incident-01-ransomware.md`](incidents/incident-01-ransomware.md) for the full structured incident report including timeline, evidence, containment steps, and lessons learned.

---

## Repository Structure

```
mde-evaluation-lab/
├── README.md                          # This file
├── queries/
│   └── threat-hunting.kql             # All KQL queries used in Advanced Hunting
└── incidents/
    └── incident-01-ransomware.md      # Full incident report
```

---

## Skills Demonstrated

- **EDR Triage**: Investigating High severity incidents in Microsoft Defender XDR
- **Automated IR**: Understanding and approving MDE automated investigation actions
- **Threat Hunting**: Writing and executing KQL queries in Advanced Hunting
- **Incident Documentation**: Structured reporting aligned with SOC analyst workflows
- **MITRE ATT&CK Mapping**: Identifying techniques from alert metadata
- **Behavioral Detection**: Understanding how MDE blocks ransomware via behavioral signals

---

## References

- [Microsoft Defender for Endpoint Evaluation Lab Docs](https://learn.microsoft.com/en-us/defender-xdr/pilot-deploy-defender-endpoint)
- [MDE Demonstration Scenarios](https://learn.microsoft.com/en-us/defender-endpoint/defender-endpoint-demonstrations)
- [KQL Reference for Advanced Hunting](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-overview)
- [MITRE ATT&CK T1486 – Data Encrypted for Impact](https://attack.mitre.org/techniques/T1486/)
