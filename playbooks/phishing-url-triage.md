# Phishing URL triage

> **Scope:** Reported phishing messages and URL triage. Handle live malicious links in isolated, approved tooling only.

## Starting data
- Original reported message, time received, sender and recipient
- Message headers, message/network ID (when available), full URLs and delivery action
- User report of clicking, credentials submitted, file downloaded or MFA prompt

## Investigation
1. Preserve the original message and raw headers. Inspect SPF/DKIM/DMARC outcomes, knowing that a pass does not prove the message is benign.
2. Decode URL encoding/shorteners using approved tools without navigating to the destination on your normal workstation.
3. Compare sender identities, display names, domains, lookalikes and reply-to addresses.
4. Check URL/domain reputation and infrastructure age/context, without relying solely on public reputation verdicts.
5. Correlate mail delivery, Safe Links click telemetry if licensed, web proxy/DNS telemetry and sign-ins.
6. If interaction occurred, investigate authentication anomalies, OAuth consent, endpoint downloads and potential credential disclosure.
7. Scope other recipients and communicate containment recommendations under your organization's playbook.

## Useful tools and precautions
- URLscan, VirusTotal, URLhaus, Google Safe Browsing, PhishTank, dnstwist and other appropriate resources
- Do not send private customer URLs, tracking tokens, uploaded attachments or proprietary IOCs to public sites without authorization.
- Reputation, rendering and URL scanning results can be stale, incomplete or unsafe to treat as definitive.

## Close-out
Record evidence, affected users, disposition, confidence, actions, and any follow-up monitoring.

Related: [master reference — threat intelligence and SOC operations](../notes/SOC_Master_Notebook_Clean.md#13-threat-intelligence-and-soc-operations).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
