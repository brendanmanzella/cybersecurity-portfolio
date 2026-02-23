# Incident 01 – Brute-Force Login Attempts

## Executive Summary
**Alert Type:** Brute-force authentication attack  
**Severity:** Medium  
**Affected Asset:** Windows workstation  
**Timeline:** 10:05–10:15  
**Status:** Contained (lab simulation)

## Incident Timeline
- **10:05:** Multiple failed logon attempts detected
- **10:07:** Wazuh alert triggered (rule ID: 5501)
- **10:10:** Investigation confirmed 12 failed logons in 5 minutes
- **10:15:** Activity confirmed as controlled lab test

## Investigation Steps
1. **Initial Triage:** Wazuh dashboard showed Event ID 4625 with high volume
2. **Log Validation:** Confirmed events in Windows Security log
3. **Context Gathering:**
   - Target: Local Administrator account
   - Source: Local console
   - Frequency: 12 attempts in 5 minutes
4. **Risk Assessment:** Pattern matches brute-force attack

## Key Evidence
Rule ID: 5501
Rule Description: Multiple Windows logon failure events
Log Source: Windows Security Event ID 4625
Occurrences: 12 in 300 seconds

## Containment & Remediation
**Immediate:**
- Account lockout policy triggered (10 attempts)
- MFA enforcement recommended

**Long-term:**
- Monitor repeated failures from remote sources
- Tune SIEM rule threshold for environment

## Lessons Learned
- Event ID 4625 is primary brute-force indicator
- Wazuh correlates authentication failures effectively
- Document source context before escalation
