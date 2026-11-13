# Windows process, services and persistence: expected vs. suspicious

> **Focus:** Windows process trees, parent-child relationships, LOLBins, masquerading and registry persistence. Technical caution: process relationships are **context-sensitive**, not immutable rules.

## 1. Parent-child relationships

| Pattern | Expected explanations | Elevating evidence | Where to check |
|---|---|---|---|
| `services.exe` starts `svchost.exe` or registered binary | Service Control Manager handling a Windows/vendor service | New service, unsigned binary in user-writable path, unexpected remote admin source | Defender `DeviceProcessEvents`; 7045 in **System** log; service config |
| `explorer.exe` launches app | User interactive start | Rare unexpected child with payload/download or process tampering | Parent identity, command line, host/user |
| User app started *without* explorer parent | Scheduled task, RDS, installer, service or automation | Unexpected parent plus first-seen binary or suspicious behavior | Process creation telemetry and orchestration details |
| `winword.exe` / `excel.exe` → shell | Legitimate integration can exist | Phishing delivery, decoded malicious script, network retrieval | M365 email, 4688 or Sysmon 1, Defender XDR |
| Browser → script interpreter | Browser extension or supported helper, development tool | Unknown executable/script written and run after risky download | Browser/download data and process tree |
| `w3wp.exe` → shell | Application integration or deployment | Abnormal HTTP request + new file + child command | IIS logs; [IIS playbook](../playbooks/suspicious-iis-child-process.md) |
| `lsass.exe` apparent child / access | Logging artifact or unusual platform behavior; investigate carefully | Tampering, memory read from unfamiliar process, abnormal signed image | Event/EDR lineage, image path, credential protection status |

**Process myths to avoid:** `explorer.exe` is not the universal ancestor of user applications; a shell started by `services.exe` is not *automatically* malicious; parentage fields can be affected by telemetry quality, PPID spoofing, or unusual orchestration. PID alone is not globally unique or immutable over time.

## 2. Paths and masquerading

Expected `svchost.exe` and `lsass.exe` generally reside in Windows system directories on standard installations. A similar name executed from `C:\Users\Public\` or `C:\ProgramData\` merits scrutiny. Validate the **exact path, file signature, hash, process instance, OS build and command line**. A valid signature establishes signer information; it does not guarantee benign runtime behavior.

## 3. Living-off-the-land executables

| Utility | Authorized use | Higher-risk context |
|---|---|---|
| `powershell.exe` / `pwsh.exe` | Administration / automation | Encoded script, untrusted retrieval, unexpected privileged account |
| `rundll32.exe` | DLL loading | Unusual export/arguments or DLL stored in writable path |
| `regsvr32.exe` | COM/DLL registration | Remote scriptlet execution or unapproved persistence |
| `certutil.exe` | Certificate/PKI maintenance | File fetch/staging outside expected workflow |
| `bitsadmin.exe` | Background transfers | Remote download followed by execution/persistence |
| `schtasks.exe` | System administration | Newly created privileged task running unknown binary |

A useful rule connects command-line semantics to *who ran it*, *where the content came from*, and *what happened next*.

## 4. Persistence artifacts

| Artifact | Legitimate changes | Suspicious change pattern | Verify |
|---|---|---|---|
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | Per-user app auto-start | First-seen script from temp/downloads or unfamiliar profile | Registry value/data, writing process, install timeline |
| `HKLM\...\Run` | System-wide startup registration | Unknown privileged startup referencing nonstandard executable | Change owner, path, signature, full permissions |
| Scheduled task | Agent maintenance, updater | Hidden trigger, obfuscated action, unknown service identity | Task XML, Security 4698 if audited, endpoint task telemetry |
| Windows service | New software / remote support | Unexpected account, unusual executable path, lateral execution | System 7045, service config, initiating host |
| WMI persistence | Management tools | New permanent event subscription from unknown author | WMI activity logs and correlated process execution |

## 5. Triage checklist

1. Pin event times and exact process instances; `ProcessUniqueId`/parent unique ID in supported Defender telemetry can avoid PID-reuse mistakes.
2. Gather full command line, initiating user, token/integrity, binaries and signers.
3. Find process-related file, registry, network and logon events.
4. Ask whether the host role supports the operation.
5. Record evidence that changes the confidence level and what remains unknown.

## Official reference
[Defender XDR process event schema](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-deviceprocessevents-table), [Defender XDR hunting best practices](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-best-practices), [Windows logon auditing](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
