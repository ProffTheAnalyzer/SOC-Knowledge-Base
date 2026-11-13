# SOC telemetry quick matrix — what the log can and cannot prove

> **Last reviewed:** 2026-11-13. Logging depends on Windows auditing, sensor configuration, connector availability and licensing. Numbers below are event IDs *within their specified providers/logs*.

| Source / event | What it records | Good for | Common limitation |
|---|---|---|---|
| Windows **Security** 4624 | Successful logon | Source, identity, logon type | Legitimate logons are common; event alone cannot prove credential abuse |
| Windows **Security** 4625 | Failed logon | Authentication failures | Often mistyped passwords, apps, stale secrets |
| Windows **Security** 4648 | Explicit credential logon attempt | `runas`, credential usage | Not proof of pass-the-hash |
| Windows **Security** 4672 | Special privileges assigned | Admin/session investigation | Routine for authorized privileged activity |
| Windows **Security** 4688 | Process created | New process execution | Command line requires audit setting; parent context may be limited |
| Windows **Security** 4698 | Scheduled task created | Task persistence hypothesis | Requires relevant audit category |
| Windows **Security** 4697 | Service installed | Service-change review | Collection/configuration varies |
| Windows **System** 7045 | Service installed | New service installation | **Not** a Windows Security event; map into correct ingested table |
| Windows **Security** 4768 | Kerberos TGT request | Initial ticket activity | KDC/DC collection is necessary |
| Windows **Security** 4769 | Kerberos service ticket request | Service ticket patterns | Common in normal applications; fields vary by OS/version |
| Windows **Security** 4776 | NTLM credentials validated | NTLM anomalies | Doesn't distinguish stolen hash by itself |
| Windows **Security** 4662 | Object operation in directory | Directory replication hypotheses | SACL/permissions auditing and rights GUID parsing required |
| Windows **Security** 5136 | Directory object modified | AD permission/object changes | Depends on Directory Service Changes auditing |
| Windows **Security** 1102 | Security audit log cleared | Potential log tampering | Admin maintenance/recovery can also do this |
| PowerShell Operational 4104 | Script block content | Script content investigation | Requires appropriate configuration and capture; noise/sensitivity |
| Sysmon 1 | Process creation | Detailed process lineage | Sysmon must be installed/configured; policy affects records |
| Sysmon 3 | TCP/UDP network connection | Process-to-network correlation; source/destination and ports | Disabled by default; configuration matters |
| Sysmon 7 | Module or DLL loaded into a process | ImageLoaded, process identity, hashes and signature; suspicious DLL loading | Disabled by default; high event volume; a loaded DLL is not proof of injection |
| Sysmon 10 | Process access | Sensitive process access investigation | Legitimate security tooling creates access too |
| Sysmon 11 | File created or overwritten | New file/payload hints | Not every write or modification is recorded |
| Sysmon 13 | Registry value set | TargetObject, writer and value details; persistence-capable changes | Requires enabled Sysmon rules; value data visibility varies; no proof of later execution |
| Sysmon 17 / 18 | Named pipe created / connected | Pipe and process correlation; possible interprocess or C2 activity | Legitimate applications also use named pipes; configuration matters |
| Sysmon 22 | Process-associated DNS query, including failed or cached queries | Domain, query status/results and originating process | Does not establish a network connection; filtering and resolver behavior matter |
| Defender XDR `DeviceProcessEvents` | Process creation and context | Process command, lineage, identity | Onboarding/sensor/licensing/schema required |
| Defender XDR `DeviceNetworkEvents` | Network activity | Connections and initiating process | Correlation needs reliable process identity |
| Sentinel `SigninLogs` | Entra sign-in events | MFA/device/app context | Connector, access and retention required |
| Defender XDR `EmailEvents` / `UrlClickEvents` | Email delivery / Safe Links clicks | Phishing correlation | Defender for Office 365 and relevant coverage required |

## Investigation precautions

- Event ID **7045** belongs to **System**; do not run a `SecurityEvent | where EventID==7045` query without confirming a normalized custom ingestion.
- The Windows **4688** log is not the same as Defender `DeviceProcessEvents`, and Defender advanced hunting `Timestamp` differs from Sentinel `TimeGenerated`.
- For process joins, prefer unique process keys where available (`ProcessUniqueId` and `InitiatingProcessUniqueId`); PID-only correlation may collide after process reuse.
- A missing event can reflect audit configuration, unavailable telemetry, filter or retention—not necessarily absence of activity.
- Sysmon events use `ProcessGuid` for process correlation. Review the same host and relevant UTC time range; use source/target GUIDs for process-access events. Sysmon requires installation and appropriate filtering. Network connection (3) and image load (7) are disabled by default.

## Official references
- [Windows auditing](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/)
- [Windows logon event types](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/basic-audit-logon-events)
- [Sysmon overview and event IDs](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Defender XDR advanced hunting schema](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-schema-tables)
- [Defender XDR query best practices](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-best-practices)

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
