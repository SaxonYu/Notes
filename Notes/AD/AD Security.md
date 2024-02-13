# AD Rights and Privileges
- `Rights`:  determine what operations a user can perform on a particular AD object.
- `Privileges` : determine what a user is permitted to do on a system-wide level, like shutting down the system, loading device drivers, or changing the system time..
	- In AD, privileges are typically assigned through `User Rights Assignment` in Group Policy.
####  User Rights Assignment
- This Microsoft article on [User Rights Assignment](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment) provides a detailed explanation of each of the user rights that can be set in Windows.

|**Privilege**|**Description**|
|---|---|
|`SeRemoteInteractiveLogonRight`|This privilege could give our target user the right to log onto a host via Remote Desktop (RDP), which could potentially be used to obtain sensitive data or escalate privileges.|
|`SeBackupPrivilege`|This grants a user the ability to create system backups and could be used to obtain copies of sensitive system files that can be used to retrieve passwords such as the SAM and SYSTEM Registry hives and the NTDS.dit Active Directory database file.|
|`SeDebugPrivilege`|This allows a user to debug and adjust the memory of a process. With this privilege, attackers could utilize a tool such as [Mimikatz](https://github.com/ParrotSec/mimikatz) to read the memory space of the Local System Authority (LSASS) process and obtain any credentials stored in memory.|
|`SeImpersonatePrivilege`|This privilege allows us to impersonate a token of a privileged account such as `NT AUTHORITY\SYSTEM`. This could be leveraged with a tool such as JuicyPotato, RogueWinRM, PrintSpoofer, etc., to escalate privileges on a target system.|
|`SeLoadDriverPrivilege`|A user with this privilege can load and unload device drivers that could potentially be used to escalate privileges or compromise a system.|
|`SeTakeOwnershipPrivilege`|This allows a process to take ownership of an object. At its most basic level, we could use this privilege to gain access to a file share or a file on a share that was otherwise not accessible to us.|
## General AD Hardening Measures
####  Local Administrator Password Solution(LAPS)
```
Accounts can be set up to have their password rotated on a fixed interval (i.e., 12 hours, 24 hours, etc.). 
```
#### Audit Policy Settings (Logging and Monitoring)
```
Every organization needs to have logging and monitoring setup to detect and react to unexpected changes or activities that may indicate an attack.
```
#### Group Policy Security Settings
```
Group Policy Objects (GPOs) are virtual collections of policy settings that can be applied to specific users, groups, and computers at the OU level. 
```
- [Account Policies](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/account-policies) - Manage how user accounts interact with the domain. These include the password policy, account lockout policy, and Kerberos-related settings such as the lifetime of Kerberos tickets
- [Local Policies](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/security-options) - These apply to a specific computer and include the security event audit policy, user rights assignments (user privileges on a host), and specific security settings such as the ability to install drivers, whether the administrator and guest accounts are enabled, renaming the guest and administrator accounts, preventing users from installing printers or using removable media, and a variety of network access and network security controls.
- [Software Restriction Policies](https://docs.microsoft.com/en-us/windows-server/identity/software-restriction-policies/software-restriction-policies) - Settings to control what software can be run on a host.
- [Application Control Policies](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/windows-defender-application-control) - Settings to control which applications can be run by certain users/groups. It is common to see organizations block access to CMD and PowerShell (among other executables) for users that do not require them for their day-to-day job. 
- [Advanced Audit Policy Configuration](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/secpol-advanced-security-audit-policy-settings) - A variety of settings that can be adjusted to audit activities such as file access or modification, account logon/logoff, policy changes, privilege usage, and more.
#### Update Management (SCCM/WSUS)
```
A patch management solution can help ensure timely deployment of patches and maximize coverage, making sure that no hosts miss critical security patches.
```

```
The Windows Server Update Service(WSUS) can be installed as a role on a Windows Server and can be used to minimize the manual task of patching Windows systems. THe System Center Configuration Manager(SCCM) is a paid solution that relies on the WSUS Windows Server role being installed and offers more features than WSUS on its own.
```
#### Group Managed Service Accounts (gMSA)
```
A gMSA is an account managed by the domain that offers a higher level of security than other types of service accounts for use with non-interactive applications, services, processes, and tasks that are run automatically but require credentials to run. They provide automatic password management with a 120 character password generated by the domain controller. The password is changed at a regular interval and does not need to be known by any user. It allows for credentials to be used across multiple hosts.
```
#### Security Groups
```
Security groups offer an easy way to assign access to network resources.  Security groups help ensure you can assign granular permissions to users en masse instead of individually managing each user.
```
#### Account Separation
```
Administrators must have two separate accounts. One for their day-to-day work and a second for any administrative tasks they must perform. 
```
#### Password Complexity Policies + Passphrases + 2FA
#### Limiting Domain Admin Account Usage
```
All-powerful Domain Admin accounts should only be used to log in to Domain Controllers, not personal workstations, jump hosts, web servers, etc. 
```
#### Periodically Auditing and Removing Stale Users and Objects
```
It is important for an organization to periodically audit Active Directory and remove or disable any unused accounts. 
```
#### Auditing Permissions and Access
```
Organizations should also periodically perform access control audits to ensure that users only have the level of access required for their day-to-day work.
```
#### Using Restricted Groups
```
Restricted Groups allow for administrators to configure group membership via Group Policy. They can be used for a number of reasons, such as controlling membership in the local administrator's group on all hosts in the domain by restricting it to just the local Administrator account and Domain Admins and controlling membership in the highly privileged Enterprise Admins and Schema Admins groups and other key administrative groups.
```
#### Limiting Server Roles
```
It is important not to install additional roles on sensitive hosts, such as installing the `Internet Information Server` (IIS) role on a Domain Controller. 
```
# Examining Group Policy
#### Group Policy Objects (GPOs)
- A [Group Policy Object (GPO)](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-objects) is a virtual collection of policy settings that can be applied to `user(s)` or `computer(s)`. 
	- GPOs include policies such as screen lock timeout, disabling USB ports, enforcing a custom domain password policy, installing software, managing applications, customizing remote access settings, and much more. 
	- Every GPO has a unique name and is assigned a unique identifier (a GUID). 
	- Every GPO contains one or more Group Policy settings that may apply at the local machine level or within the Active Directory context.
#### GPO Order of Precedence
- GPO settings are processed using the hierarchical structure of AD and are applied using the `Order of Precedence` rule as seen in the table below:

|**Level**|**Description**|
|---|---|
|`Local Group Policy`|The policies are defined directly to the host locally outside the domain. Any setting here will be overwritten if a similar setting is defined at a higher level.|
|`Site Policy`|Any policies specific to the Enterprise Site that the host resides in. Remember that enterprise environments can span large campuses and even across countries. So it stands to reason that a site might have its own policies to follow that could differentiate it from the rest of the organization.|
|`Domain-wide Policy`|Any settings you wish to have applied across the domain as a whole. For example, setting the password policy complexity level, configuring a Desktop background for all users, and setting a Notice of Use and Consent to Monitor banner at the login screen.|
|`Organizational Unit` (OU)|These settings would affect users and computers who belong to specific OUs. You would want to place any unique settings here that are role-specific. For example, the mapping of a particular share drive that can only be accessed by HR, access to specific resources like printers, or the ability for IT admins to utilize PowerShell and command-prompt.|
|`Any OU Policies nested within other OU's`|Settings at this level would reflect special permissions for objects within nested OUs. For example, providing Security Analysts a specific set of Applocker policy settings that differ from the standard IT Applocker settings.|
![[Pasted image 20230819175448.png]]
- When more than one GPO is linked to an OU, they are processed based on the `Link Order`. The GPO with the lowest Link Order is processed last, or the GPO with link order 1 has the highest precedence, then 2, and 3, and so on.
#### Group Policy Refresh Frequency
- When a new GPO is created, the settings are not automatically applied right away. Windows performs periodic Group Policy updates, which by default is done every 90 minutes with a randomized offset of +/- 30 minutes for users and computers. 
	- This random offset of +/- 30 minutes is set to avoid overwhelming domain controllers by having all clients request Group Policy from the domain controller simultaneously.