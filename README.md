Windows Server AD Security Lab – Hardening & Attack Simulation 

Overview
This project is a learning lab where I built a small Windows Server domain, applied security hardening, and then attempted common cyber-attacks from a Kali machine to see how well the environment held up.  

The goal was to understand Active Directory, learn how Group Policy affects users, and practice defensive thinking by trying to break what I built.

I focused on:

• Setting up a domain controller and workstation  
• Creating OUs, users, and groups  
• Applying security hardening  
• Running attacks to verify whether the hardening worked  

This project does NOT include successful privilege escalation (the environment became too hardened), and I decided to keep the project clean rather than force a broken demo.

-----  
1. Lab Setup

Environment:
• Windows Server 2022 – Domain Controller (sec.lab)  
• Windows 10 – Joined to the domain  
• Kali Linux – Attacker system  
• Host‑only network with DHCP provided by the server

Active Directory Structure:
• LAB OU with sub‑OUs for Admins, Staff, Interns, and Workstations  
• Groups created for role‑based access  
• Password and account policies configured  

-----  
2. Security Hardening

This was the main part of the project. I applied:

Account & Authentication Hardening:
• Disabled Guest account and anonymous access  
• Disabled LM hashing  
• Applied stronger password rules  
• Hardened NTLM settings  
• Restricted group memberships  

Workstation & OS Hardening:
• Baseline workstation GPO  
• Intern lockdown (no PowerShell, no regedit, no task manager, restricted UI)  
• Disabled autorun and remote assistance  
• Removed unnecessary features  

Audit & Logging:
• Enabled logs for account logon/logoff  
• Policy changes  
• Object access  
• Account management  

Firewall Hardening:
• Hardened domain/private/public profiles  
• Disabled unused ports and services  
• Disabled SMBv1 and legacy protocols  

Every change is documented with screenshots inside the repo.

-----  
3. Attack Simulations (All Blocked)

I ran nine categories of attacks from Kali Linux:

1. Enumeration (SMB / enum4linux) – Blocked  
2. Single‑user authentication attempts – Blocked  
3. Username enumeration – Blocked  
4. Password spraying – Blocked  
5. Kerberos fake hash / ticket forging – Blocked  
6. Kerberos SPN attacks – Blocked  
7. LLMNR/NBT‑NS poisoning – Blocked  
8. LDAP / LDAPS enumeration – Blocked  
9. Nmap scanning – Attack surface significantly reduced  

Each folder contains:
• Commands used  
• Screenshots  
• What happened  
• Why it was denied  

This section proves the hardening worked.

-----  
4. Key Learnings

• Hardening affects everything — even attacks that “should” work  
• NTLM settings and older Windows features are major attack points  
• One GPO can completely shut down an attacker’s workflow  
• Testing your own defensive setup shows you gaps you didn’t know were there  
• Knowing when to pivot (like dropping privilege escalation) is part of real cyber problem‑solving  

-----  
5. Future Improvements

If I expand this project later, I want to add:
• Centralized logging (WEC/WI or SIEM)  
• Sysmon + Sigma rules  
• MITRE ATT&CK mapping  
• A dedicated privilege escalation project  
• Automation of baselines using PowerShell  

-----  
6. Repo Structure

1‑server‑setup/  
2‑active‑directory/  
3‑workstation‑setup/  
4‑security‑hardening/  
5‑kali‑attacks/  
README.md  

-----  
Conclusion
This project helped me understand how Windows domains work from both an admin and attacker perspective. Building the environment, securing it, and then testing the security gave me a full picture of how defensive layers interact. Even though no attacks succeeded, the failures were the educational part — they showed which controls were actually doing their job.
