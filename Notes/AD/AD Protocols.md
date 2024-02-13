- Active Directory specifically requires;
	- Lightweight Directory Access Protocol (LDAP)
	- Microsoft's version of Kerberos 
	- DNS for authentication and communication,
	- MSRPC which is the Microsoft implementation of Remote Procedure Call (RPC)
# Kerberos
- Kerberos is a stateless authentication protocol based on tickets instead of transmitting user passwords over the network.
	- runs on both TCP/UDP port 88.
-  Authentication Process:
```
1. The user logs on, and their password is converted to an NTLM hash, which is used to encrypt the TGT ticket. This decouples the user's credentials from requests to resources.

2. The KDC service on the DC checks the authentication service request (AS-REQ), verifies the user information, and creates a Ticket Granting Ticket (TGT), which is delivered to the user.

3. The user presents the TGT to the DC, requesting a Ticket Granting Service (TGS) ticket for a specific service. This is the TGS-REQ. If the TGT is successfully validated, its data is copied to create a TGS ticket.

4. The TGS is encrypted with the NTLM password hash of the service or computer account in whose context the service instance is running and is delivered to the user in the TGS_REP.

5. The user presents the TGS to the service, and if it is valid, the user is permitted to connect to the resource (AP_REQ).
```
- <br>![[Pasted image 20230815212402.png]]

# NTLM Authentication
- Active Directory uses several other authentication methods including LM, NTLM, NTLMv1, and NTLMv2. LM and NTLM(NT hash) here are the hash names, and NTLMv1 and NTLMv2 are authentication protocols that utilize the LM or NT hash.
#### NTLMv1 (Net-NTLMv1)
-  The server sends the client an 8-byte random number (challenge), and the client returns a 24-byte response.
- Version 1 is deprecated, but might still be used in some old systems on the network.
```bash
C = 8-byte server challenge, random
K1 | K2 | K3 = LM/NT-hash | 5-bytes-0
response = DES(K1,C) | DES(K2,C) | DES(K3,C)
```
#### NTLMv2 (Net-NTLMv2)
-  The concept is the same as NTLMv1, only different algorithm and responses sent to the server.
```bash
SC = 8-byte server challenge, random  
CC = 8-byte client challenge, random  
CC* = (X, time, CC2, domain name)  
v2-Hash = HMAC-MD5(NT-Hash, user name, domain name)  
LMv2 = HMAC-MD5(v2-Hash, SC, CC)  
NTv2 = HMAC-MD5(v2-Hash, SC, CC*)  
response = LMv2 | CC | NTv2 | CC*
```
#### Domain Cached Credentials (MSCache2)
- Microsoft developed the [MS Cache v1 and v2](https://webstersprodigy.net/2014/02/03/mscash-hash-primer-for-pentesters/) algorithm (also known as `Domain Cached Credentials` (DCC) to solve the potential issue of a domain-joined host being unable to communicate with a domain controller.
- Hosts save the last `ten` hashes for any domain users that successfully log into the machine in the `HKEY_LOCAL_MACHINE\SECURITY\Cache` registry key. 
	- **Not Directly Usable for PtH**: The hashes stored in this location aren't the typical NTLM hashes that are used in pass-the-hash attacks. Instead, these are a derivative of the user's hash and are combined with the machine account's hash. This makes them unsuitable for direct use in a PtH attack.
	- **Slow to Crack**: The caching mechanism uses PBKDF2 (Password-Based Key Derivation Function 2), which is a cryptographic function designed to be computationally intensive. 
# LDAP
- LDAP is an open-source and cross-platform protocol used for authentication against various directory services (such as AD).
	- LDAP uses port 389.
	- LDAP over TLS/SSL (LDAPS) communicates over port 636.
- In AD environments, the Domain Controller serves as the LDAP server.
- Authentication Process:  
```
 `Simple Authentication`: This includes anonymous authentication, unauthenticated authentication, and username/password authentication. Simple authentication means that a `username` and `password` create a BIND request to authenticate to the LDAP server.
```

```
`SASL Authentication`: The Simple Authentication and Security Layer (SASL) framework uses other authentication services, such as Kerberos, to bind to the LDAP server and then uses this authentication service to authenticate to LDAP.
```
# MSRPC
- Four key RPC interfaces for accessing systems in AD environment.

|Interface Name|Description|
|---|---|
|`lsarpc`|A set of RPC calls to the [Local Security Authority (LSA)](https://networkencyclopedia.com/local-security-authority-lsa/) system which manages the local security policy on a computer, controls the audit policy, and provides interactive authentication services. LSARPC is used to perform management on domain security policies.|
|`netlogon`|Netlogon is a Windows process used to authenticate users and other services in the domain environment. It is a service that continuously runs in the background.|
|`samr`|Remote SAM (samr) provides management functionality for the domain account database, storing information about users and groups. IT administrators use the protocol to manage users, groups, and computers by enabling admins to create, read, update, and delete information about security principles. <br><br>Attackers can use the samr protocol to perform reconnaissance about the internal domain using tools such as [BloodHound](https://github.com/BloodHoundAD/) to visually map out the AD network. Organizations can [protect](https://stealthbits.com/blog/making-internal-reconnaissance-harder-using-netcease-and-samri1o/) against this type of reconnaissance by changing a Windows registry key to only allow administrators to perform remote SAM queries.|
|`drsuapi`|drsuapi is the Microsoft API that implements the Directory Replication Service (DRS) Remote Protocol which is used to perform replication-related tasks across Domain Controllers in a multi-DC environment. <br><br>Attackers can utilize drsuapi to [create a copy of the Active Directory domain database](https://attack.mitre.org/techniques/T1003/003/) (NTDS.dit) file to retrieve password hashes for all accounts in the domain.