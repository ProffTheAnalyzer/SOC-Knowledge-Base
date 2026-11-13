# Network, email and ransomware — expected behavior vs. investigation signals

> **Scope:** Scanner triage, DNS/C2 hypotheses, email and URL handling, ransomware precursor activity.

## Network and C2 hypotheses

| Behavior | Benign examples | Investigate when | Correlate |
|---|---|---|---|
| Thousands of SYN packets to many ports | Authorized Nessus/OpenVAS, inventory, external exposure scanning | Not on approved scanner list, access outside scope, successful exploitation evidence | Firewall, asset/assessment scope, server logs |
| Repeated HTTP probes at Exchange/OWA paths | External reconnaissance and internet background noise | Relevant app vulnerability + successful follow-on execution/file change | WAF/IIS, endpoint file/process, change status |
| Periodic connection to single domain | Updater, telemetry agent, health check | Unrecognized process, unusual domain and repeatable timing, payload activity | DNS, proxy, EDR process, certificate/reputation |
| Unexpected outbound high byte volume | Backups, collaboration, vendor syncing | Unknown destination + archive staging + unusual privileged access | Proxy/DLP, file events, account logs |
| Connection to public DNS resolver | Operating system, browser, application DNS | Unauthorized tunnel-like volume or abnormal query payloads | Network, DNS analytics, process, policy |

**Do not claim a web intrusion is safe merely because requests returned 401/404, or confirmed merely because one request returned 200.** Status codes require application context and correlated server-side evidence.

## Phishing / email analysis

| Observation | Normal possibility | Suspicious context |
|---|---|---|
| Spoof-like display name | Common/duplicated names, legitimate marketing | Reply-to mismatch, lookalike domain, urgent payment or credentials |
| SPF/DKIM/DMARC pass | Properly authenticated sender or compromised legitimate mailbox | Malicious email can pass all checks |
| Short URL or QR code | Normal tracking/marketing | New login lure, credential request, atypical redirect chain |
| Safe Links click event | Security tool or user browsing | Affected identity enters credentials or subsequent risky sign-ins |
| Inbox rule / OAuth grant after message | Legitimate user workflow | Unapproved mail forwarding + consent + external data read |

**Safety:** Open suspected malicious content only in approved isolated environments. Do not submit live customer emails, full URLs with tokens, or private attachments to public reputation sites without authorization.

## Ransomware precursor chain

| Stage | Commonly authorized activity | Concerning evidence |
|---|---|---|
| Initial access | Vendor support and approved RDP | Unrecognized remote access followed by suspicious process lineage |
| Discovery | Inventory, asset management, IT troubleshooting | First-seen discovery tooling + privileged pivot across multiple systems |
| Credential access | LSASS interactions by security products or backup tools | Unexpected sensitive-process access by unknown executable |
| Lateral movement | PsExec, WinRM, SMB maintenance | Unknown admin source, remote task/service creation on many hosts |
| Staging and transfers | Packaging, backup, migration | Rapid archiving of sensitive shares and unfamiliar destination |
| Defense evasion | Planned AV maintenance | Unexpected exclusions, disabling protection or log clear |
| Encryption/impact | Bulk legitimate file operations | Rapid failures/open-denied files, ransom notes, unauthorized mass modification |

**Nuance:** Not every ransomware incident involves every stage; not all campaigns exfiltrate, gain domain admin or establish traditional persistence. Attack-chain reconstruction is a working hypothesis, not a mandatory checklist.

## Example investigator flow: many file renames

1. Verify the process and actual changed file types and paths.
2. Determine whether change windows, backup, sync, migration or user jobs explain the event.
3. Correlate file activity with shadow-copy manipulation, suspicious service/task activity and new remote access.
4. Scope number of machines/shares impacted and the user's access rights.
5. Apply response playbook based on observed impact and authorization.

See: [phishing playbook](../playbooks/phishing-url-triage.md), [ransomware-precursor playbook](../playbooks/possible-ransomware-precursors.md), [MITRE ATT&CK](https://attack.mitre.org/), [Microsoft Defender hunting tables](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-schema-tables).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
