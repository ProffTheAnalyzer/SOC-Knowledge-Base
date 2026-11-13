# Cybersecurity Knowledge Base

My practical reference library for **SOC investigations, threat hunting, Windows and cloud security, and incident response**.

I document cybersecurity concepts, investigation techniques, attacker behaviors, and lessons learned as I continue to explore and gain hands-on experience. This is an evolving knowledge base that I use for my own reference, continuous learning, and practical investigations, while also sharing useful insights with others in the cybersecurity community.

I may revisit, expand, and update topics as I gain new insights and experience.

## Start here

| Resource | Purpose |
|---|---|
| [Master SOC Notebook](notes/SOC_Master_Notebook_Clean.md) | Fifteen technical chapters covering Windows, identity, attacker techniques, malware, cloud and investigations |
| [Quick Pivot Sheet](notes/SOC_Quick_Pivot_Clean.md) | Find the relevant topic from a process, command, event ID or artifact |
| [Normal vs. Suspicious Behavior](analysis/README.md) | Compare expected and concerning activity across security domains |
| [SOC Investigation Playbooks](playbooks/README.md) | Repeatable investigation workflows and evidence collection |
| [Event Telemetry Matrix](references/event-telemetry-matrix.md) | Event providers, collection prerequisites and interpretation |
| [Technical References](references/README.md) | Primary documentation and technical validation guidance |

## Behavioral analysis

- [Cross-domain behavior decision matrix](analysis/normal-vs-suspicious-behavior.md)
- [Windows processes, services and persistence](analysis/windows-process-baselines.md)
- [Identity and cloud activity](analysis/identity-and-cloud-behavior.md)
- [Network, email and ransomware behavior](analysis/network-email-and-ransomware.md)

## Investigation playbooks

| Playbook | Starting point |
|---|---|
| [Suspicious IIS worker child process](playbooks/suspicious-iis-child-process.md) | Web application worker launching a shell |
| [Suspicious PowerShell](playbooks/suspicious-powershell.md) | Unusual commands, lineage or network activity |
| [Phishing URL triage](playbooks/phishing-url-triage.md) | Reported email or suspicious link |
| [Unexpected NTLM authentication](playbooks/suspicious-ntlm-authentication.md) | Network logon from an unfamiliar source |
| [Suspicious Entra sign-in or MFA](playbooks/suspicious-sign-in.md) | Identity or session anomaly |
| [Suspicious persistence](playbooks/suspicious-persistence.md) | New service, task or registry change |
| [Potential ransomware precursors](playbooks/possible-ransomware-precursors.md) | Staging, remote execution or file impact |
| [Investigation template](playbooks/PLAYBOOK_TEMPLATE.md) | Reusable case workflow |

## Related project

- [SOC Lab](https://github.com/ProffTheAnalyzer/Ebenezer-SOC-Lab) — hands-on security platform deployment, logging and automation.

## Using the library

An indicator is not automatically proof of compromise. Confirm the telemetry source, host, identity, process instance, time, scope, business purpose and actual outcome. Use the technical references to check product-specific behavior and collection requirements.

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
