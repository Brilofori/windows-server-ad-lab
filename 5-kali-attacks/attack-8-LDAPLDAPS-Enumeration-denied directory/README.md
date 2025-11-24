**Goal:**  
Attempt to enumerate LDAP information anonymously over ports 389 (LDAP) and 636 (LDAPS).

### Command 1 (LDAP:389)
ldapsearch -x -H ldap://192.168.56.10 -s base

**Result:**  
Connection failed. No LDAP information was returned.

### Command 2 (LDAPS:636)
ldapsearch -x -H ldaps://192.168.56.10 -s base

**Result:**  
Connection refused / TLS handshake failed. LDAPS is not accessible.

### Conclusion
LDAP and LDAPS enumeration attempts were blocked.  
This confirms firewall rules + hardened AD DS configuration are preventing anonymous LDAP access.
