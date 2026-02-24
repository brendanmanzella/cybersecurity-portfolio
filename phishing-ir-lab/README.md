# Phishing Incident Response Lab

## Objective
Simulate a phishing incident, analyze malicious email headers and artifacts, then document a complete incident response process from triage to remediation.

## Scenario
Received user report: "Suspicious email claiming to be from IT asking for password reset." Email contained suspicious links and attachment.

## Analysis Performed
1. **Email Header Analysis** – IP geolocation, SPF/DKIM/DMARC validation
2. **URL Analysis** – Domain reputation, redirect chains  
3. **Attachment Analysis** – Safe sandbox execution, IOC extraction
4. **User Impact Assessment** – Account compromise risk

## Key Findings
Sender IP: Suspicious geolocation (high-risk country)
SPF: Fail
DKIM: None
DMARC: None
Attachment: EICAR test file (malware indicator)
Target: Executive account (high business impact)


## Response Playbook Developed
1. **Triage:** Validate urgency, preserve email
2. **Containment:** Quarantine email, reset user password
3. **Eradication:** Full AV scan, review recent logons
4. **Recovery:** MFA re-enrollment, user training
5. **Lessons Learned:** Phishing simulation training needed

**Full incident report:** [`incidents/phishing-incident-01.md`](incidents/phishing-incident-01.md)

## Skills Demonstrated
- Phishing email analysis (headers, SPF/DKIM/DMARC)
- IOC extraction and threat intelligence
- Incident response workflow documentation
- Risk assessment and prioritization
