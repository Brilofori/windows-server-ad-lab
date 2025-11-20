NTLM Hardening (Server 2022)

This section documents the NTLM security hardening applied to the domain controller to reduce legacy authentication attacks and enforce modern authentication standards.

Objectives

Disable weak authentication protocols (LM and NTLMv1)

Restrict NTLM usage across the domain

Prevent anonymous access and null sessions

Enforce auditing for any remaining NTLM traffic

Applied Settings
1. Audit Incoming NTLM Traffic

Policy: Network security: Restrict NTLM: Audit Incoming NTLM Traffic
Setting: Enable auditing for all accounts

2. Restrict Outgoing NTLM Traffic

Policy: Restrict NTLM: Outgoing NTLM traffic to remote servers
Setting: Deny all

3. Restrict NTLM Authentication in the Domain

Policy: Restrict NTLM: NTLM authentication in this domain
Setting: Deny all

4. NTLM Remote Server Exceptions

Policy: Restrict NTLM: Add remote server exceptions for NTLM authentication
Setting: No exceptions configured

5. Disable NULL Session Fallback

Policy: Network security: Allow LocalSystem NULL session fallback
Setting: Disabled

Summary

These configurations disable legacy NTLM behavior, block anonymous access paths, and ensure all NTLM usage is either audited or denied. This hardening significantly reduces the attack surface for relay attacks, brute-force attempts, and credential interception techniques.
