README — Kali Attacks (All Attacks Denied)

This directory contains evidence of offensive security tests performed from the Kali Linux attacker machine against the hardened Windows Server 2022 Domain Controller.
All attacks were intentionally blocked due to the applied security hardening.
Each folder documents one attack type, the commands used, and the denied results.

Attack 1 — Anonymous Enumeration (Denied)

Tests whether SMB and LDAP allow unauthenticated enumeration.

Methods tested:

smbclient -L //DC-IP/ -N

enum4linux-ng -A DC-IP

crackmapexec smb DC-IP -u '' -p ''

Result:
All anonymous enumeration attempts failed. No shares, users, or LDAP information disclosed.

Attack 2 — Single-User Null Auth Attempt (Denied)

Tests whether a specific user can authenticate with a null password.

Methods tested:

crackmapexec smb DC-IP -u john.staff -p ''

Result:
Null password authentication was rejected.

Attack 3 — Username Enumeration (Denied)

Tests whether SMB reveals valid usernames through response differences.

Methods tested:

crackmapexec smb DC-IP -u userlist.txt -p ''

crackmapexec smb DC-IP -u userlist.txt -p wrongpassword

Result:
Server responses were identical for valid and invalid names. No username enumeration possible.

Attack 4 — Password Spray (Denied)

Tests whether weak or seasonal passwords allow entry.

Methods tested:

crackmapexec smb DC-IP -u userlist.txt -p Password123

crackmapexec smb DC-IP -u userlist.txt -p Winter2024

crackmapexec smb DC-IP -u john.staff -p Password1!

Result:
All password spray attempts were rejected.

Attack 5 — Fake Hash + Kerberos Abuse Attempts (Denied)

Tests whether SMB, NTLM, or Kerberos accepts fake credential material.

Methods tested:

crackmapexec smb DC-IP -u john.staff -H 'fakehash'

crackmapexec smb DC-IP -u john.staff -k (fake Kerberos ticket)

Result:
Both attempts failed. No hash or ticket acceptance.

Attack 6 — Kerberos Ticket Attacks (Denied)

Tests whether the Domain Controller accepts attempts to request service tickets or TGTs.

Methods tested:

GetUserSPNs.py with blank or invalid creds

getTGT.py with invalid creds

Connectivity tests to port 88

Result:
Kerberos did not allow ticket requests. Port 88 dropped connections at the firewall.

Attack 7 — LLMNR/NBT-NS Poisoning Attempt (Denied)

Tests whether the Domain Controller leaks credentials through name resolution poisoning.

Methods tested:

dig AXFR sec.lab @DC-IP

Network poisoning attempt (Responder)

Result:
Zone transfer failed. LLMNR/NBT-NS did not respond. No credentials leaked.

Attack 8 — LDAP and LDAPS Enumeration (Denied)

Tests whether LDAP (389) or LDAPS (636) reveals searchable directory information.

Methods tested:

ldapsearch (LDAP)

ldapsearch (LDAPS)

Result:
Both ports dropped the requests. LDAP did not allow any binds or queries.

Attack 9 — Nmap Network Scanning (Denied)

Tests server visibility through port scanning and service detection.

Methods tested:

Full TCP SYN scan (nmap -sS -Pn -p-)

Version detection (nmap -sV)

UDP scan on key ports (nmap -sU -p 53,88,389,445)

Result:
All ports returned filtered or open|filtered with no-response.
No banners or fingerprints were revealed.

Summary

All offensive tests were successfully denied.
The Domain Controller and workstation hardening prevented:

SMB enumeration

LDAP/LDAPS information disclosure

Kerberos ticket abuse

Password spraying

Fake ticket or fake hash logins

Name resolution poisoning

Nmap fingerprinting

Anonymous access of any kind

The environment is fully hardened and resilient to common attacker techniques.
