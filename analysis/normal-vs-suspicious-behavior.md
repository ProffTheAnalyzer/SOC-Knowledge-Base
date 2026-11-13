# Normal vs. suspicious behavior — SOC analyst decision matrix

> **Purpose:** Compare Windows, credential, persistence, cloud, phishing, scanning and ransomware observations. **Not a signature catalog or proof of compromise.** Updated 2026-10-21.

**First question:** What *should* this specific host, account or workload be doing, and what evidence makes this event different?

| Observation | Often legitimate | Worth investigating when | First pivots |
|---|---|---|---|
| `winword.exe` → PowerShell | Approved Office integration or automation; less common but possible | Encoded script, remote payload, credential access, unusual document origin | Full command, document/email origin, signature, network |
| `w3wp.exe` → `cmd.exe` | Application code or deployment script invokes command processor | Unusual inbound web request, unexpected shell commands, file drops | IIS logs, child process, created files, connections |
| `services.exe` → executable/shell | Legitimate installed service or vendor tool | Unexpected service binary/path/account; execution around remote logon | System 7045, service config, binary origin, change request |
| `rundll32.exe` execution | Normal Windows or application DLL invocation | DLL in user-writable location, unexpected export, unusual child/network activity | Module path, signature/hash, initiating process |
| PowerShell `-EncodedCommand` | Management tool, installer, scheduled automation | Unrecognized user/device, suspicious decoded content, network retrieval | Decode offline, parent, 4104 if enabled, EDR |
| `HKCU\...\Run` changed | Approved app auto-start, installer | Newly added unknown binary or script, masquerading, unexpected user context | Value/data, process writer, first seen, signer |
| New scheduled task | Patch management, agent registration | Randomized name, hidden settings, script from temp/user path, new privileged run-as | Task XML, creator, trigger, payload, task execution |
| New service / remote service | Software deployment, backup, remote support | Unapproved high-privilege service or lateral execution chain | 7045, 4697 where collected, binary, logons |
| NTLM network logon | SMB, printing, legacy apps, management | First-seen privileged user from new host plus remote execution/share activity | 4624 Type 3, 4776, share/service/process timeline |
| 4624 Type 9 | `runas /netonly`, authorized tooling | Unexpected credential context plus suspicious remote access | Account, process, source host; **not proof of PtH** |
| Many 4769 service tickets | Enterprise applications and delegation | First-seen requester/SPN, high volume, suspicious encryption + lateral movement | DC Kerberos logs, service-account inventory |
| 4662 replication access | Directory synchronization or authorized DC/replication activity | Unexpected principal/source with replication rights on sensitive objects | DC SACL coverage, permissions GUIDs, replication actor |
| MFA denials then approval | Distracted user, app notification problems | Burst of prompts, unfamiliar device/IP and immediate high-value access | Sign-in/MFA audit, device, session, app activity |
| New sign-in IP / geography | VPN, NAT, travel, mobile IP, proxy | Unmanaged endpoint, impossible timing plus suspicious post-auth action | Entra logs, MFA, Conditional Access, token/session clues |
| App consent / permission update | Legitimate SaaS integration | New high-impact scope, unexpected initiator or unfamiliar publisher | Entra audit, consent details, service principal, grants |
| Repeated DNS lookups | Browsers, updaters, monitoring | Consistent periodicity + unusual domain and related payload behavior | DNS, device process, proxy, domain context |
| Port scan / HTTP probes | Security assessment, exposure scanner | Sensitive target impact, successful follow-on request, execution | Scope ticket, src IP, firewall, server access logs |
| New executable + mass file changes | Backup, indexing, deployment, bulk rename | Files become inaccessible, shadow copy changes, ransom notes | EDR, filesystem, backup events, source process |
| Large outbound data transfer | Backups, sync, authorized exports | Unusual source identity, archive staging, new external destination | Proxy/DLP/cloud logs, file creation, change records |

## Decision steps

1. **Confirm raw evidence.** Check full event, collection source, timestamp/time zone and affected asset.
2. **Baseline that entity.** Compare account privileges, host role, application owner, usual automation and planned changes.
3. **Correlate action sequence.** Map parent process → command → file/network/authentication changes within a time window.
4. **Look for competing explanations.** Document benign mechanisms, tooling gaps and gaps in collection.
5. **Scope and decide.** Determine breadth, criticality and confidence. Escalate on corroborated risk, not just a technique name.

<details>
<summary><strong>Example: an alert that looks severe but may be legitimate</strong></summary>

A service host launches PowerShell at 02:00 with an encoded command. This merits investigation, but a signed endpoint-management agent executing a documented maintenance job may explain it. Confirm parent process identity, decoded logic, management change ticket, expected destinations and comparable events on other managed hosts.

</details>

<details>
<summary><strong>Example: a stronger case for compromise</strong></summary>

An exposed IIS worker launches PowerShell seconds after a request to an obscure upload endpoint; the child process retrieves an executable, persists a scheduled task, and initiates unusual outbound access. The **linked chain** supports greater confidence than any one artifact.

</details>

## Related guides and references

[Master SOC reference](../notes/SOC_Master_Notebook_Clean.md). Also see [Windows](windows-process-baselines.md), [Identity](identity-and-cloud-behavior.md), [Network & ransomware](network-email-and-ransomware.md) and [telemetry prerequisites](../references/event-telemetry-matrix.md). See [Microsoft advanced hunting schemas](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-schema-tables) and [MITRE ATT&CK](https://attack.mitre.org/).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
