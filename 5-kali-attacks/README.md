# Kali Attack Attempts Against Hardened Windows Domain Controller — README

This section documents all offensive security attempts performed from the Kali Linux attack machine against the hardened Windows Server 2022 Domain Controller (LAB-DC01). Each attack demonstrates how common enumeration, authentication, Kerberos, DNS, and poisoning techniques fail when security hardening is properly implemented.

All attacks were executed after the completion of domain, workstation, firewall, NTLM, audit policy, and role-based restrictions.

Each attack folder contains:
1. The commands used  
2. Screenshots of console output  
3. Evidence of failure  
4. Confirmation that hardening prevented exploitation

This README provides a concise overview of each attack category and why it was unsuccessful.

---

## Attack 1 — Anonymous Network Enumeration (Denied)
Tools used: smbclient, enum4linux-ng, crackmapexec

Purpose: Determine whether anonymous users can enumerate SMB shares, domain information, or NetBIOS data.

Summary:
- Anonymous SMB listing returns “NT_STATUS_IO_TIMEOUT”
- enum4linux fails to connect to LDAP (389/636) and SMB (139/445)
- CrackMapExec initialization completes but enumeration returns no output

Conclusion: SMB, LDAP, and NetBIOS enumeration are disabled by firewall rules and guest/anonymous access hardening.

---

## Attack 2 — Single User Authentication Attempt (Denied)
Tools used: crackmapexec

Purpose: Test whether a single user with a blank password or incorrect password is accepted.

Summary:
- Null username/password rejected
- Incorrect password rejected

Conclusion: Password policy, lockout settings, and NTLM restrictions prevent unauthorized authentication.

---

## Attack 3 — Username Enumeration Using Userlists (Denied)
Tools used: crackmapexec with userlist.txt

Purpose: Identify valid domain users using SMB response analysis.

Summary:
- Null-password and wrong-password sprays return identical errors
- No distinction between valid and invalid usernames

Conclusion: Username enumeration resistance is effective.

---

## Attack 4 — Password Spray Attempts (Denied)
Tools used: crackmapexec

Purpose: Attempt common passwords across all usernames.

Summary:
- Password123 and Winter2024 failed across all users
- Single-user password spray also failed

Conclusion: Password hardening and Kerberos pre-authentication prevent password spraying.

---

## Attack 5 — Fake Hash and Kerberos Ticket Abuse Attempts (Denied)
Tools used: crackmapexec (-H, -k)

Purpose: Test pass-the-hash, pass-the-ticket, and fake Kerberos tickets.

Summary:
- Fake NTLM hash rejected
- Fake Kerberos TGT rejected

Conclusion: NTLMv1 is disabled, NTLM fallback is blocked, and Kerberos ticket integrity is enforced.

---

## Attack 6 — Kerberos Service Ticket and SPN Abuse Attempts (Denied)
Tools used: Impacket (GetUserSPNs.py, getTGT.py)

Purpose: Attempt Kerberoasting, AS-REP roasting, and ticket impersonation.

Summary:
- SPN enumeration failed
- TGT request returns Errno 111 (connection refused)
- Firewall blocks port 88

Conclusion: Kerberos exposure is minimized and SPN enumeration is restricted.

---

## Attack 7 — LLMNR/NBT-NS Poisoning Attempt (Denied)
Tools used: dig AXFR, DNS spoofing attempts

Purpose: Attempt DNS zone transfer and multicast poisoning.

Summary:
- DNS AXFR attempt timed out
- No responses on TCP/UDP port 53
- No multicast responses

Conclusion: DNS zone transfers disabled, LLMNR/NBT-NS restricted.

---

## Attack 8 — LDAP and LDAPS Enumeration Attempts (Denied)
Tools used: ldapsearch over 389 and 636

Purpose: Determine LDAP directory exposure.

Summary:
- LDAP and LDAPS both time out
- No directory information returned

Conclusion: LDAP anonymous binds disabled and ports firewalled.

---

## Attack 9 — SMB Share Permission Abuse Attempts (Denied)
Tools used: smbclient, crackmapexec smb

Purpose: Attempt connecting to administrative or hidden SMB shares using anonymous or invalid credentials.

Summary:
- Attempts to access C$, ADMIN$, IPC$ return access denied or timeout
- No share listing available to unauthenticated users
- No guest access permitted

Conclusion: Administrative shares are correctly restricted and not exposed to attackers.

---

## Overall Summary

All offensive attempts from the Kali attack machine failed.

This demonstrates the effectiveness of:
- SMB hardening
- Firewall configuration
- NTLM restrictions
- Password and lockout policies
- Kerberos hardening
- LDAP/LDAPS restrictions
- DNS security
- LLMNR/NBT-NS protections
- Role-based restrictions
- Network segmentation

The hardened Domain Controller exposes no useful information to unauthenticated attackers.
