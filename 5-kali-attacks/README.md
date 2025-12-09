Kali Attack Evidence – Denied Attempts (Adjusted Student Style)

Overview
This document summarizes every attack performed from the Kali attacker machine and explains why each attempt failed.  
The purpose is to show how the hardening applied in the Windows Server AD Lab prevented enumeration, authentication abuse, Kerberos attacks, DNS exploitation, LDAP probing, and network scanning.

All attack folders referenced below exist in the GitHub repository and contain screenshots as evidence.

------------------------------------------------------------
Attack 1 – SMB and LDAP Enumeration Denied
Folder: attack-1-enumeration-denied

01-smbclient_denied.PNG
Command:
  smbclient -L //192.168.56.10/ -N
Result:
  NT_STATUS_ACCESS_DENIED or timeout; no share listing.
Why it matters:
• Anonymous SMB enumeration is blocked.
• Guest and anonymous access restrictions + firewall prevent unauthenticated share discovery.

02-enum4linux_denied.PNG
Command:
  enum4linux-ng -A 192.168.56.10
Result:
  All SMB/LDAP probes fail; tool aborts with no data.
Why it matters:
• enum4linux cannot fetch users, groups, or shares.
• Confirms external enumeration vectors are disabled.

03-cme_initialization.PNG
Command:
  crackmapexec smb 192.168.56.10 -u '' -p ''
Result:
  Only CME initialization occurs.
Why it matters:
• Shows CME works properly before the denial cases.

04-cme_denied_no_output.PNG
Command:
  crackmapexec smb 192.168.56.10 -u '' -p ''
Result:
  No enumeration; silent failure.
Why it matters:
• Anonymous enumeration fully blocked.

------------------------------------------------------------
Attack 2 – Single User, Anonymous and Wrong Password Denied
Folder: attack-2-single-user-auth-denied

01-cme_null_user_denied.PNG
Command:
  crackmapexec smb 192.168.56.10 -u '' -p ''
Result:
  Null session denied.
Why:
• Guest/anonymous auditing settings block null logon attempts.

02-cme_singleuser_wrongpass_denied.PNG
Command:
  crackmapexec smb 192.168.56.10 -u john.staff -p ilovemyWIFE
Result:
  No authentication feedback or success.
Why:
• Failed logon attempts return generic error responses.
• Hardening prevents username discovery through authentication behavior.

------------------------------------------------------------
Attack 3 – Username Enumeration Denied
Folder: attack-3-username-enumeration-denied

01-cme_userlist_nullpass_denied.PNG
Command:
  crackmapexec smb 192.168.56.10 -u userlist.txt -p ''
Result:
  No differentiation of valid vs invalid users.
Why:
• NTLM protections prevent response-based user enumeration.

02-cme_userlist_wrongpass_denied.PNG
Command:
  crackmapexec smb 192.168.56.10 -u userlist.txt -p WrongPassword123
Result:
  All attempts look identical.
Why:
• Password spraying cannot be used to determine valid accounts.

userlist.txt
Contains test usernames used during spraying.

------------------------------------------------------------
Attack 4 – Password Spray Denied
Folder: attack-4-password-spray-denied

01–03 Password spray screenshots
Commands:
  crackmapexec smb 192.168.56.10 -u userlist.txt -p Password123
  crackmapexec smb 192.168.56.10 -u userlist.txt -p Winter2024
  crackmapexec smb 192.168.56.10 -u john.staff -p Password1!
Result:
  No accounts authenticate.
Why:
• Strong password policy + lockout policy stop spraying attempts.
• No weak or default credentials in environment.

------------------------------------------------------------
Attack 5 – Fake Hash and Kerberos Ticket Abuse Denied
Folder: attack-5-fake-hash-denied

01-cme_fake_hash_denied.PNG
Command:
  crackmapexec smb 192.168.56.10 -u john.staff -H <fakehash>
Result:
  No NTLM session created.
Why:
• NTLM hardening blocks pass-the-hash attempts using bogus or malformed hashes.

02-cme_kerberos_fake_tgt_denied.PNG
Command:
  crackmapexec smb 192.168.56.10 -u john.staff -k
Result:
  No Kerberos authentication.
Why:
• Requires a valid ticket in cache.
• Kerberos enforces strong ticket validation.

------------------------------------------------------------
Attack 6 – Kerberos Ticket and SPN Abuse Denied
Folder: attack-6-kerberos-ticket-attacks-denied

01-getuserspns_blank_creds_denied.PNG
Command:
  GetUserSPNs.py sec.lab/'' -dc-ip 192.168.56.10
Result:
  Connection refused.
Why:
• Firewall denies Kerberos/LDAP enumeration attempts.

02-getTGT_connection_refused.PNG
Command:
  getTGT.py sec.lab/bril:batman11 -dc-ip 192.168.56.10
Result:
  Connection refused.
Why:
• External Kerberos ticket requests blocked by DC firewall.

02_port88_connection_refused.PNG
Command:
  nc -vz 192.168.56.10 88
Result:
  Port 88 refused.
Why:
• Proves firewall protection of the Kerberos service.

03-responder_block_evidence.PNG
Context:
  Shows DC is healthy but shielded.
Why:
• Denial is intentional security, not server failure.

------------------------------------------------------------
Attack 7 – LLMNR/NBT-NS Poisoning and DNS Zone Transfer Denied
Folder: attack-7-LLMNR-NBTNS-poisoning-denied

01-dig_axfr_denied.PNG
Command:
  dig AXFR sec.lab @192.168.56.10
Result:
  Zone transfer rejected.
Why:
• DNS AXFR is restricted to authoritative servers only.
• LLMNR/NBT-NS protections prevent spoof-style recon.

------------------------------------------------------------
Attack 8 – LDAP/LDAPS Enumeration Denied
Folder: attack-8-LDAPLDAPS-Enumeration-denied

01–02 ldapsearch screenshots
Commands:
  ldapsearch -x -H ldap://192.168.56.10 -b "DC=sec,DC=lab"
  ldapsearch -x -H ldaps://192.168.56.10 -b "DC=sec,DC=lab"
Result:
  No entries returned; connection failures.
Why:
• Anonymous LDAP binds disabled.
• Firewall prevents LDAP/LDAPS probes from Kali.

------------------------------------------------------------
Attack 9 – Nmap Reconnaissance Denied / Limited
Folder: attack-9-nmap-recon-denied

01-nmap_syn_fullscan_denied.PNG
Command:
  sudo nmap -sS -Pn 192.168.56.10 -p- --reason
Result:
  All ports filtered.
Why:
• Firewall is filtering all inbound TCP from Kali.

02-nmap_version_detection_denied.PNG
Command:
  sudo nmap -sV -Pn 192.168.56.10 --version-all --reason
Result:
  No banners; everything filtered.
Why:
• Prevents service fingerprinting.

03-nmap_udp_keyports_denied.PNG
Command:
  sudo nmap -sU -Pn 192.168.56.10 -p 53,88,389,445 --reason
Result:
  Ports appear open|filtered.
Why:
• UDP services refuse unauthenticated probing.

------------------------------------------------------------
Overall Summary
Every attack carried out from Kali was denied.  
This confirms that the following hardening measures were effective:

• Firewall restrictions  
• NTLM hardening  
• LDAP/LDAPS restrictions  
• SMB lockdown  
• Kerberos protection  
• Password/lockout policies  
• LLMNR/NBT-NS disabled  
• DNS hardening  
• Strong OU-based GPO configurations  

The domain controller and workstation expose no useful information to unauthenticated attackers, demonstrating a strong defensive security posture.
