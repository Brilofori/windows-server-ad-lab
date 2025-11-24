README.md

This directory contains documented offensive security tests performed from the Kali attacker machine.
All attacks were executed against the hardened Windows Server 2022 Domain Controller (192.168.56.10).
Each attack demonstrates how proper security hardening prevents common enumeration and exploitation attempts.
--
Attack 1 – Enumeration (Denied)

Purpose: Test anonymous and unauthenticated enumeration against SMB and LDAP.
Outcome: All methods failed due to firewall restrictions and hardening.

Artifacts:
smbclient anonymous listing denied
enum4linux anonymous enumeration denied
CrackMapExec initialization
CrackMapExec unauthenticated enumeration denied
--
Attack 2 – Single User Authentication Attempt (Denied)

Purpose: Test a login using a single user with blank or wrong passwords.
Outcome: Authentication blocked and no information disclosed.

Artifacts:
CME null-user attempt denied
CME wrong-password attempt denied
--
Attack 3 – Username Enumeration (Denied)

Purpose: Attempt username validation using lists.
Outcome: Server returned no information that would identify valid accounts.

Artifacts:
CME userlist null-password attempts denied
CME userlist wrong-password attempts denied
Username list used for testing
--
Attack 4 – Password Spray (Denied)

Purpose: Test multiple common passwords against multiple accounts.
Outcome: All spray attempts blocked by authentication throttling and NTLM restrictions.

Artifacts:
Password123 spray denied
Winter2024 spray denied
Single-user password-spray attempt denied
--
Attack 5 – Fake Hash / Kerberos Abuse Attempts (Denied)

Purpose: Attempt authentication with invalid NTLM hash and forge Kerberos tickets.
Outcome: All attempts were blocked; server did not reveal protocol-level information.

Artifacts:
Fake NTLM hash attempt denied
Fake Kerberos TGT request denied
--
Attack 6 – Kerberos Ticket Attacks (Denied)

Purpose: Test SPN enumeration and Kerberoasting attempts.
Outcome: All tools failed due to firewall filtering on Kerberos ports.

Artifacts:
GetUserSPNs with blank credentials denied
getTGT connection refused (Kerberos port filtered)
Nmap UDP scan confirming filtered DNS/Kerberos/LDAP ports
Responder block evidence
--
Attack 7 – LLMNR/NBT-NS Poisoning (Denied)

Purpose: Capture hashes via LLMNR spoofing.
Outcome: Server refused all multicast/LLMNR interactions; poisoning unsuccessful.

Artifacts:
DNS zone transfer (AXFR) attempt denied
Responder poisoning attempt denied
--
Attack 8 – LDAP / LDAPS Enumeration (Denied)

Purpose: Attempt LDAP and LDAPS reconnaissance using ldapsearch.
Outcome: Both protocols filtered at firewall; server returned no information.

Artifacts:
LDAP enumeration denied
LDAPS enumeration denied
Attack 9 – Nmap Reconnaissance (Denied)
Purpose: Scan full TCP/UDP port ranges and attempt service fingerprinting.
Outcome: All ports filtered; no banners, service versions, or fingerprints disclosed.

Artifacts:
Full TCP SYN scan (all ports filtered)
Full service/version scan (all ports filtered)
UDP scan of key ports showing open|filtered state
