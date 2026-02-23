# Incident 02 – Suspicious PowerShell Activity

## Executive Summary
**Alert Type:** Suspicious PowerShell execution  
**Severity:** Medium-High  
**Affected Asset:** Windows workstation  
**Timeline:** 11:20–11:25  
**Status:** Investigated (lab simulation)

## Incident Timeline
- **11:20:** PowerShell execution with encoded command detected
- **11:22:** Wazuh alert triggered (rule ID: 59001)
- **11:24:** Confirmed execution was controlled PowerShell testing
- **11:25:** Documented for detection tuning

## Investigation Steps
1. **Initial Triage:** Wazuh flagged PowerShell with "-EncodedCommand" parameter
2. **Log Validation:** Reviewed PowerShell Operational log (Event ID 4104)
3. **Context Analysis:**
   - Command: Base64-encoded payload
   - Account: Local Administrator
   - Parent process: None (direct execution)
4. **Risk Assessment:** Encoded commands indicate living-off-the-land techniques

## Key Evidence
Rule ID: 59001
Rule Description: Suspicious PowerShell execution with encoded command
Log Source: PowerShell Operational Event ID 4104
MITRE ATT&CK: T1059.001 (PowerShell)
Indicators: -EncodedCommand parameter

## Containment & Remediation
**Immediate:**
- Would isolate host for forensic collection
- Review recent PowerShell execution history

**Long-term:**
- Enable Script Block Logging and Module Logging
- Constrain PowerShell execution policy
- Block encoded command execution via GPO

## Lessons Learned
- PowerShell -EncodedCommand is high-signal behavioral indicator
- Wazuh default rules catch living-off-the-land techniques effectively
- Need process tree visibility for execution context
