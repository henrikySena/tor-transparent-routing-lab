# Tor Transparency Lab — Traffic Transparency and Anonymity on Linux (Technical Study)

<br>

This project documents a **technical and educational study** on network anonymity in Linux systems, starting from the premise that **Tor provides a robust architecture for anonymous browsing**, but that its effectiveness depends directly on **how it is integrated into the operating system**.

The lab investigates, in both practical and conceptual terms, the differences between:

- Using Tor **at the application level** (e.g., `torsocks`)
- Using Tor **in a transparent, system-wide manner** (forced routing)

The focus was not merely to make Tor work, but to **understand why certain approaches fail**, where traffic leaks occur, and which architectural decisions make solutions such as **Tails** and **Whonix** more resilient.

> ⚠️ Study conducted in a controlled environment for strictly educational purposes.

---
<br>

## Motivation

The motivation for this study emerged from the following initial hypothesis:

> **Tor offers one of the most mature and well-audited infrastructures for anonymous browsing publicly available.**

From this premise, the central question of the project arose:

> *If Tor is architecturally robust, why do IP leaks and anonymity failures still occur when it is used on a traditional Linux system?*

During practical experiments on Kali Linux, it became evident that **the issue does not lie with Tor itself**, but rather:

- In how the operating system originates traffic
- In the conceptual separation between DNS, routing, and network protocols
- In the absence of restrictive network policies applied from boot time

This project therefore stems from the need to **understand anonymity as a system-level property**, not as the result of isolated commands.

---
<br>

## Technical Environment

### System
- Operating System: Kali Linux (virtual machine)
- Virtualization: VirtualBox / VMware
- Network mode: NAT

### Services and Components
- Tor daemon
- NetworkManager
- Firewall based on `iptables`

### Tools Used
- `curl`
- `ping`
- `torsocks`
- `dig`
- standard Linux networking utilities

> Methodological note: tests were conducted manually with a focus on observing network behavior. Command outputs and detailed logs were not preserved, as the primary goal was conceptual understanding.

---
<br>

## Technical Objectives

1. Evaluate the feasibility of achieving full anonymity on a traditional Linux system using Tor
2. Understand why direct IP traffic bypasses DNS configurations
3. Identify the limitations of the application-level anonymity model (`torsocks`)
4. Compare this approach with solutions designed for system-enforced anonymity

---
<br>

## Tests and Practical Observations

### Connectivity and Name Resolution

- `ping 8.8.8.8` → success
  - Demonstrates that ICMP does not depend on DNS

- `ping google.com` → failure
  - Local DNS configured to `127.0.0.1`
  - Failure occurs at name resolution, not packet transmission

- `curl http://example.com` → failure without Tor

- `torsocks curl https://check.torproject.org` → success
  - HTTP(S) traffic correctly encapsulated through the Tor network

### Relevant Observations

- DNS affects **name resolution only**, not traffic routing
- Protocols such as ICMP bypass proxy mechanisms entirely
- `torsocks` depends on explicit application cooperation

---
<br>

## Technical Analysis

### DNS Is Not Traffic

One of the main takeaways from the lab was understanding that:

> **Configuring DNS does not equate to controlling network traffic.**

Even with DNS pointing to `localhost`, applications that use:
- direct IP addresses
- low-level sockets
- protocols unsupported by proxies

can exit the system without any form of anonymization.

---
<br>

### Limitations of the Application-Level Model

Using Tor via `torsocks` presents structural limitations:

- Relies on user discipline
- Does not cover all protocols
- Does not prevent accidental leaks
- Provides no system-level guarantees

This approach is suitable for **isolated use cases**, but insufficient for strong anonymity.

---
<br>

### Anonymity as an Architectural Decision

The central insight of the project can be summarized as follows:

> **Real anonymity is not a configuration — it is an architectural decision.**

Robust solutions do not trust users or applications; they **block by default** and explicitly allow only traffic that is correctly routed.

---
<br>

## Comparison of Approaches

| Approach | Control Level | Leak Risk | Notes |
|---------|--------------|----------|------|
| Tor via torsocks | Application-level | High | Easy to use, easy to misuse |
| Kali + Tor + firewall | System-level | Medium | Requires strict control |
| Tails | System-level (by design) | Very low | Anonymity enforced from boot |

---
<br>

## Why Solutions Like Tails Work

- *Deny-by-default* policy
- Traffic allowed exclusively through Tor
- Firewall active from system initialization
- The system does not trust the user itself

In contrast, on traditional distributions like Kali Linux, the network stack is already active before strict controls are applied.

---
<br>

## Study Limitations

- No persistent logs or command outputs
- Tests focused on behavioral observation
- No packet-level traffic capture

These limitations were consciously accepted, as the goal of the project was **mental model construction**, not forensic validation.

---
<br>

## Key Learnings

- DNS does not guarantee anonymity
- Proxying does not equal transparency
- ICMP bypasses Tor
- Security depends more on architecture than on tools

---
<br>

## Next Steps

- In-depth study of Tor TransPort
- Reproducing Whonix architecture (Gateway + Workstation)
- Comparative analysis with Tails
- Future inclusion of traffic capture and logs

---

## Methodological Notes

This project was conducted with a deliberate focus on **structural learning**. Failures, unexpected behaviors, and limitations were treated as essential parts of the process, directly contributing to a deeper understanding of anonymity, networking, and security in Linux systems.
