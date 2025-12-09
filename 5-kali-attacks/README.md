# Kali Attack Evidence – Denied Attempts
---

## Attack 1 – SMB and LDAP Enumeration Denied

**Folder:** `attack-1-enumeration-denied`  

### 01-smbclient_denied.PNG  
Command (Kali):  
` smbclient -L //192.168.56.10/ -N`  

Result: NT_STATUS_IO_TIMEOUT and no share listing.  
Why it matters:  
- Anonymous SMB enumeration is blocked (firewall + guest/anonymous restrictions).  
- An attacker cannot even see which shares exist.

### 02-enum4linux_denied.PNG  
Command (Kali):  
` enum4linux-ng -A 192.168.56.10`  

Result: Every LDAP/SMB probe times out; tool aborts.  
Why it matters:  
- Combined SMB + LDAP enumeration fails, confirming external enumeration of accounts/shares is blocked.

### 03-cme_initialization.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u '' -p ''` (first run)  

Result: Only CrackMapExec database/workspace initialization output.  
Why it matters:  
- Shows the tool is installed and working, so later “no output” runs are genuine denials, not tool errors.

### 04-cme_denied_no_output.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u '' -p ''` (after init)  

Result: No hosts or shares returned; command exits silently.  
Why it matters:  
- CMEA cannot enumerate anything anonymously against the DC.  
- This pairs with the timeouts above to prove SMB enumeration is effectively shut down.

---

## Attack 2 – Single User, Anonymous and Wrong Password Denied

**Folder:** `attack-2-single-user-auth-denied`  

### 01-cme_null_user_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u '' -p ''`  

Result: No valid sessions; no output beyond prompt.  
Why it matters:  
- Proves null-session style authentication is denied.

### 02-cme_singleuser_wrongpass_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u john.staff -p ilovemyWIFE`  

Result: No success message, no hashes, no shares.  
Why it matters:  
- A direct password guess against a valid username fails with no useful feedback.

---

## Attack 3 – Username Enumeration Denied

**Folder:** `attack-3-username-enumeration-denied`  

### 01-cme_userlist_nullpass_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u userlist.txt -p ''`  

Result: No “valid user” hits; no users differentiated.  
Why it matters:  
- Null-password username-enumeration spray does not reveal which usernames are real.

### 02-cme_userlist_wrongpass_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u userlist.txt -p WrongPassword123`  

Result: All attempts look identical; no indication of valid vs invalid users.  
Why it matters:  
- Password-spray username discovery is blocked; prevents building a clean list of live accounts.

### userlist.txt  
Context file listing candidate usernames used in these sprays.

---

## Attack 4 – Password Spray Denied

**Folder:** `attack-4-password-spray-denied`  

### 01-cme_password_spray_Password123_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u userlist.txt -p Password123`  

### 02-cme_password_spray_Winter2024_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u userlist.txt -p Winter2024`  

Result for both: No successful logons, no sessions opened.  
Why it matters:  
- Common weak passwords sprayed across many users fail, showing strong password / lockout controls.

### 03-cme_singleuser_password_spray_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u john.staff -p Password1!`  

Result: No success or hashes.  
Why it matters:  
- A typical corporate-style password guess against a single user is rejected.

---

## Attack 5 – Fake Hash and Kerberos Ticket Abuse Denied

**Folder:** `attack-5-fake-hash-denied`  

### 01-cme_fake_hash_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u john.staff -H 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'`  

Result: No session established.  
Why it matters:  
- A bogus NTLM hash cannot be used to pass-the-hash against the DC.

### 02-cme_kerberos_fake_tgt_denied.PNG  
Command (Kali):  
` crackmapexec smb 192.168.56.10 -u john.staff -k`  

Result: No Kerberos-based session created.  
Why it matters:  
- Without a valid ticket in the cache, Kerberos auth via CME fails, showing ticket abuse is not trivial.

---

## Attack 6 – Kerberos Ticket and SPN Abuse Denied

**Folder:** `attack-6-kerberos-ticket-attacks-denied`  

