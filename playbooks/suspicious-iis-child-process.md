# Investigating `w3wp.exe` spawning a command shell

> **Scope:** Web application process and request correlation.

## Trigger

On an IIS web server, `w3wp.exe` starts `cmd.exe`, `powershell.exe`, or another interactive/system utility.

## Why it matters

The IIS worker process executes website/application code. A child shell can be consistent with web shell execution or exploitation, but can also be caused by intentionally configured application code, deployment workflows or maintenance.

## Required context

- Host identity, IIS application pool, website and application owner
- Child process full command line, parent process, initiating identity, file signatures and hashes
- IIS/HTTP requests immediately before the child process
- EDR/Sysmon process and network telemetry; file creation and changes
- Known application functionality, scheduled deployments and change tickets

## Investigation sequence

1. Confirm the process lineage and timestamps; don't infer lineage from process names alone.
2. Inspect the full command line and any decoded arguments; determine account privileges.
3. Correlate IIS request logs and application logs around execution. Examine request path, method, status, originating IP, user agent and authentication context.
4. Check whether new web-accessible files, scripts or executables were created or modified.
5. Follow subsequent child processes, outbound connections and lateral movement indicators.
6. Compare with the documented application design, authorized testing and maintenance.

## Escalation indicators

- Unexpected remote request followed closely by child process execution
- Newly introduced web shell-like artifacts, payload retrieval or suspicious network connections
- Persistence, privileged commands, unexpected account activity or evidence of data access

## Caveats

An HTTP 200 response by itself does not prove code execution or exploit success. Conversely, an unsuccessful-looking HTTP status does not rule out compromise. The process relationship is suspicious **in context**, not automatically confirmed compromise.

## Evidence record

Capture timestamps in UTC, request IDs/log entries, process GUID or equivalent, command line, username, relevant hashes, network destinations, evidence of authorized application behavior, and rationale for the severity chosen.

Related: [master reference — telemetry and host investigation](../notes/SOC_Master_Notebook_Clean.md#05-telemetry-event-logs-and-host-investigation).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
