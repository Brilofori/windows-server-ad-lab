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

Summary of results:
- Anonymous SMB listing returns “NT_STATUS_IO_TIMEOUT”
- enum4linux fails to connect to LDAP (389/636) and SMB (139/445)
- CrackMapExec initialization completes but further enumeration returns no output

Conclusion:
SMB, LDAP, and NetBIOS enumeration are disabled by firewall rules and guest/anonymous access hardening.

---

## Attack 2 — Single User Authentication Attempt (Denied)
Tools used: crackmapexec

Purpose: Test whether a single user with a blank password or incorrect password is accepted.

Summary of results:
- Null username/password combination rejected
- Incorrect password for a valid domain user rejected

Conclusion:
Account lockout, password policies, and NTLM restrictions prevent unauthorized single-user authentication attempts.

---

## Attack 3 — Username Enumeration Using Userlists (Denied)
Tools used: crackmapexec with -u userlist.txt

Purpose: Identify valid domain users based on SMB response differences.

Summary of results:
- Both null-password and wrong-password sprays return identical generic failure messages
- No distinction is made between valid and invalid usernames

Conclusion:
Username enumeration resistance is properly configured.  
NTLM auditing and SMB hardening prevent user validation through timing or error responses.

---

## Attack 4 — Password Spray Attempts (Denied)
Tools used: crackmapexec

Purpose: Attempt common passwords across all usernames to identify weak credentials.

Summary of results:
- Password spraying using “Password123” and “Winter2024” failed
- Single-user spraying also produced uniform failure messages

Conclusion:
Password policy, lockout policy, and Kerberos pre-authentication prevent password spray success.

---

## Attack 5 — Fake Hash and Kerberos Ticket Abuse Attempts (Denied)
Tools used: crackmapexec with -H and -k

Purpose: Test whether pass-the-hash, pass-the-ticket, or invalid Kerberos tickets can be used.

Summary of results:
- Fake NTLM hash authentication failed
- Fake Kerberos TGT attempt failed
- No usable authentication material was accepted

Conclusion:
NTLMv1 is disabled, NTLM fallback is blocked, and Kerberos ticket integrity checks are enforced.

---

## Attack 6 — Kerberos Service Ticket and SPN Abuse Attempts (Denied)
Tools used: Impacket (GetUserSPNs.py, getTGT.py)

Purpose: Attempt Kerberoasting, AS-REP roasting, and TGT abuse.

Summary of results:
- SPN enumeration with blank credentials failed
- TGT request returned “Errno 111 Connection refused”
- Port 88 connectivity tests confirm firewall blocking
- Responder traffic and poisoning attempts were blocked

Conclusion:
Kerberos exposure is minimized, port 88 is firewalled, and SPN enumeration is restricted.

---

## Attack 7 — LLMNR/NBT-NS Poisoning Attempt (Denied)
Tools used: dig AXFR, DNS zone transfer attempts

Purpose: Attempt DNS zone transfer and poisoning attacks related to multicast/LLMNR/NBT-NS.

Summary of results:
- DNS AXFR attempt failed with timeout
- No servers responded on TCP/UDP 53 for transfer
- Multicast DNS lookups produced no responses

Conclusion:
DNS zone transfers are disabled, LLMNR/NBT-NS are restricted, and firewall rules prevent poisoning.

---

## Attack 8 — LDAP and LDAPS Enumeration Attempts (Denied)
Tools used: ldapsearch over LDAP (389) and LDAPS (636)

Purpose: Determine whether LDAP directory objects can be queried anonymously.

Summary of results:
- LDAP search returned connection timeout
- LDAPS also returned timeout
- No directory information was exposed

Conclusion:
LDAP anonymous binds are disabled, LDAPS is restricted, and both ports are firewalled from external hosts.

---

## Overall Summary
All offensive attempts from the Kali attack machine failed.  
This demonstrates the effectiveness of the implemented hardening measures:

- SMB hardening  
- Domain firewall configuration  
- NTLM policies  
- Password and lockout policies  
- Kerberos hardening  
- LDAP/LDAPS restrictions  
- DNS security  
- LLMNR/NBT-NS protections  
- Role-based workstation restrictions  
- Network segmentation

The hardened Domain Controller provides no useful information to an unauthenticated attacker, and all high-value attack paths are blocked at the network or policy level.
