NTLM Hardening (Workstation Baseline)

This directory documents the NTLM hardening controls applied to the Workstation Baseline GPO. These settings enforce modern authentication, prevent downgrade attacks, and block legacy NTLM/LM protocols.

Implemented Controls
1. Audit Incoming NTLM Traffic
Enables auditing for all NTLM inbound authentication attempts.

2. Audit Outgoing NTLM Traffic
Audits or blocks all outbound NTLM authentication. Configured to Deny all.

3. NTLM Authentication in this Domain
Domain-wide NTLM authentication is denied to prevent credential replay and downgrade attacks.

4. Remote Server Exceptions
No exceptions are configured. NTLM usage is fully restricted.

5. NULL Session Fallback
NULL session fallback is disabled to prevent unauthenticated remote access attempts.

Purpose
These configurations eliminate the use of LM, NTLMv1, and insecure NTLM traffic, reducing exposure to:
Pass-the-Hash attacks
Responder/relay attacks
SMB relay
NTLM downgrade attacks
Credential interception

This aligns the workstation with modern enterprise authentication standards (Kerberos + NTLMv2 only).
