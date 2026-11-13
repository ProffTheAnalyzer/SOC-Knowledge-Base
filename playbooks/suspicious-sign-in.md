# Playbook: suspicious Entra ID sign-in or MFA activity

> **Scope:** Identity and MFA investigation. No single "impossible travel" score or IP reputation verdict is dispositive.

## Trigger

Sign-in from a new IP/device, unusual country, MFA push storm, or resource access apparently inconsistent with normal user activity.

## Normal vs suspicious

| Could be authorized | Increases confidence |
|---|---|
| Corporate VPN, mobile IP, travel, new managed device, refresh tokens | Unfamiliar device + MFA anomaly + suspicious privileged/API/mail changes |

## Investigate

1. Confirm principal, timestamp, IP, app, client type, Conditional Access/MFA details and device assurance from `SigninLogs`.
2. Investigate previous sign-ins and compare with known VPN egress and travel; flag missing baselines.
3. Look for MFA denials followed by approval and the user's actual confirmation.
4. Review `AuditLogs` for MFA method changes, app consent, directory group/role changes and new applications.
5. Correlate mailbox/file/API activity and endpoint posture, including evidence of token/session misuse where visible.
6. Scope additional identities, apps and sessions; apply organization-approved session and credential response if warranted.

## Conclusion

Classify as expected, suspicious/inconclusive, or likely unauthorized based on corroboration; include business context and telemetry gaps. Refresh token behavior and revocation effects depend on token/session policy.

See [identity behavior guide](../analysis/identity-and-cloud-behavior.md) and [Entra sign-in documentation](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/concept-sign-ins).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
