# SOC / Threat Hunting — Quick Pivot Sheet

Use this alongside the [**Master Reference Notebook**](SOC_Master_Notebook_Clean.md) to find the relevant investigation topic.

[**Back to the library home**](../README.md)

## Which chapter should I open?

| You are looking at... | Category to open | First question to answer |
|---|---|---|
| `HKCU`, `HKLM`, Run keys, Winlogon, IFEO, COM hijack | [02 — Registry / persistence](SOC_Master_Notebook_Clean.md#02-registry-persistence-and-execution-hijacking) | What exactly changed (key, value name, data) and who changed it? |
| Core Windows tree (`System`, `smss.exe`, `wininit.exe`, `services.exe`, `lsass.exe`) | [01 — Windows process baseline](SOC_Master_Notebook_Clean.md#01-windows-fundamentals-and-architecture) | Is the path, signer, parent/start time, session and user consistent with this host? |
| LSASS/DPAPI/DCSync; OAuth access, refresh or session token | [03 — Credentials / tokens](SOC_Master_Notebook_Clean.md#03-windows-authentication-credentials-and-active-directory) | Is this a Windows process token or an API/session credential, and what was actually used? |
| `svcctl`, `srvsvc`, `ADMIN$`, `IPC$`, named pipes | [04 — Remote administration](SOC_Master_Notebook_Clean.md#04-remote-administration-and-lateral-movement) | Is this enumeration, file transfer, service creation, or execution? |
| Event 4624/4688/4769, BAM/Prefetch; unusual Office/browser/IIS process chain | [05 — Telemetry / process trees](SOC_Master_Notebook_Clean.md#05-telemetry-event-logs-and-host-investigation) | What evidence confirms child execution, identity, and outcome? |
| `attrib`, timestomping, packers, UAC, AMSI, 8.3 paths | [06 — Evasion](SOC_Master_Notebook_Clean.md#06-defense-evasion-anti-forensics-and-privilege-escalation) | What protection/artifact is being bypassed or changed? |
| Emotet, Neshta, Cobalt Strike/Sliver, `svchost.com` | [07 — Malware / cases](SOC_Master_Notebook_Clean.md#07-malware-families-beacons-and-incident-case-studies) | Which observations are proven, and which are hypotheses? |
| DNS/JA3, `localhost`, HTTP, Pastebin, rclone, SOCKS | [08 — C2 / network](SOC_Master_Notebook_Clean.md#08-networking-c2-tunneling-and-exfiltration) | External, internal, or loopback traffic? Command, stage, or data? |
| `nltest`, Nessus/OpenVAS, broad port sweeps, Exchange/OWA or Autodiscover probes | [09 — Recon / scan triage](SOC_Master_Notebook_Clean.md#09-reconnaissance-and-directory-discovery) | Is there verified scope/authorization, and evidence of successful access or exploitation? |
| AWS STS, Microsoft Graph, MFA fatigue, impossible travel, token replay | [10 — Cloud identity / MFA](SOC_Master_Notebook_Clean.md#10-cloud-security-identity-and-apis) | Which session, device, MFA result, privilege and API activity follow the sign-in? |
| `.env`, npm hooks, `package.json`, Log4Shell | [11 — Web / supply chain](SOC_Master_Notebook_Clean.md#11-web-applications-and-software-supply-chain) | Is the dependency or secret exposure authorized and expected? |
| File header, PE/PDF, IMPHASH, YARA, sandbox | [12 — Malware analysis](SOC_Master_Notebook_Clean.md#12-malware-analysis-and-reverse-engineering) | What artifacts can be collected without unsafe execution? |
| VirusTotal/Joe Sandbox, AbuseIPDB, URLVoid, urlscan.io, PhishTool, SEG | [13 — OSINT / phishing](SOC_Master_Notebook_Clean.md#13-threat-intelligence-and-soc-operations) | What is the source quality, data-sharing risk, and corroborating local evidence? |
| `-enc`, `IEX`, `-nop`, `-w hidden`, `regsvr32`, `certutil`, BITS | [14 — Commands / LOLBins](SOC_Master_Notebook_Clean.md#14-command-line-detection-and-investigation-library) | What does the whole command decode to, and did the action succeed? |
| Overconfident technical explanation | [15 — Common misconceptions](SOC_Master_Notebook_Clean.md#15-common-investigative-misconceptions) | Is the claim OS-/version-/event-specific, or only a hypothesis? |

## Three fast triage pivots

- **Suspicious `w3wp.exe → powershell.exe`:** correlate IIS request → app-pool process → command arguments → file writes/outbound traffic. High priority does not equal confirmed web shell.
- **Successful login after repeated MFA denies:** examine prompts, approved device, session/token issuance, source IP/ASN and the *actual APIs/data accessed*; do not equate an accepted prompt with genuine user consent.
- **Noisy OWA/Autodiscover or horizontal SYN scan:** validate a change ticket and test owner, but also investigate authentication, exploit attempts and affected assets; an HTTP 401/404 or firewall drop is not a universal reason to close the alert.

## Reusable mini-investigation checklist

1. **Trigger:** What alert, event, query, or hypothesis prompted the investigation?
2. **Entity:** Who/what acted? User, host, parent process, command line, token, role.
3. **Action:** What changed or executed? Registry, service, task, network call, API request.
4. **Scope:** Local machine, another endpoint, domain controller, or cloud account?
5. **Outcome:** Did authentication, execution, data transfer, or persistence actually succeed?
6. **Evidence:** Collect independent evidence; do not infer credential theft from a lone network connection or malware family from a filename.
7. **Investigation summary:** *Observation → relevant Windows/cloud mechanism → possible benign and malicious explanations → confirming telemetry → likely ATT&CK tactic (if applicable).* 

## Important distinctions

- **Authentication ≠ interactive session ≠ remote execution.** Type 3 is a network logon, not an RDP desktop.
- **DLL ≠ COM object.** A DLL can host COM components, but many DLLs are ordinary libraries.
- **IOC ≠ IOA.** An IOC is a known observable artifact; an IOA focuses on attacker behavior/sequence.
- **LSASS ≠ DPAPI.** One is an authentication/security process, the other a data-protection mechanism; the two can interact.
- **Tool use ≠ malicious intent.** Admin utilities and red-team tools require host/user/timing/context. Pentest/scanner fingerprints are not proof of authorization.
- **Static ≠ dynamic analysis.** Static inspection does not execute the sample; dynamic analysis observes running behavior in a controlled environment.
- **Token type matters.** OAuth bearer/refresh/session tokens are different from local Windows process access tokens.
- **A suspicious process pair ≠ automatic compromise.** Validate parent start time, command, service/app pool, digital signature and outcomes.
- **A 200 status / successful handshake ≠ confirmed exploit.** Conversely, a 401/404 or blocked packet does not always eliminate incident risk.
- **Public OSINT submissions can expose data.** Sanitize URLs, emails, attachments and credentials before using third-party services.

See Chapter 15 for common interpretation pitfalls and technical distinctions.

## Investigation guides

| If you observe... | Use |
|---|---|
| A suspicious parent → child process or LOLBin | [Windows process and persistence behavior](../analysis/windows-process-baselines.md) |
| NTLM, Kerberos, MFA, cloud token or unfamiliar session | [Identity and cloud behavior](../analysis/identity-and-cloud-behavior.md) |
| DNS beacon hypothesis, scanner, email click or ransomware precursor | [Network, email and ransomware behavior](../analysis/network-email-and-ransomware.md) |
| An event ID and questions about its source | [Event / telemetry matrix](../references/event-telemetry-matrix.md) |
| Need to decide severity vs expected baseline | [Cross-domain behavior decision matrix](../analysis/normal-vs-suspicious-behavior.md) |
| Need a repeatable investigative procedure | [SOC playbook index](../playbooks/README.md) |

**Reminder:** Draft event/signature correlation ≠ production-ready detection; confirm table availability, schemas, collection policy and benign context.

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
