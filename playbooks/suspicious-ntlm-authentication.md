# Playbook: unexpected NTLM network authentication

> **Scope:** Unexpected NTLM authentication; tailor the investigation to domain roles and logging policies.

## Trigger

Successful Windows network logon (e.g., Security 4624, Type 3) using NTLM from an unfamiliar source to a sensitive endpoint, possibly with remote service/share access.

## Normal vs suspicious

| Benign possibility | Escalating clue |
|---|---|
| File/print servers, legacy applications, backup agents, approved admin tools | New privileged source host, lateral movement, unknown remote services or credentials accessed |

**Caution:** NTLM Type 3 is **not** a Pass-the-Hash verdict. A Type 9 *NewCredentials* event is also not proof of stolen hashes.

## Evidence and sequence

1. Confirm destination, source IP/hostname, targeted account, logon type, auth package and exact UTC timestamp.
2. Determine whether the principal and source device routinely access this target.
3. Review relevant 4776/DC auth events, 4648 and 4672 where available, understanding collection limitations.
4. Inspect service installations (System 7045), task changes, admin share access and remote process execution.
5. Check originating endpoint process and any escalation, file staging or suspicious connections.
6. Determine authorized change/maintenance context; scope other targets and accounts for the same source.

## Decision

- **Monitor / low confidence:** Established application auth without suspicious secondary actions.
- **Investigate:** First-seen privileged auth and unclear origin.
- **Escalate promptly:** Evidence of unauthorized access, remote execution, account abuse or movement to additional hosts.

## Documentation

Identify the benign alternatives considered, actual corroboration, account owner, impacted hosts, source queries and telemetry blind spots. Link to [event matrix](../references/event-telemetry-matrix.md).

Source: [Windows logon audit reference](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/basic-audit-logon-events).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
