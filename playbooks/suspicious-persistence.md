# Playbook: suspicious service, task or registry persistence

> **Scope:** Persistence-capable artifacts. Collection differs by Windows auditing and EDR configuration.

## Trigger

First-seen Run key value, scheduled task, startup artifact, installed Windows service or permanent WMI subscription.

## Normal vs suspicious

| Normal pattern | Investigation priority increases when |
|---|---|
| Approved software install, endpoint management, updater, backup tool | User-writable path, unsigned/script payload, high privileges, unusual writer and remote initiation |

## Steps

1. Confirm artifact exact name/path, command or binary, creation time, modifying user/process and registry/task/service configuration.
2. Retrieve initiating process, file origin, signature/hash, persistence trigger and run-as context.
3. Correlate with logons, software installs, remote management, downloads and recent privilege changes.
4. Establish whether owner and application legitimately require the artifact.
5. Check persistence execution, network behavior and spread to other hosts.
6. Determine risk, recommended response and explicit criteria for suppression or re-alerting.

## Telemetry examples

- `DeviceRegistryEvents` and `DeviceProcessEvents` (Defender XDR)
- Windows Security 4698 when task auditing enabled, System 7045 for services
- Sysmon 12/13/14 for configured registry event capture

**Pitfall:** A legitimate signed `schtasks.exe` or `reg.exe` executable can create malicious artifacts. The action and created payload matter more than binary reputation alone.

See [Windows behavior guide](../analysis/windows-process-baselines.md) and [telemetry matrix](../references/event-telemetry-matrix.md).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