### 01-getuserspns_blank_creds_denied.PNG  
Command (Kali):  
` python3 /usr/share/doc/python3-impacket/examples/GetUserSPNs.py sec.lab/'' -dc-ip 192.168.56.10`  

Result: `[Errno 111] Connection refused`.  
Why it matters:  
- SPN enumeration over Kerberos/LDAP from Kali is blocked at the network level.

### 02-getTGT_connection_refused.PNG  
Command (Kali):  
` sudo python3 /usr/share/doc/python3-impacket/examples/getTGT.py sec.lab/bril:'batman11' -dc-ip 192.168.56.10`  

Result: `[Errno 111] Connection refused`.  
Why it matters:  
- Even with valid-looking credentials, Impacket cannot reach the DC’s Kerberos port from Kali.

### 02_port88_connection_refused.PNG  
Command (Kali):  
` nc -vz 192.168.56.10 88`  

Result: TCP connection to port 88 refused.  
Why it matters:  
- Confirms the firewall is blocking Kerberos from Kali, explaining the Impacket failures.

### 03-responder_block_evidence.PNG  
Context screenshot (Windows/Kali):  
- Shows KDC and DNS services running on the DC while Kali reports port 88 as blocked.  
Why it matters:  
- Proves it is a network hardening choice, not a broken DC.

---

## Attack 7 – LLMNR / NBT-NS Poisoning and DNS Zone Transfer Denied

**Folder:** `attack-7-LLMNR-NBTNS-poisoning-denied`  

### 01-dig_axfr_denied.png  
Command (Kali):  
` dig AXFR sec.lab @192.168.56.10`  

Result: “no servers could be reached”; AXFR fails.  
Why it matters:  
- DNS zone transfers from the DC are blocked to Kali.  
- Combined with your Windows-side hardening (LLMNR/NBT-NS disabled), this shows common name-resolution abuse paths are closed.

---

## Attack 8 – LDAP / LDAPS Enumeration Denied

**Folder:** `attack-8-LDAPLDAPS-Enumeration-denied`  

### 01-ldapsearch_ldap_denied.PNG  
Command (Kali):  
` ldapsearch -x -H ldap://192.168.56.10 -b "DC=sec,DC=lab"`  

### 02-ldapsearch_ldaps_denied.PNG  
Command (Kali):  
` ldapsearch -x -H ldaps://192.168.56.10 -b "DC=sec,DC=lab"`  

Result for both: Connections time out / no entries returned.  
Why it matters:  
- Direct LDAP and LDAPS enumeration from Kali is blocked, protecting user and computer objects from unauthenticated discovery.

---

## Attack 9 – Nmap Reconnaissance Denied / Limited

**Folder:** `attack-9-nmap-recon-denied`  

### 01-nmap_syn_fullscan_denied.PNG  
Command (Kali):  
` sudo nmap -sS -Pn 192.168.56.10 -p- --reason`  

Result:  
- Host is detected as up, but all 65,535 TCP ports are shown as filtered (no-response).  
Why it matters:  
- Full TCP SYN scan across all ports yields no usable service data.  
- Confirms the firewall is tightly filtering inbound TCP from Kali.

### 02-nmap_version_detection_denied.PNG  
Command (Kali):  
` sudo nmap -sV -Pn 192.168.56.10 --version-all --reason`  

Result:  
- All of the 1,000 default TCP ports are also filtered (no-response).  
- Version detection completes but finds no banners or services.  
Why it matters:  
- Even targeted version scanning does not leak service information such as HTTP, RDP, SMB, or LDAP banners.

### 03-nmap_udp_keyports_denied.PNG  
Command (Kali):  
` sudo nmap -sU -Pn 192.168.56.10 -p 53,88,389,445 --reason`  

Result:  
- Ports 53, 88, 389, and 445 are reported as `open|filtered` with “no-response.”  
Why it matters:  
- Key UDP ports for DNS, Kerberos, and LDAP do not respond in a useful way to unauthenticated probes.  
- To an attacker, the DC appears highly opaque: they can see a host exists but cannot tell which services are really open.

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
