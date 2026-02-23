# Home SOC Lab – Wazuh + Windows Logs

## Objective
Build a home SOC lab using Wazuh and a Windows VM to detect brute-force login attempts and suspicious PowerShell activity, then document how I investigated and responded to those events.

## Environment & Tools
- Wazuh Manager & Dashboard
- Windows 10/11 virtual machine with Wazuh agent installed
- Hypervisor: VirtualBox / VMware Workstation
- Log sources:
  - Windows Security logs
  - Windows PowerShell logs

## Architecture
Windows VM sends security and PowerShell event logs to the Wazuh manager, where rules generate alerts that I analyze through the Wazuh dashboard.

## Scenario 1 – Brute-Force / Failed Logons

### Goal
Detect repeated failed logon attempts that resemble a brute-force attack against a Windows host.

### Steps I Took
1. Generated multiple failed logons on the Windows VM using an incorrect password several times in a row.
2. Confirmed that the failed logon events appeared in the Windows Security log.
3. Verified that the Wazuh agent forwarded these events to the Wazuh manager.
4. Reviewed the resulting alerts in the Wazuh dashboard and identified the rule that triggered on the repeated failures.

### Outcome / What I Learned
- How failed authentication attempts are recorded in the Windows Security log and forwarded to a SIEM.
- How Wazuh applies rules to detect brute-force patterns based on event frequency and context.
- How to pivot from a SIEM alert into underlying host logs to validate and scope an incident.

---

## Scenario 2 – Suspicious PowerShell Activity

### Goal
Detect potentially malicious PowerShell usage (such as encoded commands) on the Windows host.

### Steps I Took
1. Enabled enhanced PowerShell logging on the Windows VM.
2. Ran a series of test PowerShell commands, including a simulated "high-signal" command.
3. Verified that the PowerShell events were captured in the Windows event logs and forwarded to Wazuh.
4. Located and analyzed the corresponding alerts in the Wazuh dashboard.

### Outcome / What I Learned
- Which types of PowerShell activity are considered high-value indicators from a detection perspective.
- How to use Wazuh to surface suspicious script execution and then verify it on the endpoint.
- How I would document and escalate PowerShell-based threats in a SOC environment.

---

## Skills Demonstrated
- SIEM configuration and log ingestion (Wazuh + Windows)
- Security event analysis (failed logons, PowerShell activity)
- Basic detection engineering (understanding and tuning alert rules)
- Incident documentation and reporting for SOC workflows
