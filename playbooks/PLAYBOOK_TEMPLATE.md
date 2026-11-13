# Investigation playbook: [Title]

> **Status:** Draft / Reviewed / Tested. **Last reviewed:** YYYY-MM-DD.

## Trigger / analyst question
What activity caused the investigation? What decision are we trying to make?

## Telemetry prerequisites
| Source | Key fields | Collection caveat |
|---|---|---|
| [event source] | [fields] | [audit setting, license, retention] |

## Investigation
1. Validate the alert and raw event.
2. Determine the parent process, command line, user, host and time range as applicable.
3. Correlate related network, file, identity and service events.
4. Establish whether any response action is needed.

## Benign explanations and false positives
- [Likely business use or deployment mechanism.]
- [Common instrumentation gaps.]

## Escalation and response
Escalate based on corroborated evidence, asset criticality and organization policy. Record containment action and ownership.

## Evidence to capture
- UTC timeline, affected identities/devices, raw event IDs, relevant process lineage, IoCs and artifacts
- Query or detection version, retained evidence location, confidence and open questions

## References and validation
Official docs, technique mapping, test notes, and date last checked.

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
