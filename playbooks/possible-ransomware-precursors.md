# Playbook: possible ransomware precursor activity

> **Scope:** Potential ransomware staging and impact; follow local incident-response procedures for active ransomware indicators.

## Trigger

Unusual archive creation, rapid share enumeration, mass file changes, backup/shadow-copy disruption, broad remote execution, or security control changes.

## Contrast

| Potentially benign | Potentially malicious |
|---|---|
| Backup, file migration, software distribution, patch deployment, IT fleet management | Unauthorized privileged changes, new remote services/tasks across hosts, data staging, unknown outbound transfer and destructive actions |

## Investigation sequence

1. Confirm scope, criticality, active impact and whether rapid containment is required.
2. Build the sequence: initial access, privileged identity use, host discovery, remote execution, files staged, data movement, defense impairment and file impact **where supported**.
3. Correlate `DeviceProcessEvents`, `DeviceFileEvents`, `DeviceNetworkEvents`, service/task changes, identity logs, backup records and storage alerts.
4. Rule out approved migration, restore, EDR testing, backup agents, scheduled IT work and sanctioned penetration tests.
5. Search for related files, accounts, remote destinations, unique process lineages and affected shares.
6. Preserve key evidence and escalate to incident response on strong indicators of active unauthorized impact; do not wait for a ransom note.

## Important nuance

Ransomware incidents do not all contain each textbook phase. Encryption-only, data theft-only and opportunistic campaigns can have different timelines. Avoid assuming all operators achieved domain admin or persistence.

## Evidence record

Affected hosts/users/shares, UTC timeline, commands and hashes, number of file operations, backup status, outbound data estimates with uncertainty, and containment decisions.

See [network and ransomware behavior guide](../analysis/network-email-and-ransomware.md).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
