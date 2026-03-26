# Incident Report – IR-001: Ransomware Simulation Detection

**Incident ID:** IR-001  
**Date:** 2026-03-26  
**Analyst:** Brendan Manzella  
**Severity:** High  
**Status:** Resolved (Automated Remediation)  
**Platform:** Microsoft Defender for Endpoint – Evaluation Lab  

---

## Executive Summary

A simulated ransomware attack was executed against a Windows 11 evaluation lab device using Microsoft Defender for Endpoint's built-in attack scenario. The attack involved PowerShell-based file encryption and ransom note creation. MDE detected the behavior within seconds, automatically blocked the attack chain, quarantined malicious artifacts, and opened a High severity incident for analyst review. This report documents the full detection, investigation, and response process.

---

## Incident Timeline

| Time (UTC) | Event |
|---|---|
| T+00:00 | Ransomware PowerShell script downloaded to eval lab VM via Attack scenarios tab |
| T+00:35 | Script executed via PowerShell (Admin) on Windows 11 eval device |
| T+00:48 | MDE Behavioral Monitoring triggers — ransomware behavior detected |
| T+00:51 | Process blocked; automated investigation launched |
| T+01:10 | Ransom note dropped to Desktop (benign sim artifact) |
| T+01:15 | High severity incident created in Defender portal |
| T+02:30 | Automated investigation completes — malicious process tree remediated |
| T+03:00 | Action center shows file quarantine and process termination confirmed |
| T+05:00 | Analyst reviews alert chain, runs KQL queries, exports results |
| T+07:00 | Lab cleaned up; incident documented |

---

## Affected Assets

| Asset | Details |
|---|---|
| Device Name | WIN11-EVALLAB-01 |
| OS | Windows 11 Enterprise |
| User Account | Administrator (eval lab local account) |
| Environment | MDE Evaluation Lab (Azure-hosted, isolated VM) |

---

## Attack Description

### Initial Access
- Attack scenario initiated from within the MDE Evaluation Lab portal
- PowerShell script (`.ps1`) downloaded via Attack scenarios tab
- Script executed in an elevated PowerShell session

### Execution
- Script invoked via: `Set-ExecutionPolicy Bypass -Scope Process; .\ransomware.ps1`
- PowerShell process spawned child processes to enumerate and encrypt user files
- File extensions appended with encrypted suffix (benign sim behavior)

### Impact
- Files in `C:\Users\Administrator\Documents` modified (renamed/encrypted by sim)
- Ransom note (`README_RECOVER_FILES.txt`) dropped to Desktop
- No real data loss — benign simulation environment

---

## Detection Details

| Field | Value |
|---|---|
| Alert Title | Ransomware-linked behavior detected |
| MITRE Tactic | Impact |
| MITRE Technique | T1486 – Data Encrypted for Impact |
| MITRE Sub-Technique | T1059.001 – PowerShell |
| Detection Source | Behavioral Monitoring (EDR) |
| Detection Time | < 30 seconds from script execution |
| Confidence | High |

---

## Evidence

### KQL Query Results (Summary)

Queries run in Advanced Hunting confirmed:
- PowerShell parent process with encryption-related command line arguments
- Child process spawning consistent with ransomware behavior pattern
- Mass file rename/modification activity in user directories (Documents, Desktop)
- Ransom note file creation event captured in `DeviceFileEvents`
- `DeviceEvents` table confirmed `BehaviorPrevented` and `AntivirusRemediation` action types fired

Full KQL queries: [`queries/threat-hunting.kql`](../queries/threat-hunting.kql)

### Portal Observations
- High severity incident appeared in Incidents & alerts within ~45 seconds of script execution
- Alert chain: `powershell.exe` → child process → file encryption → ransom note drop
- MITRE technique T1486 automatically tagged on the alert
- Automated investigation graph mapped the full attack chain without manual pivoting
- Action center showed quarantine and process termination completed automatically

---

## Investigation Steps

1. **Identified the incident** in Incidents & alerts > Incidents — High severity, ransomware category
2. **Reviewed alert chain** — confirmed PowerShell execution as root cause
3. **Checked MITRE mapping** — T1486 (Data Encrypted for Impact) confirmed
4. **Opened Automated investigations tab** — reviewed investigation graph showing malicious process tree
5. **Reviewed Action center** — confirmed quarantine of malicious script and process termination
6. **Ran Advanced Hunting queries** — validated process events, file modifications, alert metadata
7. **Verified containment** — no lateral movement detected; attack contained to single device

---

## Containment & Remediation

| Action | Method | Status |
|---|---|---|
| Process termination | Automated (MDE behavioral block) | Completed |
| Malicious file quarantine | Automated (MDE auto-investigation) | Completed |
| Device isolation | Not required (eval lab, isolated by design) | N/A |
| User account review | Not required (local admin, eval only) | N/A |
| Lab cleanup | Manual (Evaluation lab > Clean up) | Completed |

---

## Root Cause Analysis

**Root Cause:** Authorized simulation – ransomware PS1 script manually executed by analyst in isolated lab VM for SOC skill development and portfolio documentation.

---

## Lessons Learned

1. **MDE behavioral detection is fast** — detected ransomware patterns in under 30 seconds via behavioral signals alone (file encryption rate, process ancestry)
2. **Automated investigation reduces analyst burden** — investigation graph mapped the full attack chain without manual pivoting
3. **KQL Advanced Hunting is essential** — manual hunt queries validated automated findings and surfaced additional process tree context
4. **MITRE ATT&CK mapping aids communication** — technique tagging makes it easy to explain detections to stakeholders
5. **Action center approval workflow** — in production, high-impact remediation may require analyst approval before execution

---

## Recommendations

- Enable **Controlled Folder Access** to block unauthorized writes to user directories
- Restrict **PowerShell execution policy** via GPO (AllSigned or RemoteSigned)
- Enable **Attack Surface Reduction (ASR) rules** targeting Office macro abuse and process injection
- Configure automated investigation to **auto-remediate** high-confidence detections
- Tune Advanced Hunting scheduled queries to alert on mass file rename events (>50 files/min)

---

## References

- [MITRE T1486 – Data Encrypted for Impact](https://attack.mitre.org/techniques/T1486/)
- [MITRE T1059.001 – PowerShell](https://attack.mitre.org/techniques/T1059/001/)
- [MDE Automated Investigation](https://learn.microsoft.com/en-us/defender-endpoint/automated-investigations)
- [MDE Action Center](https://learn.microsoft.com/en-us/defender-xdr/m365d-action-center)
- [Controlled Folder Access](https://learn.microsoft.com/en-us/defender-endpoint/controlled-folders)
