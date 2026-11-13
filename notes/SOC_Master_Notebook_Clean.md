# SOC & Cybersecurity — Master Reference Notebook

A practical reference for SOC investigations, threat hunting and cybersecurity fundamentals. Navigate by observation, process, event ID, artifact or technology. Each topic connects the underlying mechanism with investigative context, benign explanations and corroborating evidence.

[Library home](../README.md) · [Quick pivot sheet](SOC_Quick_Pivot_Clean.md) · [Behavior guides](../analysis/README.md) · [Investigation playbooks](../playbooks/README.md) · [Event telemetry matrix](../references/event-telemetry-matrix.md)

## Quick pivot — What did you just observe?

| Observation / question | Go to |
|---|---|
| A changed `HKCU` / `HKLM` key, `Run`, `Winlogon`, IFEO, `exefile` | [02. Registry, persistence, and execution hijacking](#02-registry-persistence-and-execution-hijacking) |
| `System` / PID 4, `smss.exe`, `services.exe`, `wininit.exe`, `lsass.exe` core process baseline | [01. Windows fundamentals and architecture](#01-windows-fundamentals-and-architecture) |
| A suspicious DLL, process, service, COM object, or Windows path | [01. Windows fundamentals and architecture](#01-windows-fundamentals-and-architecture) |
| OAuth access/refresh token, session cookie, device ID or anomalous token refresh | [03. Windows authentication, credentials, and Active Directory](#03-windows-authentication-credentials-and-active-directory) |
| LSASS access, `0x10`, WDigest, Mimikatz, DPAPI, DCSync, `krbtgt` | [03. Windows authentication, credentials, and Active Directory](#03-windows-authentication-credentials-and-active-directory) |
| `svcctl`, `srvsvc`, `ADMIN$`, `IPC$`, SMB, WMI, WinRM | [04. Remote administration and lateral movement](#04-remote-administration-and-lateral-movement) |
| Office/browser/IIS parent-child chain, `w3wp.exe` shell, short process lifespan | [05. Telemetry, event logs, and host investigation](#05-telemetry-event-logs-and-host-investigation) |
| Event 4624 Type 3, 4648, 4663, 4688, 4769, Prefetch, BAM | [05. Telemetry, event logs, and host investigation](#05-telemetry-event-logs-and-host-investigation) |
| 8.3 filenames, `.com`, packed files, AMSI, timestamps, `/headless` | [06. Defense evasion, anti-forensics, and privilege escalation](#06-defense-evasion-anti-forensics-and-privilege-escalation) |
| Cobalt Strike, Sliver, Neshta, Emotet, `svchost.com`/OneDrive | [07. Malware families, beacons, and incident case studies](#07-malware-families-beacons-and-incident-case-studies) |
| DNS tunneling, JA3, HTTP localhost, SOCKS, rclone, Pastebin | [08. Networking, C2, tunneling, and exfiltration](#08-networking-c2-tunneling-and-exfiltration) |
| Nessus/OpenVAS scan, port sweeps, OWA/Exchange probes, Autodiscover | [09. Reconnaissance and directory discovery](#09-reconnaissance-and-directory-discovery) |
| `nltest`, `domain.msc`, domain trust, remote share discovery | [09. Reconnaissance and directory discovery](#09-reconnaissance-and-directory-discovery) |
| MFA push bombing, AiTM vs. MFA fatigue, unusual API use following a sign-in | [10. Cloud security, identity, and APIs](#10-cloud-security-identity-and-apis) |
| AWS `AssumeRole`, Azure roles, Microsoft Graph, metadata API | [10. Cloud security, identity, and APIs](#10-cloud-security-identity-and-apis) |
| npm `postinstall`, `.env`, Axios, WordPress, Log4Shell | [11. Web applications and software supply chain](#11-web-applications-and-software-supply-chain) |
| PE/PDF files, `imphash`, YARA, static/dynamic malware testing | [12. Malware analysis and reverse engineering](#12-malware-analysis-and-reverse-engineering) |
| AbuseIPDB, PhishTank, urlscan.io, dnstwist, email header triage, SEG | [13. Threat intelligence and SOC operations](#13-threat-intelligence-and-soc-operations) |
| MISP, VirusTotal, abuse.ch, ISAC, SOC-CMM, Shodan | [13. Threat intelligence and SOC operations](#13-threat-intelligence-and-soc-operations) |
| `-EncodedCommand`, `-w hidden`, `-noni`, `IEX`, Base64 | [14. Command-line detection and investigation library](#14-command-line-detection-and-investigation-library) |
| An unfamiliar command line or LOLBin you need to recognize | [14. Command-line detection and investigation library](#14-command-line-detection-and-investigation-library) |
| A claim that sounds absolute or incident-specific | [15. Common investigative misconceptions](#15-common-investigative-misconceptions) |

## Chapters

- [01. Windows fundamentals and architecture](#01-windows-fundamentals-and-architecture) — Understand Windows files, processes, executables, runtime components, and how the operating system works.
- [02. Registry, persistence, and execution hijacking](#02-registry-persistence-and-execution-hijacking) — Investigate auto-start locations, file associations, COM hijacks, Winlogon, services, and UAC-related registry abuse.
- [03. Windows authentication, credentials, and Active Directory](#03-windows-authentication-credentials-and-active-directory) — Investigate LSASS/DPAPI, Kerberos, SAM, credential dumping, domain accounts, and identity abuse.
- [04. Remote administration and lateral movement](#04-remote-administration-and-lateral-movement) — Pivot on SMB, RPC, shares, named pipes, WMI, WinRM, remote services, RDP, and movement between hosts.
- [05. Telemetry, event logs, and host investigation](#05-telemetry-event-logs-and-host-investigation) — Interpret process trees, logon types, events, log fields, execution artifacts, and detection logic.
- [06. Defense evasion, anti-forensics, and privilege escalation](#06-defense-evasion-anti-forensics-and-privilege-escalation) — Find suspicious staging paths, packing, timestomping, security-tool interference, execution bypasses, and elevation.
- [07. Malware families, beacons, and incident case studies](#07-malware-families-beacons-and-incident-case-studies) — Recognize malware families/frameworks and revisit observations that are incident-specific rather than universally diagnostic.
- [08. Networking, C2, tunneling, and exfiltration](#08-networking-c2-tunneling-and-exfiltration) — Follow local or remote command channels, DNS/TLS indicators, cloud synchronization, network tunnels, and egress.
- [09. Reconnaissance and directory discovery](#09-reconnaissance-and-directory-discovery) — Investigate host/network enumeration, group discovery, identity exploration, shares, and domain-trust mapping.
- [10. Cloud security, identity, and APIs](#10-cloud-security-identity-and-apis) — Work with AWS IAM, Azure/Entra, tokens, API activity, storage access, and cloud metadata.
- [11. Web applications and software supply chain](#11-web-applications-and-software-supply-chain) — Review Node.js/npm, developer secrets, WordPress, browser/web concepts, and Log4j-related risks.
- [12. Malware analysis and reverse engineering](#12-malware-analysis-and-reverse-engineering) — Approach static and dynamic malware analysis, PE/PDF artifacts, fingerprints, YARA, and analysis tools.
- [13. Threat intelligence and SOC operations](#13-threat-intelligence-and-soc-operations) — Find intelligence sources, exposure-discovery tools, maturity models, and operational frameworks.
- [14. Command-line detection and investigation library](#14-command-line-detection-and-investigation-library) — Look up commands grouped by attacker objective; examples are for recognition and defensive analysis.
- [15. Common investigative misconceptions](#15-common-investigative-misconceptions) — Separate useful hypotheses from oversimplified rules or version-dependent claims.

---

## 01. Windows fundamentals and architecture

*When to pivot here:* Understand Windows files, processes, executables, runtime components, and how the operating system works.

**Conceptual model:** Windows uses processes/services for execution, the registry for configuration, DLLs for libraries, and COM as a component-activation/interface mechanism. Learn which parts are normal before calling them suspicious.

### 01.1 Registry and COM vocabulary

<details>
<summary>Full details</summary>

The Windows Registry stores configuration in hives, keys and values. HKLM is machine-wide; HKCU represents the current user; HKCR merges class registrations. Record the key, value name, type and data. COM (Component Object Model) lets applications instantiate registered components; a CLSID identifies a class. DLLs may implement COM components, but not every DLL is a COM object. Verify registration scope and actual server path.

</details>


### 01.2 Windows files and libraries

<details>
<summary>Full details</summary>

Windows executables, DLLs, scripts, installers and shortcuts use different execution mechanisms. Inspect actual file type, PE headers, architecture, signer, hash, origin and command line. On 64-bit Windows, System32 generally contains native 64-bit binaries and SysWOW64 32-bit components; filesystem redirection affects some processes. Imports, sections and entry points provide clues, not definitive malware classification. Filename extensions do not establish the underlying file format.

</details>


### 01.3 Windows services and managers

<details>
<summary>Full details</summary>

The Service Control Manager (`services.exe`) registers, starts and controls services. `svchost.exe` hosts services; others have standalone executables. Inspect service name, ImagePath, arguments, start type, account, binary origin and signature. System 7045 or Security 4697 may record service installation. A service can support maintenance, persistence or remote execution. Session 0 is non-interactive, not automatically SYSTEM.

</details>


### 01.4 Volume snapshots and Windows tools

<details>
<summary>Full details</summary>

Volume Shadow Copy Service (VSS) coordinates point-in-time volume copies. `vssadmin.exe` and backup agents can legitimately manage snapshots. Unexpected deletion or disruption can precede ransomware; verify the actual change, actor, affected volumes, backup product and authorization.

</details>


### 01.5 WebDAV, DLLs, and remote file interfaces

<details>
<summary>Full details</summary>

WebDAV extends HTTP for remote file management. Windows WebClient and related components may access compatible resources. Distinguish WebDAV from SMB and ordinary HTTP. A DLL loaded during remote access can be legitimate; establish destination, authentication, requesting process, file and resulting operation.

</details>


### 01.6 Local administration consoles and operating-system components

<details>
<summary>Full details</summary>

Administrative consoles such as `mmc.exe` and .msc snap-ins expose service, domain and event management. `w3wp.exe` hosts IIS application pools; `wmiprvse.exe` hosts WMI providers. Identify the relevant snap-in, pool, provider, identity and request before judging process behavior.

</details>


### 01.7 Windows startup and core-process relationship baseline

The process tree below illustrates typical Windows ancestry, not a hardcoded tree that every Windows build or session must reproduce.

```text
System (commonly PID 4; kernel process context)
└── smss.exe (Session Manager; session-specific instances may appear)
    ├── csrss.exe (Client/Server Runtime; per-session)
    ├── wininit.exe (initialization for Session 0)
    │   ├── services.exe (Service Control Manager)
    │   │   ├── svchost.exe (hosts groups of services)
    │   │   └── other standalone service executables
    │   └── lsass.exe (local security/authentication)
    └── winlogon.exe (interactive session logon)
        └── userinit.exe (may initiate the user shell)
            └── explorer.exe (common interactive shell)
```

| Process / component | Expected function | Useful investigation pivot |
|---|---|---|
| **System / PID 4** | Common representation of the kernel's System process, normally present from startup; `ntoskrnl.exe` is the kernel image, not necessarily a path shown as the System process's executable in every tool. | Verify event provider, PID, boot time, image metadata, and whether a process is merely *named* `System`; do not equate a lookalike name with kernel identity. |
| **`smss.exe` / `csrss.exe`** | Session setup and client/server runtime; multiple session-related instances can be normal. | Compare session, path, signer, process start, and ancestors. |
| **`wininit.exe`** | Helps initialize essential system services in Session 0. | Investigate unusual ancestry or image path rather than a name match alone. |
| **`services.exe`** | Service Control Manager: manages service registrations and starts service hosts/standalone service executables (see **01.3**). | A shell or script child can be suspicious, but may also arise from a legitimate service wrapper or maintenance script. Find the service name, binary path, and service-creation/change events. |
| **`lsass.exe`** | Security policy, authentication, and token-related functions (see **03.1**). | An apparent child is unusual and merits priority review; it is **not** by itself proof of Mimikatz, injection, or dumping. Validate the telemetry and related access/behavior. |
| **`winlogon.exe` / `userinit.exe` / `explorer.exe`** | Coordinate interactive user logon and commonly launch the user shell. | User applications may also start under a terminal, IDE, automation agent, task scheduler, service, or another shell. Non-Explorer ancestry is a context clue, not a verdict. |

**Baseline validation recipe:** Check the exact image path and signature → confirm parent PID *and parent start time* → identify session, logon ID, user and integrity level → compare with the same host role/OS baseline → corroborate with process/network/service/registry telemetry. System process names and parentage alone do not establish compromise.

---

## 02. Registry, persistence, and execution hijacking

*When to pivot here:* Investigate auto-start locations, file associations, COM hijacks, Winlogon, services, and UAC-related registry abuse.

**Investigative question:** Did a configuration change create automatic execution, redirect file launches, or replace a legitimate component? Correlate *which key/value changed*, *which process changed it*, and *what subsequently launched*.

### 02.1 COM registration and COM hijacking

<details>
<summary>Full details</summary>

COM registrations can point to DLLs (`InprocServer32`) or executable servers (`LocalServer32`). Unexpected per-user registrations may override machine registrations in applicable activation contexts. Check CLSID, registration scope, old/new component path, writer, signature and evidence of actual activation.

</details>


### 02.2 Auto-start and registry persistence overview

<details>
<summary>Full details</summary>

Persistence-capable mechanisms include Run/RunOnce values under `Software\Microsoft\Windows\CurrentVersion`, Startup folders, Winlogon Shell/Userinit, services, scheduled tasks and permanent WMI subscriptions. Review the value or trigger, payload, creator, privileges, scope and actual execution. A startup entry is not automatically malicious.

</details>


### 02.3 Common registry abuse paths

<details>
<summary>Full details</summary>

Investigate Image File Execution Options debugger settings, COM registrations, service ImagePath, Winlogon and LSA authentication-package configuration. An unexpected `exefile\shell\open\command` handler may redirect shell-mediated launches, but not all executable launch mechanisms. Correlate the registry writer and before/after values with process execution. Sysmon 12–14 and Defender registry telemetry can help where collected.

</details>


### 02.4 SAM registry structures

<details>
<summary>Full details</summary>

SAM stores local account and credential-related information; SYSTEM and SECURITY contain related configuration and security secrets. Investigate unexpected access, export, privileged activity and file staging. Distinguish approved backups from unauthorized extraction; a hive read alone does not establish credential recovery.

</details>


---

## 03. Windows authentication, credentials, and Active Directory

*When to pivot here:* Investigate LSASS/DPAPI, Kerberos, SAM, credential dumping, domain accounts, and identity abuse.

**Investigative question:** Who authenticated, what credentials or secrets were accessed, and what protections applied? Track identity, privilege, host role, process access, and relevant event logs.

### 03.1 LSASS, handles, memory access, and authentication

<details>
<summary>Full details</summary>

LSASS handles authentication, local security policy and credential-related functions. Investigate unexpected memory access, dumps, handles, file writes and security-package changes. Access rights such as 0x10 (process VM read) describe rights, not proof that secrets were retrieved. Sysmon 10 and EDR process-access records can help. WDigest plaintext caching depends on version/configuration, including UseLogonCredential. DPAPI protects application and user/machine secrets with key material; recoverability depends on key type, user state and protections. Credential Guard and protected-process settings affect access.

</details>


Kerberos uses TGTs and service tickets; NTLM uses challenge-response authentication. Event 4769 and RC4-HMAC type 0x17 can support a Kerberoasting hypothesis but require service-account, encryption and client baselines. Type 9 is NewCredentials, not a standalone pass-the-hash or ticket-replay signature. `cmdkey.exe /list` lists stored credential entries, not plaintext passwords. Correlate actor/target process, granted access, artifacts and downstream authentication.


### 03.2 DCSync and suspicious account usage

<details>
<summary>Full details</summary>

DCSync abuses directory replication permissions to request credential-related directory data. Domain controllers and authorized synchronization services can legitimately replicate. Review source, requesting principal, domain-controller role, permissions and replication-rights GUIDs in Security 4662 where audit policy and SACL coverage permit. Investigate directory permission changes and downstream privileged access.

</details>


### 03.3 OAuth/session tokens and token-theft hunting

Cloud/application tokens are distinct from a **Windows process access token**.

| Credential concept | What it does | SOC relevance |
|---|---|---|
| **OAuth/access token** | Often a short-lived token authorizing access to a particular API or resource; exact lifetime and controls vary. | A stolen usable token may grant API access without a new interactive password or MFA prompt. |
| **Refresh token** | Can request a new access token subject to service policies, revocation, device binding, and other controls. | Unusual issuance/refresh behavior can extend access after the initial sign-in and sometimes after a password change, depending on the identity system. |
| **Session cookie/token** | Maintains an application session; behavior and expiration depend on the service. | Session replay can resemble an authenticated user and may require device, network, and session-context detection. |
| **Windows process access token** | A local Windows security-context structure with user/group SIDs and privileges. | Relevant to process identity or impersonation; **not the same** as an OAuth access token. |

**Token-theft/contextual indicators:** a new device ID or unfamiliar subnet/ASN; an apparently valid sign-in without the normal interactive challenge; geographically implausible simultaneous use; unusually frequent refresh activity without corresponding user/app activity; and high-value administrative API access (Microsoft Graph, AWS STS, Google Cloud APIs) shortly after token issuance. Each observation has possible benign explanations such as proxies, mobile networks, corporate VPNs, automation, or legitimate API clients.

**Investigation pivot:** Correlate token/session IDs where available, client/app ID, user, source IP/ASN, device compliance, MFA/Conditional Access outcome, issued-at and refresh events, API scopes, grant type, and actual actions/targets. Distinguish *valid authentication* from *authorized activity*. For cloud audit APIs and account impacts see **10.1–10.2**.

---

## 04. Remote administration and lateral movement

*When to pivot here:* Pivot on SMB, RPC, shares, named pipes, WMI, WinRM, remote services, RDP, and movement between hosts.

**Investigative question:** How did data or code move to another host—SMB shares, RPC/service control, WMI, WinRM, RDP, or named pipes? Authentication is not the same thing as a desktop logon.

### 04.1 RPC and remote service protocols

<details>
<summary>Full details</summary>

RPC allows clients to invoke services on other processes or hosts. SMB named pipes can carry RPC interfaces such as `svcctl` (service control) and `srvsvc` (server/share management). Pipe or RPC connectivity is not proof of remote execution: identify interface, operation, identity and resulting service/process changes.

</details>


### 04.2 Administrative shares, SMB, and named pipes

<details>
<summary>Full details</summary>

SMB supports file sharing and named-pipe communication. ADMIN$, drive-letter administrative shares and IPC$ have distinct functions; IPC$ commonly supports named pipes. Examine 4624 logons, share events 5140/5145 where audited, actual file transfer, service/task creation and destination process execution. Port 445 connectivity is not proof of lateral execution.

</details>


### 04.3 Remote management and remote sessions

<details>
<summary>Full details</summary>

RDP provides remote desktop access; WinRM supports remote management and PowerShell remoting; WMI supports management operations including possible process creation. SMB/RPC supports transfer or service control. Security 4624 Type 3 means network logon; Type 10 means RemoteInteractive. RDP with NLA can involve multiple authentication stages. Correlate TerminalServices, WinRM/WMI, logon and process records to distinguish authentication, desktop session and execution.

</details>


### 04.4 Recognizing remote execution from parent processes

<details>
<summary>Full details</summary>

`services.exe` launching a new service binary, `wmiprvse.exe` spawning a process and `wsmprovhost.exe` hosting PowerShell remoting can suggest remote activity; all have legitimate administrative uses. Confirm origin, protocol, identity, target, exact process and actual operation. `wsmprovhost.exe -Embedding` is not evidence of Cobalt Strike or malicious remoting by itself.

</details>


---

## 05. Telemetry, event logs, and host investigation

*When to pivot here:* Interpret process trees, logon types, events, log fields, execution artifacts, and detection logic.

**Workflow:** Start from a timestamp/alert → pivot to process tree and initiating user → collect logon/event context → correlate related hosts, paths, network endpoints, and sensitive process access.

### 05.1 Prefetch, logs, and event-log tools

<details>
<summary>Full details</summary>

Prefetch can support execution analysis and reveal referenced files, subject to configuration. BAM records execution-related information for some users/programs, not every execution. Combine with process logs, Amcache and filesystem artifacts. Distinguish execution time from artifact write time. Event Viewer and `wevtutil.exe` manage logs; investigate unexpected clearing through Security 1102 and applicable channel records.

</details>


### 05.2 Network logons and authentication event interpretation

<details>
<summary>Full details</summary>

Security 4624/4625 record successful/failed logons; 4648 records explicit credential attempts; 4672 records special privileges assigned to a logon; 4768/4769 record Kerberos ticket requests; 4776 records credential validation. Interpret provider, logon type, account, package and source. Type 3 is not interactive RDP, and Type 9 may result from legitimate `runas /netonly`. Verify actual follow-on activity.

</details>


### 05.3 Unexpected access and suspicious process observations

<details>
<summary>Full details</summary>

Security 4688 records process creation when audited; command-line inclusion requires additional policy. PowerShell Operational 4104 records script blocks when captured. Security 4663 concerns audited object access; inspect object, rights and SACL. Sysmon 1/3/10/11/13 can provide process, network, access, file and registry context when configured. A command line can record an attempted action rather than success.

</details>


### 05.4 Logon correlation and session fields

<details>
<summary>Full details</summary>

Logon IDs help correlate events on the same host in the relevant boot context. PIDs are reused; prefer unique process identifiers and creation timestamps. Sysmon LogonGuid is useful but not universally one-to-one with Security 4624. Session 0 identifies a non-interactive session, not a particular privilege. Verify host, time and identity in joins.

</details>


### 05.5 Suspicious parent-child process relationships

Several **high-signal combinations** deserve attention, but labels such as “impossible,” “confirmed compromise,” or “must never” should not be used as automatic verdicts. Build an environment baseline and correlate command line, signer, network access, file writes, and user intent.

| Parent / lineage | Unexpected child / behavior to hunt | Possible explanation to validate |
|---|---|---|
| `winword.exe`, `excel.exe`, `outlook.exe` | `powershell.exe`, `cmd.exe`, `mshta.exe` | Document exploit or payload execution **vs.** legitimate automation, add-in, or workflow. |
| `chrome.exe`, `msedge.exe` | `powershell.exe`, `cmd.exe`, `rundll32.exe` | Browser abuse, extension, or exploit **vs.** helper app, deployment/integration, or approved software. |
| `w3wp.exe` | `cmd.exe`, `powershell.exe`, unusual child, or executable written to disk | Web shell/RCE **vs.** web application using subprocesses, compilation, or deployment tasks (see **05.6**). |
| `lsass.exe` | An apparent child process | Unusual and high priority; validate sensor/process data and actual behavioral evidence before claiming credential dumping. |
| `services.exe` | `cmd.exe`, `powershell.exe` or a newly installed service binary | Malicious service/persistence **vs.** normal administrative service orchestration. |
| `explorer.exe` | `rundll32.exe` | DLL loading via a system utility; often entirely legitimate, examine DLL path/exports/arguments. |

**What to collect:** process GUID and PID, parent GUID and start time (protect against PID reuse), full command line, executable path/hash/signer, integrity level, account and logon session, initiating file/URL, nearby scheduled-task or service events, child descendants, and outcomes. Process names establish *context*; **combined events and command-line arguments** help establish *purpose and impact*.

### 05.6 IIS / `w3wp.exe` triage

**IIS (Internet Information Services)** receives HTTP(S) requests and routes them to sites/application pools. **`w3wp.exe`** is the IIS worker process hosting web application code; depending on configuration the application may invoke subprocesses, scripts, or external components. Its presence is already defined in **01.6**; this section focuses on investigation rather than repeating the definition.

**Detection chain:** incoming request (URL, method, status, user, client IP, headers) → application pool and `w3wp.exe` identity → unexpected child process and full arguments → new file or script creation → outbound communication, persistence, or privilege change. A `w3wp.exe → cmd.exe`/`powershell.exe` relationship, especially alongside suspicious upload or exploit requests, deserves urgent investigation but is **not independently a confirmed web shell**. A 200 HTTP status by itself is not evidence of successful code execution.

### 05.7 Time, privilege, masquerading, and beaconing anomalies

- **Ephemeral execution:** many short-lived processes (sometimes under a second), repeated spawning/termination, or fast directory/domain enumeration can indicate scripting, droppers, or scanning. Compare with build agents and routine administration.
- **Privilege transitions:** correlate an observed change from a lower-integrity user context to a service/SYSTEM/high-integrity context; examine `services.exe`, `schtasks.exe`, `psexecsvc.exe`, and `wmiprvse.exe` with account/service creation logs. A SYSTEM process does **not** prove an escalation occurred.
- **Process masquerading:** names such as `C:\Users\Public\svchost.exe` or `C:\ProgramData\lsass.exe` are higher-risk when they imitate system images from unusual paths. Verify the path, signed publisher, hash, file metadata, command line and launch chain. Capitalization alone is not a reliable indicator on normal case-insensitive Windows filesystems.
- **Temporal patterns:** off-hours bursts and repeated connections at regular intervals can suggest scheduled automation or command-and-control beaconing. Use jitter, destination, payload size, schedule and business context to distinguish them.

For filename/path deception and anti-forensics, cross-reference **06**; for network periodicity and exfiltration, **08**.

---

## 06. Defense evasion, anti-forensics, and privilege escalation

*When to pivot here:* Find suspicious staging paths, packing, timestomping, security-tool interference, execution bypasses, and elevation.

**Investigative question:** Was there behavior intended to hide files, evade policy, disable telemetry/protection, elevate execution, or alter timestamps? Establish expected administrative activity first.

### 06.1 Obfuscation, packaging, and staging

<details>
<summary>Full details</summary>

Obfuscation can hide scripts, content, filenames and paths using encoding, packing, archives, alternate extensions or 8.3 names. Legitimate software uses these mechanisms too. Decode for inspection, identify file format, trace launcher and subsequent activity. Timestomping hypotheses require comparison of independent timestamps and filesystem evidence.

</details>


### 06.2 Security product and notification interference

<details>
<summary>Full details</summary>

Review unexpected changes to antivirus exclusions, services, tamper protection and logging. Signed security-product reporting components are not inherently suspicious. AMSI allows supported applications to submit content for antimalware inspection. Establish actual protection changes, actor, impact and authorization before classifying evasion.

</details>


### 06.3 UAC and elevated execution

<details>
<summary>Full details</summary>

UAC mediates elevation requests for applicable administrative actions. PowerShell `Start-Process ... -Verb RunAs` can request elevation, not prove bypass or successful privilege escalation. Check consent, account, token integrity, related configuration and subsequent privileged actions.

</details>


### 06.4 Executable and scripting behavior

<details>
<summary>Full details</summary>

PowerShell, cmd and scripting hosts are dual-use. Review full arguments and script content; encoded commands, -NoProfile, hidden windows and non-interactive execution can support legitimate automation. Headless browsers may be test runners or crawlers, not evidence of injection or hollowing.

</details>


### 06.5 Hiding with metadata and filenames

<details>
<summary>Full details</summary>

Inspect file attributes, extensions, actual format, hashes and directory context for hidden or disguised files. NTFS metadata and alternate data streams have legitimate as well as malicious uses. Filename capitalization alone is not a reliable masquerading indicator on typical Windows filesystems.

</details>


---

## 07. Malware families, beacons, and incident case studies

*When to pivot here:* Recognize malware families/frameworks and revisit observations that are incident-specific rather than universally diagnostic.

**Study pattern:** Keep confirmed observations (path, parent/child, registry change, network connection, hash) separate from attribution or inferred intent (family, injection, covert proxy). The `svchost.com` case also connects to the registry hijacking material in **chapter 02**.

### 07.1 Emotet, Neshta, and file infectors

<details>
<summary>Full details</summary>

Emotet is historically associated with malicious document delivery, modular malware and additional payload distribution; Neshta is a Windows file infector associated with executable infection and file-association changes. Family context helps form hypotheses; attribution requires sample/behavior evidence and reputable reporting, not a filename.

</details>


### 07.2 Case: registry exefile open command and svchost.com

<details>
<summary>Full details</summary>

An unexpected `svchost.com` in an executable-file shell association is a useful example of execution-redirection and masquerading hypotheses. Verify registry before/after values, writer, file type/hash/signature, launch mechanism and subsequent processes. A chain involving OneDrive does not establish injection, proxying or Neshta without independent evidence. Not all process launches use the shell association.

</details>


### 07.3 Case: Logon-event interpretations and Outlook shortcut behavior

<details>
<summary>Full details</summary>

A network logon and modified Outlook shortcut raise distinct questions: who authenticated, was a desktop session established, and what changed the shortcut? Inspect link target, arguments, metadata, user, session records and subsequent execution. Type 3 or an unusual shortcut alone does not establish lateral movement or payload execution.

</details>


### 07.4 Cobalt Strike, Sliver, and beacon-specific notes

Cobalt Strike is a commercial adversary-emulation platform. Its Beacon payload receives tasks and returns results through command-and-control (C2) channels. Sliver is an open-source adversary-emulation and C2 framework. Both can be used for authorized security testing or abused during intrusions.

<details>
<summary>Full details</summary>

#### Cobalt Strike: named-pipe indicators

Named pipes let processes communicate locally or across hosts via SMB. Cobalt Strike has documented historical default pipe patterns that may appear in endpoint telemetry.

| Pattern | Associated behavior |
|---|---|
| `msagent_*` | SMB Beacon peer communication |
| `status_*` | Named-pipe payload staging |
| `postex_*` | Post-exploitation jobs (documented default from version 4.2) |
| `postex_ssh_*` | SSH session communication with Beacon |
| `MSSE-*-server` | Default Artifact Kit binaries in the documented implementation |

The wildcard represents a variable suffix. A pipe such as `\msagent_a1` could match the SMB Beacon pattern. Matching a pipe does not establish Cobalt Strike use; legitimate applications also use named pipes and operators can change the defaults.

**Other behavioral clues:** recurring HTTP(S) or DNS traffic, unusual process communication over SMB, or a process opening another process followed by suspicious memory, pipe or network activity. Sleep intervals and jitter are configurable; a fixed interval alone is not a reliable signature.

**Investigate:** Sysmon 17 (pipe created) and 18 (pipe connected), where configured, can identify the pipe and process. Correlate with process creation, Sysmon 10 process access, remote SMB activity, and EDR or memory evidence. Confirm whether the activity was authorized.

References: [Cobalt Strike named-pipe defaults](https://www.cobaltstrike.com/blog/learn-pipe-fitting-for-all-of-your-offense-projects) and [Sysmon event descriptions](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).

</details>


---

## 08. Networking, C2, tunneling, and exfiltration

*When to pivot here:* Follow local or remote command channels, DNS/TLS indicators, cloud synchronization, network tunnels, and egress.

**Network pivot:** Identify source/destination, protocol, authentication context, egress restrictions, periodicity, and whether traffic is external, internal, or loopback (`localhost`).

### 08.1 Tunnels and local/remote IPC

<details>
<summary>Full details</summary>

Tunneling encapsulates traffic in another transport; SOCKS proxies relay connections; reverse connections may permit inbound-style access through an outbound channel. Named pipes and loopback can connect local components. Examine listening/connecting processes, direction, destination and remote endpoint. 127.0.0.1 is local loopback, not proof of external C2.

</details>


### 08.2 DNS, TLS, HTTP, and traffic observations

<details>
<summary>Full details</summary>

DNS queries, TLS fingerprints such as JA3, and HTTP methods/paths/statuses can inform hypotheses. Fingerprints can be shared or modified. Compare periodicity, jitter, process, domain and byte counts with legitimate schedules. HTTP 200 does not prove successful exploitation; failed requests do not exclude attempted exploitation.

</details>


### 08.3 Cloud synchronization and file-transfer context

<details>
<summary>Full details</summary>

Cloud sync and transfer tools, including rclone, support legitimate backup and collaboration as well as unauthorized transfer. Verify file selection/staging, actor, process, destination, authorization and outbound byte counts. A tool's presence is not proof of exfiltration.

</details>


---

## 09. Reconnaissance and directory discovery

*When to pivot here:* Investigate host/network enumeration, group discovery, identity exploration, shares, and domain-trust mapping.

**Recon pivot:** Determine which objects were queried (host, users, groups, shares, DCs, trust links) and whether the querying account/process had a legitimate administrative reason. For detailed reconnaissance command samples, jump to **14.3 Reconnaissance commands**.

### 09.1 Domain trust and discovery tools

<details>
<summary>Full details</summary>

`nltest.exe` and domain-management consoles can query domain controllers and trust relationships. LDAP/directory searches can enumerate users, groups, hosts and SPNs. Examine querying identity, process, objects, volume, host role and subsequent activity. A trust query does not establish trust abuse.

</details>


### 09.2 Authorized testing/scanning vs. real intrusions

Penetration-test and scanner activity can resemble attacker behavior. **None of these distinctions is definitive.** Authorized red teams can be stealthy and create test persistence; real intruders can use noisy, unmodified scanners and stop at reconnaissance. The reliable distinction is **documented authorization, ownership, scope and the observed activity**, not tool fingerprints or noise level.

| Dimension | May resemble authorized test/scanner activity | Also possible in an intrusion — verify |
|---|---|---|
| **Network footprint** | Broad sequential port sweeps, including 80/443, 445, 3389, 5985, 1433. | Either targeted, low-volume probes or similarly noisy scans. Determine where traffic originated and who authorized it. |
| **AD/host enumeration** | Broad LDAP, SPN/TGS and share/host queries. | Targeted discovery of DCs or high-value systems **or** broad automated enumeration. |
| **Tool artifacts** | Nessus, OpenVAS, Nikto, sqlmap, `python-requests`, PowerView modules. | Original or modified tools, obfuscated scripts, custom binaries or renamed executables; a scanner User-Agent does not authenticate an authorized test. |
| **Authentication pattern** | Many failures across many hosts/accounts, sometimes stopping at confirmation. | Low-and-slow spraying, credential stuffing, successful follow-on logons, or the same noisy pattern. |
| **Kerberos behavior** | A temporary burst of 4769/TGS requests or SPN enumeration. | Repeated targeted service-ticket requests and possible later ticket abuse; neither pattern alone demonstrates cracking, Kerberoasting, or ticket replay. |
| **Persistence / cleanup** | Authorized exercises *may* prohibit persistence and include documented cleanup. | Intruders may install tasks, Run keys, subscriptions or accounts, but do not necessarily persist; authorized red teams may also be permitted to simulate it. |
| **Scope / evidence of approval** | Change ticket, signed rules of engagement, expected time/IP ranges, test owner and reporting. | Missing authorization, out-of-scope targets, unexplained pivots, or activity denied by the named owner. This is the **strongest discriminator**. |

**Practical decision path:** (1) obtain source host and observed targets; (2) consult change windows, vulnerability-management schedules and rules of engagement; (3) independently verify the sponsor/owner; (4) review actual authentication, exploits, code execution and side effects; (5) escalate according to asset criticality and incident-response policy if authorization or harmful impact is unclear. A “pentest-looking” signature is not permission to close the alert.

### 09.3 Scanning triage scenarios

| Scenario | Example signals | Checks and escalation decision |
|---|---|---|
| **A — Exchange/OWA web probing** | Requests for `/owa/auth/x.js`, `/ecp/proxyLogon.ecp`, `/aspnet_client/`; script/scanner User-Agent. | Examine response status **and** application/IIS logs, authentication, file writes, WAF/EDR, exploit sequence and `w3wp.exe` descendants. Scanning may remain reconnaissance; do not require a 200 response/web shell before escalating a credible exploit attempt. |
| **B — Horizontal network scanning** | A source sends many SYNs across broad subnets and ports 80, 443, 445, 3389, 5985 and 1433. | Confirm direction and source ownership, allow/deny decisions, completed handshakes, service responses and any subsequent authentication or commands. Blocked traffic can still warrant escalation, especially when launched from an internal compromised host. |
| **C — Autodiscover enumeration** | `/autodiscover/autodiscover.json` requests with 401/404 responses and generic automation User-Agents. | Correlate repeated routes, request parameters, identity sign-in events, password spray, related IPs/subnets, and successful follow-on requests. The HTTP status alone neither confirms credential harvesting nor proves safety. |

**Remember:** Observation of a probe ≠ proof of compromise; lack of confirmed compromise ≠ reason to ignore an exposed asset, active exploit attempt or high-risk source. Follow local severity/response criteria.

---

## 10. Cloud security, identity, and APIs

*When to pivot here:* Work with AWS IAM, Azure/Entra, tokens, API activity, storage access, and cloud metadata.

**Cloud pivot:** Follow the principal, token/session, role/permission change, API action, source, and target resource. Privileged APIs and bulk storage activity can matter more than network-port scans.

### 10.1 Cloud identities, role changes, APIs, and storage

<details>
<summary>Full details</summary>

AWS IAM users/roles and STS sessions can be investigated through CloudTrail principal/session issuer, source, API operation and target. Azure RBAC and Entra roles require scope, assignment, actor and follow-on activity. Microsoft Graph activity requires app/service principal, consent, scopes and resource context. Review object-storage permissions and access. The metadata address 169.254.169.254 does not automatically expose usable credentials: platform configuration and safeguards such as AWS IMDSv2 affect access.

</details>


### 10.2 MFA fatigue, token/session anomalies, and API follow-through

**MFA fatigue / push bombing:** bursts of repeated MFA prompts, denials or timeouts targeting one account, followed by an approval, new session and sensitive resource use from an unusual device/IP may suggest social engineering or unauthorized access. Check the actual authentication method, device registration and post-login behavior. MFA push bombing and **adversary-in-the-middle (AiTM)** phishing are **related but distinct** techniques; one does not automatically imply the other.

**Investigation sequence:** number and timing of MFA challenges → approvals/denials/timeouts → source network/device and Conditional Access outcome → token issuance or refresh → actual API calls and data accessed → user confirmation via a trusted channel. Correlate new device IDs, unfamiliar ASNs, impossible-travel flags, quiet-hours refresh activity, and access to privileged APIs. A valid token and successful MFA event do not establish legitimate user intent.

**High-value post-authentication API examples:** Microsoft Graph (directory/tenant resources), AWS Security Token Service (`AssumeRole` and session/role activity), and Google Cloud Resource Manager; inspect the principal, effective permissions, request target, timestamp and response/impact. For OAuth/session-token definitions and theft signals see **03.3**; for broader AWS/Entra and Microsoft Graph material see **10.1**.

---

## 11. Web applications and software supply chain

*When to pivot here:* Review Node.js/npm, developer secrets, WordPress, browser/web concepts, and Log4j-related risks.

**Supply-chain pivot:** Examine package provenance, lifecycle scripts, dependencies, environment variables/secrets, and outbound requests. Distinguish normal application behavior from malicious modification.

### 11.1 Browser and web basics

<details>
<summary>Full details</summary>

HTTP(S) requests contain methods, headers and bodies; user-agent strings are client claims and can be changed. Browser and server-side requests have different context. Inspect redirects, authentication, origin, requesting process and actions rather than relying on a user agent.

</details>


### 11.2 Node/npm dependencies and supply chain

<details>
<summary>Full details</summary>

Node.js runs JavaScript outside browsers; npm manages packages and dependencies. `package.json` defines dependencies/scripts, and lockfiles record resolved versions. Lifecycle hooks such as postinstall may execute during installation depending on configuration. Compromised dependencies may abuse hooks, access environment variables or contact remote destinations. Axios is a JavaScript HTTP client, not inherently suspicious. .env files may contain credentials; inspect package provenance, versions, lockfile changes, install-time processes, network and secret access.

</details>


### 11.3 Java Log4j and JNDI

Apache Log4j is a Java library used to record application activity, errors and other messages. **Log4Shell (CVE-2021-44228)** is a vulnerability in affected Log4j 2 core versions involving JNDI (Java Naming and Directory Interface) lookups.

<details>
<summary>Full details</summary>

#### How the vulnerability works

1. An attacker supplies a specially crafted string in input that an application may log (for example, an HTTP header).
2. A vulnerable Log4j implementation evaluates the JNDI lookup in that input rather than simply recording it as text.
3. Depending on the version, configuration and runtime, the lookup can contact an attacker-controlled service and potentially lead to arbitrary code execution.

Illustrative log artifacts (non-operational placeholders):

- `${jndi:ldap://example.invalid/a}`
- `${jndi:rmi://example.invalid/a}`

**SOC investigation:** Identify the application and actual `log4j-core` version, where the string appeared, whether it was logged and evaluated, and whether outbound DNS/LDAP/RMI activity followed. Inspect Java process descendants, unexpected file creation and other endpoint activity. The string alone shows that the input was observed, not that exploitation succeeded. An outbound lookup is stronger evidence of evaluation but does not independently establish code execution.

Reference: [Apache Log4j security advisories](https://logging.apache.org/security.html#CVE-2021-44228). Consult the advisory for current remediation appropriate to the relevant Java and Log4j versions.

</details>


---

## 12. Malware analysis and reverse engineering

*When to pivot here:* Approach static and dynamic malware analysis, PE/PDF artifacts, fingerprints, YARA, and analysis tools.

**Analysis sequence:** Preserve the original sample and metadata → inspect hashes/headers/imports and obvious strings → use safe isolated execution when appropriate → extract IOCs and hypotheses for hunting.

### 12.1 File analysis tools and fingerprints

<details>
<summary>Full details</summary>

Start with file size, hashes (SHA-256 for identification), actual type, headers, signature and origin. PE imphash summarizes imported functions and can aid clustering, but is not a unique family identifier. YARA matches rule patterns and conditions; evaluate false positives and scope. Preserve evidence.

</details>


### 12.2 Static malware analysis and document inspection

<details>
<summary>Full details</summary>

Static analysis examines a sample without running it. Inspect PE headers, sections, imports, strings, resources, embedded URLs, signatures and document/PDF structure. Packed or encrypted content can limit visibility. Preserve hashes and distinguish observed artifacts from inferred behavior. Decode for inspection, not execution.

</details>


### 12.3 Dynamic analysis

<details>
<summary>Full details</summary>

Dynamic analysis observes execution in an authorized, isolated environment. Capture process, registry, file, network and memory activity against a clean baseline. Sandbox results may miss conditional execution, dependencies or anti-analysis. Derive hunting hypotheses and IOCs from observed actions; keep unknown samples off production systems.

</details>


---

## 13. Threat intelligence and SOC operations

*When to pivot here:* Find intelligence sources, exposure-discovery tools, maturity models, and operational frameworks.

**SOC workflow:** Use threat intelligence to enrich local evidence; validate indicators against your own environment. Use maturity models to evaluate repeatable detection, triage, response, and improvement.

### 13.1 Threat intelligence sources

<details>
<summary>Full details</summary>

Threat intelligence provides indicator, campaign and behavior context. Examples include MITRE ATT&CK, vendor research, abuse.ch/URLhaus, VirusTotal and MISP-based sharing. Record source quality, recency, confidence and local corroboration. Reputation results alone do not prove a local incident.

</details>


### 13.2 Exposure discovery and SOC maturity

<details>
<summary>Full details</summary>

Shodan and exposure-search tools identify internet-visible services; results can be stale and ownership must be checked. Vulnerability assessment should have authorized scope. SOC maturity models such as SOC-CMM assess people, process, technology and repeatable outcomes rather than counting tools.

</details>


### 13.3 OSINT, reputation, file sandboxing, and URL investigation tools

These tools support common investigative tasks. Service features and availability can change; verify before operational use.

| Investigation task | Example tools | Why an analyst might use them |
|---|---|---|
| **Hash/file triage & sandbox behavior** | VirusTotal (**already in 13.1**), **Joe Sandbox** | Look for prior submissions, detections, metadata, and controlled behavioral-analysis reports. Third-party verdicts are enrichment, not proof. |
| **IP/domain reputation** | **AbuseIPDB**, **IPVoid**, **URLVoid** | Obtain reports/blacklist context about IPs and hostnames; inspect report quality and recency. |
| **URL and rendered page triage** | **urlscan.io**, **Wannabrowser**, **URL2PNG** | Review URL redirects, page renderings/screenshots or browser-emulation output without casually opening suspect links in an everyday browser. |
| **Known-bad URL / phishing feeds** | URLhaus (part of **abuse.ch**, already in 13.1), **Google Safe Browsing**, **PhishTank** | Compare URLs/domains with malware distribution, unsafe-browsing and community phishing reports. Verify freshness and false positives. |
| **Typosquatting / impersonation** | **dnstwist**, Unicode homograph/homoglyph analysis, **Punycode/IDN** inspection | Identify lookalike domains and brand-impersonation patterns; do not assume generated candidate domains are registered or malicious. |
| **Redirect expansion and de-obfuscation** | **unshorten.it**, Base64 / percent-URL / HTML entity decoders | Reveal hidden destinations or encoded artifacts while keeping the original sample for evidence. |
| **Email artifacts & headers** | **eioc.py**, **PhishTool** | Extract potential IOCs and inspect mail header/authentication and attachment context. |
| **Enterprise mail-security telemetry** | **Proofpoint**, **Mimecast**, **Barracuda** | Retrieve secure email gateway (SEG) results, quarantine/delivery decisions and related user/message metadata. |

**Data-handling caution:** Public scan/sandbox and URL-visualization services may expose submitted URLs, files, tokens or internal hostnames to a third party. Follow organization policy, sanitize secrets, use approved private submission features where appropriate, and avoid submitting sensitive attachments or live session-bearing links. Never execute an unknown attachment on a production endpoint.

### 13.4 Phishing investigation and encoding pivots

**Suggested analyst sequence:** preserve original email and headers → identify sender, reply-to, return-path and SPF/DKIM/DMARC results → extract links/attachments/IOCs → decode obfuscation **offline** where practical → inspect domain similarities and redirect chain using approved tooling → correlate SEG delivery/quarantine and click/endpoint telemetry → decide scope and affected users.

- **Lookalike domain:** compare registrable domain, Unicode/Punycode representation, certificate/registration context and historical infrastructure; the display label alone is not evidence of control by an attacker.
- **URL shortening:** expand and log each redirect hop without automatically browsing or authenticating to the destination.
- **Encoding:** Base64 decoding is **different** from percent-URL decoding and HTML-entity unescaping. A terminal example such as `echo '<encoded>' | base64 -d` decodes Base64 only; select the appropriate decoder for other encodings, and do not pipe decoded bytes to a shell.
- **Correlate evidence:** feed hits and tool scores prioritize leads; a robust disposition also needs actual delivery, clicks, downloads, credential submission or endpoint/network activity.

---

## 14. Command-line detection and investigation library

*When to pivot here:* Look up commands grouped by attacker objective; examples are for recognition and defensive analysis.

**Usage:** Search this chapter by executable name or tactic. These command patterns are for analyst recognition, not instructions to run on production systems.

### 14.1 Credential access and sensitive database artifacts

<details>
<summary>Full details</summary>

Credential-access pivots include unexpected LSASS memory access, dump files, SAM/SYSTEM/SECURITY hive exports, credential-manager enumeration and tool artifacts. `cmdkey.exe /list` lists credential entries, not plaintext secrets. Verify actor, privileges, actual access, staged files and downstream authentication.

</details>


### 14.2 Living-off-the-land tools and command patterns

<details>
<summary>Full details</summary>

Dual-use utilities include `rundll32.exe` (DLL exports), `regsvr32.exe` (COM registration), `certutil.exe` (certificate management), `mshta.exe` (HTML applications), `bitsadmin.exe` (background transfer) and `msiexec.exe` (installation). Review full arguments, content/source paths, signer, parent, account and subsequent actions.

</details>


### 14.3 Reconnaissance commands

<details>
<summary>Full details</summary>

Common discovery commands include `whoami /all`, `systeminfo`, `ipconfig /all`, `net user`, `net localgroup administrators`, `nltest /domain_trusts` and `tasklist`. These reveal identity, privileges, system/network configuration, groups, trusts and running processes. Investigate unusual clusters using actor, host role, authorization and follow-on activity.

</details>


### 14.4 Privilege-escalation command patterns

<details>
<summary>Full details</summary>

Elevation requests, privileged service/task creation, integrity-level transitions and impersonation are useful pivots. `Start-Process ... -Verb RunAs` requests elevation. Confirm resulting token, user, consent and actual execution before inferring escalation.

</details>


### 14.5 Persistence command patterns

<details>
<summary>Full details</summary>

`reg.exe`, `schtasks.exe` and `sc.exe` can modify persistence-capable artifacts. Capture the actual value/task/service, creator, executable, trigger, privilege and execution evidence. Check approved software and administrative activity.

</details>


### 14.6 Defense-evasion command patterns

<details>
<summary>Full details</summary>

Investigate unexpected security-service, exclusion, logging, file-attribute and timestamp changes. `wevtutil.exe`, `attrib.exe` and PowerShell have legitimate purposes. Establish actual change and security impact; attempted action is not necessarily success.

</details>


### 14.7 Remote-execution command patterns

<details>
<summary>Full details</summary>

WinRM, WMI, SMB, RDP and remote services enable remote administration or lateral movement. Relevant processes include `psexecsvc.exe`, `wmiprvse.exe` and `wsmprovhost.exe`. Identify origin, destination, identity, actual operation, resulting processes and artifacts.

</details>


### 14.8 Collection command patterns

<details>
<summary>Full details</summary>

Archive creation and file enumeration/copying may represent backup, deployment or collection. Common tools include PowerShell compression, `tar.exe`, 7-Zip and `robocopy.exe`. Identify selected files, archive path, data sensitivity, actor and subsequent access before classifying staging.

</details>


### 14.9 C2 command patterns

<details>
<summary>Full details</summary>

Unfamiliar processes making repeated network connections or unusual DNS/HTTP requests merit investigation. Compare destination, timing, jitter, process identity and related payload/tasking behavior with legitimate management and updater schedules.

</details>


### 14.10 Exfiltration command patterns

<details>
<summary>Full details</summary>

File-transfer clients, sync agents and scripts can move data legitimately. Review destination, identity, authorization, bytes, files staged and storage/proxy audit records. A connection or attempted upload does not prove successful exfiltration.

</details>


### 14.11 Command-line red flags and obfuscation cues

Use this **argument-level hunting index** alongside the dual-use executable examples in section 14.2.

| Signal | Typical meaning / investigative question |
|---|---|
| PowerShell `-EncodedCommand` / `-enc` | Encoded command input: what does it decode to, and which process/user supplied it? |
| `-NoProfile` / `-nop`, `-NonInteractive` / `-noni` | Reduced profile/interaction requirements: legitimate in automation; inspect parent, flags and script contents. |
| `-WindowStyle Hidden` / `-w hidden` | Suppresses a visible window: common to some deployment scripts and also malicious staging. |
| `IEX` / `Invoke-Expression` | Evaluates expression/script text: trace the data source and whether downloaded content is executed. |
| `[System.Convert]::FromBase64String(...)` | Decodes Base64 inside .NET/PowerShell: examine resultant bytes or strings in an isolated analysis context. |
| `DownloadString`, `Invoke-WebRequest`, `curl` | Acquires remote content: distinguish fetching a legitimate package from execution of attacker-controlled content. |
| Literal `http://`, `https://` or public IP in arguments | Potential network destination; validate actual connection and whether the address is benign, internal, or remote. |
| `certutil -urlcache -split -f`, BITS jobs, remote `regsvr32` scriptlets | Already documented with source commands in **14.2**; correlate download destination, child process and execution outcome. |

**Analyst principle:** an executable name or a flag is a **lead**, not the incident verdict. Preserve and safely decode the complete command; link it to execution, persistence and network telemetry.

---

## 15. Common investigative misconceptions

*When to pivot here:* Separate useful hypotheses from oversimplified rules or version-dependent claims.

These distinctions help prevent common interpretation errors. Confirm the applicable event provider, OS version and corroborating evidence.

### 15.1 Static analysis

The key distinction is that static analysis inspects without running the sample; dynamic analysis observes execution.

### 15.2 `.com` vs `.exe` and executable search

The path-resolution rules depend on the caller and Windows APIs. Executable resolution does not follow a universal `.com → .exe → .bat → .cmd` order. Likewise, changes to the `exefile` shell-open association are not a guarantee that every process launch uses that association.

### 15.3 `svchost.com` → OneDrive.exe

An unusual command chain can support a loader/infection hypothesis. It does not, by itself, prove process injection, traffic proxying, or Neshta attribution. Confirm with module/memory evidence, process behavior, and file analysis.

### 15.5 Network Logon Type 3 and NLA

Type 3 identifies a network logon, not an interactive desktop. RDP may involve multiple authentication phases, and NLA does not categorically eliminate authentication event logging.

### 15.6 `lsass.exe` / `svchost.exe` as initiating process

Their presence alone does not prove Pass-the-Hash, credential injection, a backdoor, or service persistence. Check event semantics, logon type, command line, token, host role, and correlated traffic.

### 15.7 Sysmon `LogonGuid` and session 0

`LogonGuid` is useful for event correlation but is not a universal one-to-one join with Windows event 4624. Session 0 is non-interactive; it does not automatically mean the process runs as SYSTEM.

### 15.8 `wsmprovhost.exe -Embedding`

Seeing this process/flag suggests a remoting/hosting context but does not independently establish a malicious operator, Cobalt Strike use, or a particular lateral-movement technique.

### 15.9 Kerberoasting and `0x17`

Kerberos Event ID 4769 and RC4-HMAC encryption type 0x17 can be hunting clues but are not standalone evidence of Kerberoasting. Baseline normal traffic and account/service configurations.

### 15.10 Event IDs and access masks

Windows Security and Sysmon events describe different observations. Check the provider and event fields before interpreting a match.

| Source / event | Meaning | Investigation pivot |
|---|---|---|
| Security **4663** | An audited access right was used on a file, registry or other object. | Review ObjectName, ObjectType, ProcessName, SubjectLogonId and AccessMask. Requires relevant audit policy and object SACL. |
| Security **4648** | A process attempted to log on using explicitly supplied credentials. | Compare the requesting identity, target account, process and target server; correlate with authentication outcomes and subsequent activity. |
| Security **4696** | A primary token was assigned to a process running with a non-current token. | Historical event, deprecated starting with Windows 7 and Server 2008 R2. For modern systems use available process, logon and elevation telemetry such as 4688 and 4624. |
| Security **4688** | A new process was created. | Inspect executable, creator, target identity and token elevation; command-line capture requires policy. |
| Sysmon **1** — Process creation | A new process started. | Inspect Image, CommandLine, ParentImage, ParentCommandLine, User, Hashes and ProcessGuid. Establish process lineage and execution context. |
| Sysmon **3** — Network connection | A process made or received a TCP/UDP connection. | Review Image, ProcessGuid, Initiated, source/destination IPs and ports. Correlate with process execution and possible C2. |
| Sysmon **7** — Image loaded | A module, such as a DLL, was loaded into a process. | Inspect Image, ImageLoaded, Hashes, Signed, Signature and SignatureStatus for unexpected modules, DLL side-loading or other suspicious loading behavior. |
| Sysmon **10** — Process access | One process opened another process. | Inspect SourceImage, TargetImage, GrantedAccess and process identity; useful for investigating LSASS access. |
| Sysmon **11** — File creation | A file was created or overwritten. | Check Image, ProcessGuid and TargetFilename for downloads, payload staging and changes to Startup folders. |
| Sysmon **13** — Registry value set | A registry value was set or modified. | Inspect Image, ProcessGuid, TargetObject and Details; examine Run/RunOnce values, service configuration and other persistence-capable locations. |
| Sysmon **17 / 18** — Named pipes | A named pipe was created (17) or connected (18). | Correlate PipeName and process identity with expected software, remote SMB activity and potential C2 or post-exploitation behavior. |
| Sysmon **22** — DNS query | A process made a DNS query, including failed or cached queries. | Review Image, ProcessGuid, QueryName, QueryStatus and QueryResults; investigate suspicious domains, beaconing and DNS tunneling hypotheses. |

**Sysmon collection:** On modern Windows, these events are recorded in `Microsoft-Windows-Sysmon/Operational`. Collection depends on Sysmon configuration and filtering. Network connection (3) and image load (7) are disabled by default; image-load telemetry can be especially noisy. Event 22 records a query, not proof that a connection to the resolved address occurred. Event 13 identifies a registry modification, but a matching value does not prove persistence or subsequent execution.

**Example investigation:** Start with Event 1 to identify a suspicious process. Pivot using its `ProcessGuid` to Event 7 for loaded DLLs, Event 22 for queried domains, Event 3 for network connections, and Event 13 for registry changes. Event 10 can reveal access to other processes, and Event 11 can reveal file creation. Establish which actions actually occurred and whether they share the same process or related process lineage. Events 17/18 can add named-pipe evidence when investigating Cobalt Strike-like behavior.

**4648 in practice:** A user logged in as `Analyst1` runs `runas /user:DOMAIN\Admin1 cmd.exe`. The process attempts a logon using explicitly supplied credentials for Admin1. Scheduled tasks and routine administration can also generate 4648. The event does **not** log the password, show that it crossed the network in plaintext, or prove the attempted logon succeeded. Correlate with 4624/4625 and subsequent activity on the relevant hosts.

**Explicit credentials versus cleartext:** Event 4624 Logon Type 8 (NetworkCleartext) means the user's password was supplied to the authentication package in unhashed form. This is distinct from 4648; it does not mean the password necessarily traversed the network in plaintext. Type 9 (NewCredentials) uses a cloned local token and alternate credentials for outbound connections; it is not proof of pass-the-hash.

**Access masks:** Interpret masks against the object type. For process handles, `0x10` is PROCESS_VM_READ, permission to read process memory. A Sysmon 10 record with this access right does not prove memory was read or credentials extracted. File and registry masks have different meanings.

References: [4663](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4663), [4648](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4648), [4696](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4696), [4624 logon types](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4624), [Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon). See also [event telemetry matrix](../references/event-telemetry-matrix.md).


### 15.11 Cloud metadata endpoint

`169.254.169.254` is commonly used for instance metadata, but metadata contents, credential exposure, authentication safeguards, and protections such as AWS IMDSv2 depend on platform and configuration. Access does not automatically yield credentials.

### 15.12 LSASS and DPAPI master keys

The categories “LSASS = memory secrets” and “DPAPI = disk secrets” are useful mental models, but exact secrets in memory and available decryption paths depend on OS version, user state, mitigations, and key type.

### 15.13 Headless Chrome

`--headless=new` and suppressed startup UI can be legitimate automation. These flags do not prove process hollowing or malware.

### 15.14 BAM and Prefetch

Execution artifacts have coverage and retention limitations; BAM does not literally record every binary that ever ran, and Prefetch availability depends on system configuration and usage.

### 15.15 Cobalt Strike “default” IOCs

Cobalt Strike Beacon is a payload used for post-exploitation and command-and-control. Historical default indicators include named-pipe patterns `msagent_*` (SMB Beacon), `status_*` (staging), `postex_*` (post-exploitation jobs) and `MSSE-*-server` (Artifact Kit).

These are hunting leads in Sysmon 17/18 or equivalent endpoint telemetry, not definitive signatures. Verify the process creating or connecting to the pipe, related memory/process activity and network communication. Defaults can be changed, and a generic port, user agent or periodic connection is not sufficient for attribution.

See [Cobalt Strike and Beacon investigation details](#074-cobalt-strike-sliver-and-beacon-specific-notes) for more examples and the vendor reference.


### 15.16 Default rules vs genuine maliciousness

Legitimate binaries (`regsvr32`, `rundll32`, `msiexec`, `csc`, `wevtutil`) and admin tools (`PsExec`, `NetExec`, `rclone`) are dual-use. Interpret them in context rather than treating a binary name alone as proof.

### 15.17 Log4Shell

Log4Shell (CVE-2021-44228) affects susceptible Log4j 2 core implementations. Log4j is a Java logging library; vulnerable JNDI lookup behavior can turn attacker-controlled logged input into a remote lookup and, under applicable conditions, code execution.

For example, a log containing `${jndi:ldap://example.invalid/a}` may indicate probing. Determine whether the affected application evaluates the string, connects to a remote lookup service, and subsequently executes unexpected code. Check the component version, application logs, network telemetry, Java child processes and file changes.

See [Java Log4j and JNDI](#113-java-log4j-and-jndi) for the mechanism, sample indicators and investigation workflow.


### 15.18 Logon Type 3 / lateral movement distinction

Remote file access, execution, and interactive sessions are different behaviors. SMB can support movement without a desktop session, but a Type 3 event by itself is not proof of successful remote execution.


### 15.19 Windows process ancestry and PID rules

A typical process tree is a simplified educational map. `System` typically appears as PID 4, but sensor representations, process parents and session-specific instances vary. Avoid treating `ntoskrnl.exe` as a universally reported `System` executable path, and verify process lineage with timestamps.

### 15.20 `services.exe`, `lsass.exe` and `explorer.exe` absolute rules

Shell/script children of `services.exe` or apparent `lsass.exe` child activity can be important alerts; neither proves compromise without corroboration. Ordinary applications need not ultimately descend from `explorer.exe`. Control-set references are not a general rule for detecting service abuse.

### 15.21 IIS worker process → shell / file write

`w3wp.exe` spawning a shell or writing an executable is high-priority evidence, **not** a stand-alone confirmation of a web shell or RCE. Inspect application logic, request logs, file creation, and downstream activity.

### 15.22 Browser/Office parent-child and “impossible” labels

An Office or browser process spawning a shell is worth reviewing but can be part of signed software, integrations, or legitimate automation. A browser child or `explorer.exe → rundll32.exe` alone is not a security incident.

### 15.23 Cloud access/refresh token behavior

A token may survive some password changes or interactive MFA steps, but revocation, conditional access, lifetime, audience, device-binding and provider rules differ. Distinguish OAuth access tokens from Windows process access tokens.

### 15.24 MFA fatigue vs. AiTM phishing

Push bombing and AiTM phishing are distinct techniques. A series of MFA denials followed by an approval strongly warrants investigation; confirming account takeover requires session and activity evidence.

### 15.25 Pentest versus intrusion “fingerprints”

Authorized testers can be quiet, persistent, or modified-tool users; intruders can be noisy and nonpersistent. Scope, authorized owners, rules of engagement and follow-on impact are decisive context; tool/User-Agent signatures are not proof of authorization.

### 15.26 Web-probing and port-scanning escalation thresholds

Do **not** require a 200 status, successful handshake or command execution as universal escalation thresholds: blocked scans from internal hosts, high-severity exploit attempts, and 401/404 probes can still require escalation under your IR policy.

### 15.27 Short process duration, process name casing, and time-of-day

Lifetimes below one second, off-hours bursts and periodic traffic are heuristic clues, not proofs of malware. Windows file-name case differences are ordinarily not reliable evidence of masquerading.

### 15.28 OSINT reputation and third-party submissions

Threat feeds, screenshot services and sandbox results may be stale or false positive. Uploads/submissions can disclose internal URLs, credentials or sensitive content; use approved workflows.

### 15.29 Base64, URL percent encoding, HTML entities

The terminal `base64 -d` example applies to Base64 and does not automatically decode URL percent escapes or HTML entities. Decode offline and never execute decoded strings merely to inspect them.

---

## Applying this reference

Treat indicators as investigative hypotheses. Confirm the event source, actual action, identity, process instance, time, scope and outcome. Use approved isolated environments for controlled testing.

See [technical references](../references/README.md) for vendor documentation and evidence validation.

## Practical SOC behavior guides

These companion guides translate technical concepts into investigation decisions.

<details>
<summary><strong>Normal vs suspicious — what makes an indicator meaningful?</strong></summary>

| Observation | Potentially authorized | Corroboration that raises concern |
|---|---|---|
| `w3wp.exe` → `powershell.exe` | Web-app integration or deployment | Relevant HTTP request + unexpected payload download/file creation |
| 4624 Type 3 with NTLM | SMB share, printing, service | Unknown privileged host + remote process/task/service |
| First-seen sign-in IP | VPN, travel, roaming | New unmanaged device + consent/mail/admin action |
| Scheduled task / new Run key | Updater, endpoint management | Unapproved script from writable path + outbound behavior |
| Large file changes | Backup, migration, bulk rename | Unauthorized mass modification + defense impairment |

See [complete behavior matrix](../analysis/normal-vs-suspicious-behavior.md).

</details>

<details>
<summary><strong>Investigate rather than assume — key corrections</strong></summary>

- Logon Type 9 means **NewCredentials**, not automatic pass-the-hash.
- `7045` is a **System** log event, rather than a Security log event.
- A process launched outside `explorer.exe` ancestry may be normal scheduled or managed execution.
- Neither HTTP 200 nor 404 alone determines exploitation success.
- Broad exclusions for machine accounts and management binaries can create detection blind spots.

See [field telemetry reference](../references/event-telemetry-matrix.md).

</details>

### Companion navigation

- [Windows processes and persistence](../analysis/windows-process-baselines.md)
- [Identity, MFA and cloud tokens](../analysis/identity-and-cloud-behavior.md)
- [Networking, phishing and ransomware behavior](../analysis/network-email-and-ransomware.md)
- [SOC investigation playbooks](../playbooks/README.md)
- [Event ID / telemetry quick matrix](../references/event-telemetry-matrix.md)

**Validation notice:** Practical guides include general analyst heuristics and Microsoft documentation links. They have not been run as detection tests against a tenant or endpoint lab.

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
