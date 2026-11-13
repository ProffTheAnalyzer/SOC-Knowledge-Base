# Behavioral analysis — normal vs. suspicious

These field guides connect technical concepts with **analyst decisions**. Use them alongside the [master SOC notebook](../notes/SOC_Master_Notebook_Clean.md).

> **How to read:** *Normal* means plausible authorized activity, not automatically harmless. *Suspicious* means a reason to investigate, not automatic evidence of compromise. A good disposition considers identity, host, time, process lineage, request context and corroborating telemetry.

| Guide | Scope |
|---|---|
| [Behavior decision matrix](normal-vs-suspicious-behavior.md) | Fast cross-domain comparisons and pivots |
| [Windows process and persistence baselines](windows-process-baselines.md) | Parents/children, paths, services, Run keys, scheduled tasks |
| [Authentication and cloud identity](identity-and-cloud-behavior.md) | NTLM, Kerberos, MFA, tokens and risky consent |
| [Network, email and ransomware](network-email-and-ransomware.md) | Scanning, beacon hypotheses, email clicks and attack-chain staging |
| [Event and telemetry matrix](../references/event-telemetry-matrix.md) | What source to consult and key collection prerequisites |

## What is a useful signal?
A useful detection pairs a **specific observation** with context that differentiates it from business behavior. For example, `w3wp.exe` spawning `powershell.exe` is noteworthy; a suspicious inbound request, unexpected child command line, and newly created executable on the web server make the case stronger.

## Applying the guides
Use the examples as investigation hypotheses and verify the telemetry, baseline and outcome in the environment under review.

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
