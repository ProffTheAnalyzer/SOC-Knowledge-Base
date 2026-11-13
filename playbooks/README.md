# SOC Investigation Playbooks

These are reusable analyst workflows, not definitive verdicts.

| Playbook | Starting evidence |
|---|---|
| [Suspicious IIS worker child process](suspicious-iis-child-process.md) | `w3wp.exe` spawning `cmd.exe` / `powershell.exe` |
| [Suspicious PowerShell execution](suspicious-powershell.md) | Obfuscated arguments, unusual parentage or network behavior |
| [Phishing URL triage](phishing-url-triage.md) | Reported email, clicked URL or suspicious domain |
| [Template](PLAYBOOK_TEMPLATE.md) | Start a new playbook consistently |

**Triage pattern:** Confirm source data → scope events → reconstruct timeline → investigate related activity → consider benign explanations → document decision and response.

## Identity, persistence and ransomware

| Playbook | Starting question |
|---|---|
| [Unexpected NTLM network authentication](suspicious-ntlm-authentication.md) | Is this remote logon authorized? |
| [Suspicious Entra sign-in / MFA](suspicious-sign-in.md) | Is identity access expected for this person/device? |
| [New service, task or registry persistence](suspicious-persistence.md) | Who created this artifact and why? |
| [Potential ransomware precursor activity](possible-ransomware-precursors.md) | Is this normal IT change or malicious impact staging? |

See the [normal-vs-suspicious matrix](../analysis/normal-vs-suspicious-behavior.md) for fast behavior comparison.

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
