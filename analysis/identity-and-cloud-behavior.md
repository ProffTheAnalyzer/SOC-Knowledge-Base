# Identity and cloud activity — normal vs. suspicious

> **Focus:** NTLM, Kerberos, token theft, MFA fatigue and cloud-security investigations. Validate individual alerts against available tenant telemetry.

## Authentication and credentials

| Observation | Could be normal | Raises concern when | Follow-up |
|---|---|---|---|
| Security 4624, Logon Type 3 | File share, print server, remote management, service access | New privileged account from unexpected source plus remote execution | Source/destination, 4776/4769, service/share/process events |
| Security 4624, Type 9 | `runas /netonly` or other explicit network credential mechanism | Surprising account + external network actions | Account source, initiating process, 4648 where present |
| NTLM authentication on domain | Older legitimate apps or appliances | Sudden high-value account use, privilege escalation or new lateral path | NTLM baseline, affected resource, source host and process |
| Many Kerberos TGS requests | Service use, discovery, delegated app workflows | Unusual SPNs, client, encryption settings plus subsequent exploitation | DC event 4769, service owner, ticket details |
| Event 4662 / replication | Authorized domain replication, identity sync | Unapproved security principal or source host with replication rights | SACL configuration, access GUIDs and ACL inventory |
| Administrator privileges (4672) | Admin interactive/service login | Privileged use outside permitted workflow plus suspicious changes | Related 4624/logon ID, initiating host, subsequent processes |

**Important distinctions:** Type 9 is `NewCredentials`; it does not diagnose pass-the-hash. A successful NTLM Type 3 logon is not proof of a stolen NTLM hash. The absence of a nearby Kerberos ticket event is not proof of forged or replayed tickets—cached tickets and incomplete collection exist.

## Entra ID sign-in / MFA / token scenarios

<details>
<summary><strong>Unfamiliar IP or different country</strong></summary>

**Normal possibilities:** VPN egress change, mobile carrier NAT, travel, roaming, shared proxy, hosting of legitimate automation.

**Concern increases:** First-seen unmanaged device, high-risk sign-in context, unfamiliar app/resource, consent changes or bulk data access after login.

**Pivot:** `SigninLogs`, `AuditLogs`, MFA and Conditional Access result, device/browser detail, activity after sign-in. Distinguish an unfamiliar IP from an impossible-travel *hypothesis*.

</details>

<details>
<summary><strong>MFA push prompt storm</strong></summary>

**Normal possibilities:** Misconfigured client, repeated sign-in attempts or user confusion.

**Concern increases:** Repeated denials then an approval, followed by suspicious resource access, changed MFA methods, or new device/session registration.

**Pivot:** Authentication methods, per-attempt MFA details, device, app, source IP, user confirmation and follow-on audit actions.

</details>

<details>
<summary><strong>Successful access without interactive MFA challenge</strong></summary>

**Normal possibilities:** Existing session, valid refresh token, device claims, Conditional Access satisfied previously, service principals.

**Concern increases:** Token use from atypical client/location, suspicious OAuth app, abnormal refresh pattern or privilege sensitive API access.

**Pivot:** Token/session policy, available sign-in and audit records, app/service principal, granted scopes, device assurance, revoke actions where appropriate.

**Do not assume:** Every access token is a session cookie; changing a password or revoking sessions has uniform effect across all token types. The effect depends on platform, token lifetime and continuous access evaluation/policies.

</details>

<details>
<summary><strong>New OAuth application / admin consent</strong></summary>

**Normal possibilities:** IT-approved SaaS integration, new internal workflow.

**Concern increases:** Unverified publisher, unexpected administrator approval, access to broad mail/files privileges, unusual app sign-ins or mass API retrieval.

**Pivot:** `AuditLogs` (consent and service-principal changes), app owner, permissions, OAuth sign-in activity, business approval and subsequent data operations.

</details>

## Cloud identity parallels

| Surface | Normal use | Concerning combination |
|---|---|---|
| AWS STS assumed role | Federated SSO, sanctioned CI/CD | Newly assumed sensitive role from unusual principal plus key/resource access |
| AWS CloudTrail console/API events | Automation and platform management | First-seen role, unapproved IAM policy/credential change plus data access |
| Azure role assignment | Planned RBAC maintenance | New privileged role granted without change ticket, followed by admin actions |
| Entra application permissions | SaaS connection, lifecycle management | Unauthorized broad permission grant and unfamiliar service principal |

CloudTrail / Entra audit formats and fields vary; validate data connector coverage before treating absence of logs as absence of misuse.

## Confidence model

- **Weak:** Unfamiliar location or a failed sign-in alone.
- **Moderate:** Multiple unusual attributes with identifiable principal, device and resource.
- **Higher:** Abnormal authentication + unauthorized permission change/data access or confirmed endpoint compromise.

References: [Windows logon types](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/basic-audit-logon-events), [Microsoft Entra sign-in logs](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/concept-sign-ins), [MITRE ATT&CK](https://attack.mitre.org/). See [event matrix](../references/event-telemetry-matrix.md).

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
