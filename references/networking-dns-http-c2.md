# Network architecture, DNS, HTTP, and C2 investigation reference

This reference connects everyday network concepts to evidence-driven SOC investigations. Network artifacts such as domain names, ports, HTTP methods and timing patterns are **context**, not automatic proof of an attack.

## DMZ (demilitarized zone)

A DMZ is a network segment used to separate services exposed to untrusted networks from more sensitive internal systems. Public-facing applications such as websites, reverse proxies, mail gateways or VPN portals may sit in a DMZ or a modern equivalent.

Internet-facing does **not** mean the entire internal network is directly reachable. Firewall policy, segmentation, identity controls and service configuration govern permitted flows. Some organizations host public services in cloud networks rather than a traditional on-premises DMZ.

**Investigate:** Which service should be internet-accessible? Which inbound and outbound connections are allowed? Did an externally reachable host initiate unusual connections to protected internal systems?

## Network flows: two endpoints and direction

A connection is often described with a five-tuple: **source IP + source port → destination IP + destination port + transport protocol**. For example:

```text
192.0.2.15:51544 → 198.51.100.20:443 (TCP)
```

- IP addresses identify the endpoints; port numbers identify service endpoints within TCP/UDP.
- TCP/UDP port fields are 16-bit values from **0 to 65535**. Port 0 is reserved or special-purpose in many contexts; ordinary network services generally use ports 1–65535.
- Port **443** is the default for HTTPS but is not proof that observed traffic is HTTPS, encrypted, benign or malicious.
- Flow records commonly include timestamps, duration, source/destination, protocol, ports, bytes and packets. **Byte direction depends on the sensor's schema**; a field named `bytes` does not automatically mean "bytes received."
- NAT, load balancers, VPNs, proxies and cloud egress can change which endpoint IPs a particular sensor records.

**Investigate:** Determine flow direction, who initiated the connection, process attribution where available, byte counts, the actual service and whether the behavior is expected.

## DNS records and TTL

| Record | What it represents | Example |
|---|---|---|
| **A** | Maps a hostname to an IPv4 address | `www.example.org` → `192.0.2.20` |
| **AAAA** | Maps a hostname to an IPv6 address | `www.example.org` → `2001:db8::20` |
| **MX** | Specifies mail servers for a domain | `example.org` → `mail.example.org` |
| **CNAME** | Aliases one hostname to another hostname | `portal.example.org` → `web.example.org` |
| **TXT** | Text-based DNS records, often used for verification and mail security | SPF/verification data |

**TTL (Time to Live)** controls how long DNS resolvers may cache an answer before revalidating it. For example, TTL **300** means **300 seconds**, or five minutes. Caching, DNS forwarding and local resolver behavior affect which queries appear in different logs.

### What DNS evidence can tell an analyst

A process-associated query can show that a device or application requested a domain's DNS information. This can remain useful even when later web traffic is encrypted. However:

- A DNS query does **not** prove a successful network connection, malicious intent, or that the domain was visited by a person.
- Queries may come from automatic application behavior, security tools, preview/prefetch, background services or system components.
- Cached answers may reduce external DNS queries, and not every DNS request is necessarily visible at every collection point.
- Correlate with proxy, firewall, EDR network events, process lineage and the actual destination.

**Investigation example:** An unfamiliar executable queries a newly observed hostname and then contacts an associated IP. Correlate process identity, timestamps, DNS responses and network activity before considering possible command-and-control behavior.

## Proxies, HTTPS and visibility

A forward web proxy sits between clients and web destinations, often enforcing access policy and recording requests. For **HTTP**, a proxy may observe the complete requested URL. For **HTTPS**, full paths and request contents are available to an inspecting proxy **only if traffic is successfully decrypted/terminated** (for example, through an authorized TLS-inspection deployment). Without decryption, visibility may be limited to target host, connection metadata, SNI when available, DNS and other fields. Coverage varies with proxy architecture, browser behavior, ECH, exceptions and certificate pinning.

**Investigate:** Request method, host, path if visible, user/device, action (allow/block), response, redirect chain, bytes and the initiating process.

## HTTP methods and response codes

| Method | Typical purpose |
|---|---|
| `GET` | Retrieve a resource |
| `POST` | Submit data or request processing |
| `PUT` | Create or replace a resource at a specified URI |
| `DELETE` | Request removal of a resource |

| Status | Meaning |
|---|---|
| **200** | Request succeeded at the HTTP application level |
| **301** | Permanent redirect |
| **302** | Temporary redirect |
| **403** | Forbidden / refused by server |
| **404** | Resource not found |
| **500** | Internal server error |

