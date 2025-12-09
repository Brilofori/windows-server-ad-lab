Kali Attack Evidence – Denied Attempts (Student Style)

This document lists each attack attempted from the Kali attacker machine and explains how the hardening on the Windows Server Active Directory environment stopped the attack. The goal is to show what was tried, what happened, and why the attack failed.

------------------------------------------------------------
Attack 1 – SMB and LDAP Enumeration Denied
Folder: attack-1-enumeration-denied

1. 01-smbclient_denied.PNG
Command:
    smbclient -L //192.168.56.10/ -N
Result:
    NT_STATUS_ACCESS_DENIED or timeout, no shares listed.
Why it matters:

    • Anonymous SMB enumeration is blocked.
    • Guest and anonymous restrictions + firewall prevent attackers from seeing shares.

3. 02-enum4linux_denied.PNG
Command:
    enum4linux-ng -A 192.168.56.10
Result:
    All SMB/LDAP probes time out; tool stops with no data.
Why it matters:
    • enum4linux cannot fetch users, groups, or shares.
    • Confirms enumeration is blocked at multiple levels.

4. 03-cme_initialization.PNG
Command:
    crackmapexec smb 192.168.56.10 -u '' -p ''
Result:
    Only the tool initialization appears.
Why it matters:

    • Shows the tool is working normally, so future denied attempts are valid.

6. 04-cme_denied_no_output.PNG
Command:
    crackmapexec smb 192.168.56.10 -u '' -p ''
Result:
    Silent exit, nothing returned.
Why it matters:

    • Anonymous enumeration is completely blocked.

------------------------------------------------------------
Attack 2 – Single User, Anonymous and Wrong Password Attempts Denied
Folder: attack-2-single-user-auth-denied

1. 01-cme_null_user_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u '' -p ''
Result:
    Null session denied.
Why it matters:

    • Guest/anonymous logon attempts are blocked.

3. 02-cme_singleuser_wrongpass_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u john.staff -p ilovemyWIFE
Result:
    No authentication feedback or success.
Why it matters:

    • Wrong password attempts return generic errors.
    • Hardening prevents giving away username validity.

------------------------------------------------------------
Attack 3 – Username Enumeration Denied
Folder: attack-3-username-enumeration-denied

1. 01-cme_userlist_nullpass_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u userlist.txt -p ''
Result:
    No difference between valid or invalid usernames.
Why it matters:

    • Prevents attackers from identifying real accounts.

3. 02-cme_userlist_wrongpass_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u userlist.txt -p WrongPassword123
Result:
    All responses look the same.
Why it matters:

    • Password sprays cannot be used to discover live accounts.

userlist.txt:
    File containing usernames used for testing.

------------------------------------------------------------
Attack 4 – Password Spray Denied
Folder: attack-4-password-spray-denied

1. 01-cme_password_spray_Password123_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u userlist.txt -p Password123

2. 02-cme_password_spray_Winter2024_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u userlist.txt -p Winter2024

Result:
    No logons succeed.
Why it matters:

    • Strong password policies and lockout rules protect accounts.

3. 03-cme_singleuser_password_spray_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u john.staff -p Password1!
Result:
    No success returned.
Why it matters:

    • Shows even targeted sprays fail.

------------------------------------------------------------
Attack 5 – Fake Hash and Kerberos Ticket Abuse Denied
Folder: attack-5-fake-hash-denied

1. 01-cme_fake_hash_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u john.staff -H 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'
Result:
    No session opened.
Why it matters:

    • Fake NTLM hashes cannot be used for pass-the-hash.

3. 02-cme_kerberos_fake_tgt_denied.PNG
Command:
    crackmapexec smb 192.168.56.10 -u john.staff -k
Result:
    No Kerberos session created.
Why it matters:

    • Without a valid ticket, Kerberos authentication fails immediately.

------------------------------------------------------------
Attack 6 – Kerberos Ticket and SPN Abuse Denied
Folder: attack-6-kerberos-ticket-attacks-denied

1. 01-getuserspns_blank_creds_denied.PNG
Command:
    python3 GetUserSPNs.py sec.lab/'' -dc-ip 192.168.56.10
Result:
    Connection refused.
Why it matters:

    • SPN enumeration blocked by firewall.

3. 02-getTGT_connection_refused.PNG
Command:
    getTGT.py sec.lab/bril:'batman11'
Result:
    Connection refused.
Why it matters:

    • Direct Kerberos requests are blocked.

5. 02_port88_connection_refused.PNG
Command:
    nc -vz 192.168.56.10 88
Result:
    Connection refused.
Why it matters:

    • Shows port 88 (Kerberos) is blocked externally.

7. 03-responder_block_evidence.PNG
Context:
    KDC services running on DC while Kali shows ports blocked.
Why it matters:
    • Confirms network hardening, not server failure.

------------------------------------------------------------
Attack 7 – LLMNR/NBT-NS Poisoning and DNS Zone Transfer Denied
Folder: attack-7-LLMNR-NBTNS-poisoning-denied

1. 01-dig_axfr_denied.png
Command:
    dig AXFR sec.lab @192.168.56.10
Result:
    Zone transfer blocked.
Why it matters:

    • DNS data cannot be leaked to attackers.

------------------------------------------------------------
Attack 8 – LDAP / LDAPS Enumeration Denied
Folder: attack-8-LDAPLDAPS-Enumeration-denied

1. 01-ldapsearch_ldap_denied.PNG
Command:
    ldapsearch -x -H ldap://192.168.56.10 -b "DC=sec,DC=lab"

2. 02-ldapsearch_ldaps_denied.PNG
Command:
    ldapsearch -x -H ldaps://192.168.56.10 -b "DC=sec,DC=lab"

Result:
    No entries, timeouts.
Why it matters:

    • LDAP and LDAPS enumeration from unauthorized sources is blocked.

------------------------------------------------------------
Attack 9 – Nmap Reconnaissance Denied / Limited
Folder: attack-9-nmap-recon-denied

1. 01-nmap_syn_fullscan_denied.PNG
Command:
    sudo nmap -sS -Pn 192.168.56.10 -p- --reason
Result:
    All ports filtered.
Why it matters:

    • DC gives no service visibility.

3. 02-nmap_version_detection_denied.PNG
Command:
    sudo nmap -sV -Pn 192.168.56.10 --version-all --reason
Result:
    No banners or services discovered.
Why it matters:

    • Service fingerprinting completely blocked.

5. 03-nmap_udp_keyports_denied.PNG
Command:
    sudo nmap -sU -Pn 192.168.56.10 -p 53,88,389,445 --reason
Result:
    open|filtered, no responses.
Why it matters:

    • Key domain services cannot be probed by attackers.

------------------------------------------------------------
Overall Summary
All offensive attempts failed. This shows:
• Firewall and network hardening worked.
• NTLM and Kerberos protections blocked authentication abuse.
• LDAP, SMB, DNS, and port-based enumeration failed.
• Workstation and domain settings prevented username/password discovery.
• The domain controller appears opaque to an attacker with no credentials.

The hardening effectively stops an attacker from gaining any useful information or foothold.

