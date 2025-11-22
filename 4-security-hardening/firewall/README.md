Windows Firewall Hardening

This directory documents the firewall rules applied as part of the workstation security baseline. These rules restrict unnecessary network exposure, reduce attack surface, and enforce controlled communication paths.

Configured Rules
1. Disable Inbound Connections (Default Deny)
All unsolicited inbound traffic is blocked unless explicitly allowed.
This prevents remote exploitation, lateral movement, and unauthorized access.

2. Block Outbound Connections When Unsafe
Outbound traffic from high-risk services or unnecessary applications is restricted to prevent:
-Malware beaconing
-Data exfiltration
-Unauthorized remote access

3. Harden Public and Private Profiles
Each network profile is configured with stricter rules, including:
Blocking file and printer sharing
Disabling remote desktop inbound
Preventing discovery and unsolicited connections

5. Explicit Allow Rules for Required Services
Only essential services are granted allow rules (e.g., DHCP, DNS).
Everything else remains blocked by design.

Purpose

These rules enforce a least-privilege network posture by allowing only the minimum required communication. This significantly reduces exposure to network-based attacks and aligns with enterprise workstation security practices.