A **200** response does not prove a web exploit succeeded or that a payload executed. A **403/404** does not mean no attack attempt occurred. Interpret response codes alongside server-side requests, authentication, process creation, file changes and follow-on activity.

## Command-and-control (C2) and beaconing

A compromised endpoint may communicate with attacker-controlled infrastructure for instructions, status updates or data transfer. **Beaconing** describes repeated check-ins, which may appear periodically in DNS, proxy, firewall or endpoint telemetry.

Illustrative, non-resolving example:

```text
hxxps://update-cdn-service[.]example/api/v1/status
hxxps://update-cdn-service[.]example/api/v1/config
```

Paths such as `/api/v1/status` or `/api/v1/config` can be **legitimate API endpoints**; the path alone cannot identify a C2 channel or reveal the exact purpose of a request. Under a C2 hypothesis, one path might support a check-in and another configuration retrieval, but that role must be established with additional evidence.

### What can make repeated traffic suspicious?

- Similar intervals, such as roughly every 5, 15, 30 or 60 minutes, especially when attributed to an unexpected process.
- Consistent connections to a rare or newly observed destination.
- Repeatable request structure, similar byte patterns, unexplained GET/POST activity or accompanying payload execution.
- Relevant endpoint alerts, persistence, suspicious file activity, credential access or unauthorized remote control.

**Caveats:** Updaters, monitoring agents, polling services and health checks can be very regular. Many malware families introduce **jitter** (random timing variation) or use event-driven communication, so perfectly regular intervals are not required. DNS and HTTPS alone do not establish C2.

### Domains, TLDs and port 443

Some attacker-controlled domains imitate infrastructure or software-update naming. A name resembling a CDN or API may draw investigation interest when combined with suspicious activity. The **`.xyz` top-level domain is not inherently malicious**: legitimate sites use it, and TLD alone is insufficient for classification.

Attackers may use outbound HTTPS on 443 because it is common and often allowed, but **port 443 traffic does not all look identical to defenders**. Processes, certificates, destinations, timing, TLS/network metadata and (when available) proxy content may distinguish activity.

## Blocking suspicious communications

| Layer | Potential response | Limitations |
|---|---|---|
| **DNS** | Deny or sinkhole a known malicious domain | IP literals, alternative resolvers, cached answers or other domains can bypass one DNS rule |
| **Firewall** | Block destination IP/port or restrict egress | Shared cloud/CDN IPs may host legitimate services; endpoints can change |
| **Proxy / secure web gateway** | Block hostnames or URL paths where visible | Encrypted paths may not be visible without permitted inspection; redirects and alternate channels may bypass patterns |
| **Endpoint** | Isolate a host or block an observed process, per incident-response authority | Containment requires authorization and may affect business services |

Always scope indicators, monitor for changes and verify that the block actually prevented the relevant behavior. Blocking one destination does not mean an incident is contained.

## Air-gapped networks and systems

An **air-gapped** environment is intentionally isolated from other networks, typically with no direct routed or wireless connection to the internet or corporate network. It may be used for highly sensitive or critical systems, including industrial/OT environments.

- Software and data may need tightly controlled transfer using removable media or offline update procedures.
- Air gaps reduce network exposure but **do not eliminate** insider, removable-media, supply-chain or physical-access risks.
- A **data diode** is a deliberately engineered *one-way connection*. That is a controlled isolation architecture, not literally the same thing as having no physical path between networks.
- Investigations should consider removable-media logs, physical access, approved maintenance, transfer controls and the actual documented topology.

## XOR in security analysis

**XOR (exclusive OR)** returns 1 when two input bits differ and 0 when they match:

| A | B | A XOR B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Repeated XOR with the same key reverses the transformation: `(data XOR key) XOR key = data`. Analysts encounter XOR in obfuscated malware strings, lightweight encodings and binary transformations. **XOR alone is not proof of malware**, and a simple repeating-key XOR is not secure encryption. Inspect surrounding context before classifying encoded content.

## References

- [Cloudflare DNS record types and TTL](https://www.cloudflare.com/learning/dns/dns-records/)
- [Windows DNS lookups and caching](https://learn.microsoft.com/en-us/windows-server/networking/dns/queries-lookups)
- [Sysmon events, including DNS query Event 22](https://learn.microsoft.com/en-us/windows/security/operating-system-security/sysmon/sysmon-events)
- [MDN HTTP methods and response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference)
- [SOC network, email and ransomware behavior guide](../analysis/network-email-and-ransomware.md)

---

**[Cyber Profezzor — YouTube Channel](https://www.youtube.com/@thecyberprofezzor)**  
Explore my cybersecurity investigations, SOC labs, and hands-on security tutorials.
