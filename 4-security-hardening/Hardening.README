Hardening Evaluation Report – Windows Server AD Security Lab 

Overview
This document evaluates the security hardening I applied in the Windows Server AD Lab and explains how each control contributed to blocking the attacks attempted from the Kali attacker machine.  
The goal of this evaluation is to show which defensive measures were effective, why the attacks failed, and how the environment behaved under realistic offensive testing.

-----

1. Evaluation Approach

I evaluated hardening by:
• Building a Windows domain with Server 2022 and a Windows 10 workstation  
• Applying layered security policies (GPO, NTLM, firewall, account rules)  
• Running nine categories of attacks from a Kali Linux machine  
• Comparing expected behavior vs. actual results  
• Mapping failures back to specific security controls  

This process helped me understand how different Windows security settings protect Active Directory environments.

-----

2. Summary of Hardening Controls

Security controls applied included:

Account & Authentication Controls
• Disabled Guest account  
• Disabled anonymous access  
• Disabled LM hash storage  
• Strong password + lockout policies  
• NTLM hardening (auditing, fallbacks disabled)

Host Hardening
• Workstation baseline GPO applied  
• Intern workstation lockdown (restricted UI, PowerShell disabled, regedit disabled)  
• Removed autorun  
• Disabled remote assistance  

Firewall Hardening
• Hardened domain/private/public profiles  
• Disabled SMBv1  
• Restricted unnecessary ports  

Audit and Logging
• Enabled logging for account logon, logoff  
• Policy change logs  
• Object access logs  
• Account management logs  

Each of these contributed directly to preventing attacks.

-----

3. Attack-by-Attack Evaluation

Below is an explanation of why each attack was blocked based on the hardening configuration.

Attack 1 – Enumeration (SMB, enum4linux)
Result: Blocked  
Reason:  
• Guest and anonymous access disabled  
• NTLM restrictions prevented null-auth enumeration  
• Firewall rules limited SMB interactions  

Attack 2 – Single-User Authentication Attempt
Result: Blocked  
Reason:  
• Password lockout thresholds and strong policies  
• No weak passwords in domain  
• NTLM authentication protections  

Attack 3 – Username Enumeration
Result: Blocked  
Reason:  
• No anonymous or null sessions allowed  
• NTLM auditing and policies prevented disclosure  
• Incorrect authentication attempts returned generic failure responses  

Attack 4 – Password Spraying
Result: Blocked  
Reason:  
• Strong password policy  
• Lockout threshold  
• No accounts with common or default passwords  

Attack 5 – Fake Hash / Fake TGT (Kerberos Abuse)
Result: Blocked  
Reason:  
• NTLM hardening prevents downgrade attacks  
• Kerberos policies enforced secure behavior  
• No exposure of service accounts or SPNs  

Attack 6 – Kerberos Ticket Attacks (getuserspn, getTGT, etc.)
Result: Blocked  
Reason:  
• No SPNs exposed  
• NTLM authentication hardening restricted fallback paths  
• Firewall and domain policies blocked attempts  

Attack 7 – LLMNR / NBT-NS Poisoning
Result: Blocked  
Reason:  
• Workstation baseline policies prevented LLMNR leaks  
• No network traffic vulnerable to responder poisoning  
• Network profile firewall rules further limited exposure  

Attack 8 – LDAP / LDAPS Enumeration
Result: Blocked  
Reason:  
• Anonymous LDAP bind disabled  
• Firewall rules restricted LDAP access  
• Domain policies prevented data exposure  

Attack 9 – Nmap Port Scanning
Result: Significantly reduced attack surface  
Reason:  
• Firewall hardened (domain, private, public profiles)  
• SMBv1 disabled  
• Only essential ports exposed on DC and workstation  

Overall:  
Every attack failed because at least one hardening control specifically countered the technique.

-----

4. Hardening Effectiveness Summary

The following patterns emerged:

• NTLM hardening was extremely effective — it blocked multiple attacks indirectly.  
• Lockdown GPOs on Interns made the workstation resistant to abuse from low-privilege users.  
• Firewall restrictions played a major role in reducing enumeration and port-scanning visibility.  
• Removing legacy protocols (SMBv1, anonymous binds) dramatically increased security posture.  
• Strong passwords + lockout rules completely prevented guess-based attacks.  

The environment behaved like a properly secured enterprise domain.

-----

Conclusion

This evaluation shows that the hardening applied across authentication, Group Policy, firewall, and audit settings worked together to stop all tested attack vectors.  
The project demonstrated not only how to configure defenses, but also how to validate them using attacker tools — giving me a clearer understanding of how Windows security works in practice.

