# User and Machine Accounts
- When a user logs in, the system verifies their password and creates an access token, which describes the security content of a process or thread and includes the user's security identity and group membership. 
	- Whenever a user interacts with a process, access token is presented.
#### Local Accounts
- Local user accounts are considered security principals but can only manage access to and secure resources on a standalone host.
	-  can be assigned rights on that host either individually or via group membership.
	-  Any rights assigned can only be granted to that specific host and will not work across the domain.
- Default Local User Accounts:
	- `Administrator`: The account has the SID `S-1-5-domain-500` and is the first account created with a new Windows installation. 
		- It has full control over almost every resource on the system.
		- It cannot be deleted or locked, but it can be disabled or renamed.
	- `Guest`:  The purpose of this account is to allow users without an account on the computer to log in temporarily with limited access rights.
		- is disabled by default.  
		- By default, it has a blank password and is generally recommended to be left disabled because of the security risk of allowing anonymous access to a host.
	- `NT AUTHORITY\SYSTEM` : The account on a Windows host is the default account installed and used by the operating system to perform many of its internal functions. 
		- It does not appear in User Manager and cannot be added to any groups. 
		- A `NT AUTHORITY\SYSTEM` account is the highest permission level one can achieve on a Windows host and, by default, is granted Full Control permissions to all files on a Windows system.
	- `Network Service`:  A predefined local account used by the Service Control Manager (SCM) for running Windows services. 
		- When a service runs in the context of this particular account, it will present credentials to remote services.
	- `Local Service`:  A predefined local account used by the Service Control Manager (SCM) for running Windows services. 
		- It is configured with minimal privileges on the computer and presents anonymous credentials to the network.
#### Domain Users 
- Domain user accounts can log in to any host in the domain. 
	- One account to keep in mind is the `KRBTGT` account. This is a type of local account built into the AD infrastructure. This account acts as a service account for the Key Distribution service providing authentication and access for domain resources. 
#### User Naming Attributes
- Security in Active Directory can be improved using a set of user naming attributes to help identify user objects like logon name or ID.

|    |    |
|---|---|
|`UserPrincipalName` (UPN)|This is the primary logon name for the user. By convention, the UPN uses the email address of the user.|
|`ObjectGUID`|This is a unique identifier of the user. In AD, the ObjectGUID attribute name never changes and remains unique even if the user is removed.|
|`SAMAccountName`|This is a logon name that supports the previous version of Windows clients and servers.|
|`objectSID`|The user's Security Identifier (SID). This attribute identifies a user and its group memberships during security interactions with the server.|
|`sIDHistory`|This contains previous SIDs for the user object if moved from another domain and is typically seen in migration scenarios from domain to domain. After a migration occurs, the last SID will be added to the `sIDHistory` property, and the new SID will become its `objectSID`.|
#### Domain-joined vs. Non-Domain-joined Machines
- `Domain joined`:  A host joined to a domain will acquire any configurations or changes necessary through the domain's Group Policy. 
	- The benefit here is that a user in the domain can log in and access resources from any host joined to the domain, not just the one they work on. 
	- This is the typical setup you will see in enterprise environments.
- `Non-domain joined`: Non-domain joined computers or computers in a workgroup are not managed by domain policy. 
	- The advantage of this setup is that the individual users are in charge of any changes they wish to make to their host. 
	- This is fine for computers meant for home use or small business clusters on the same LAN. 
# AD Groups
- Difference Between Groups and Organizational Units(OUs): 
	- OUs can be used to delegate administrative tasks to a user, such as resetting passwords or unlocking user accounts without giving them additional admin rights that they may inherit through group membership.
	- Groups are primarily used to assign permissions to access resources.
#### Types of Groups
- Groups in Active Directory have two fundamental characteristics: `type` and `scope`. 
	- `type` : defines the group's purpose. There are two main types: `security` and `distribution` groups.
		- `security`: primarily for ease of assigning permissions and rights to a collection of users instead of one at a time.  All users added to a security group will inherit any permissions assigned to the group.
		- `distribution`: is used by email applications such as Microsoft Exchange to distribute messages to group members. This type of group cannot be used to assign permissions to resources in a domain environment.
	- `scope`: shows how the group can be used within the domain or forest. There are three different scopes: `Domain Local`,  `Global`,  and `Universal`.
		- `Domain Local`: Domain local groups can only be used to manage permissions to domain resources in the domain where it was created. Local groups cannot be used in other domains but **CAN** contain users from **OTHER** domains. 
			- Local groups can be nested into (contained within) other local groups but `NOT` within global groups.
		- `Global`: Global groups can be used to grant access to resources in `another domain`. A global group can only contain accounts from the domain where it was created. Global groups can be added to both other global groups and local groups.
		- `Universal`: Universal groups can be used to manage resources distributed across multiple domains and can be given permissions to any object within the same forest. They are available to all domains within an organization and can contain users from any domain.
			- universal groups are stored in the Global Catalog (GC), and adding or removing objects from a universal group triggers forest-wide replication.
			- It is recommended that administrators maintain other groups (such as global groups) as members of universal groups to optimize replication traffic.
#### Nested Group Membership
- Through nested group membership, a user may inherit privileges not assigned directly to their account or even the group they are directly a member of, but rather the group that their group is a member of.
####  Important Group Attributes
- Some of the most [important group attributes](https://docs.microsoft.com/en-us/windows/win32/ad/group-objects) include:
	- `cn`: The `cn` or Common-Name is the name of the group in Active Directory Domain Services.
	- `member`: Which user, group, and contact objects are members of the group.
	- `groupType`: An integer that specifies the group type and scope.
	- `memberOf`: A listing of any groups that contain the group as a member (nested group membership).
	- `objectSid`: This is the security identifier or SID of the group, which is the unique value used to identify the group as a security principal.
#### Built-in AD Groups
|Group Name|Description|
|---|---|
|`Account Operators`|Members can create and modify most types of accounts, including those of users, local groups, and global groups, and members can log in locally to domain controllers. They cannot manage the Administrator account, administrative user accounts, or members of the Administrators, Server Operators, Account Operators, Backup Operators, or Print Operators groups.|
|`Administrators`|Members have full and unrestricted access to a computer or an entire domain if they are in this group on a Domain Controller.|
|`Backup Operators`|Members can back up and restore all files on a computer, regardless of the permissions set on the files. Backup Operators can also log on to and shut down the computer. Members can log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, which, if taken, can be used to extract credentials and other juicy info.|
|`DnsAdmins`|Members have access to network DNS information. The group will only be created if the DNS server role is or was at one time installed on a domain controller in the domain.|
|`Domain Admins`|Members have full access to administer the domain and are members of the local administrator's group on all domain-joined machines.|
|`Domain Computers`|Any computers created in the domain (aside from domain controllers) are added to this group.|
|`Domain Controllers`|Contains all DCs within a domain. New DCs are added to this group automatically.|
|`Domain Guests`|This group includes the domain's built-in Guest account. Members of this group have a domain profile created when signing onto a domain-joined computer as a local guest.|
|`Domain Users`|This group contains all user accounts in a domain. A new user account created in the domain is automatically added to this group.|
|`Enterprise Admins`|Membership in this group provides complete configuration access within the domain. The group only exists in the root domain of an AD forest. Members in this group are granted the ability to make forest-wide changes such as adding a child domain or creating a trust. The Administrator account for the forest root domain is the only member of this group by default.|
|`Event Log Readers`|Members can read event logs on local computers. The group is only created when a host is promoted to a domain controller.|
|`Group Policy Creator Owners`|Members create, edit, or delete Group Policy Objects in the domain.|
|`Hyper-V Administrators`|Members have complete and unrestricted access to all the features in Hyper-V. If there are virtual DCs in the domain, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.|
|`IIS_IUSRS`|This is a built-in group used by Internet Information Services (IIS), beginning with IIS 7.0.|
|`Pre–Windows 2000 Compatible Access`|This group exists for backward compatibility for computers running Windows NT 4.0 and earlier. Membership in this group is often a leftover legacy configuration. It can lead to flaws where anyone on the network can read information from AD without requiring a valid AD username and password.|
|`Print Operators`|Members can manage, create, share, and delete printers that are connected to domain controllers in the domain along with any printer objects in AD. Members are allowed to log on to DCs locally and may be used to load a malicious printer driver and escalate privileges within the domain.|
|`Protected Users`|Members of this [group](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#protected-users) are provided additional protections against credential theft and tactics such as Kerberos abuse.|
|`Read-only Domain Controllers`|Contains all Read-only domain controllers in the domain.|
|`Remote Desktop Users`|This group is used to grant users and groups permission to connect to a host via Remote Desktop (RDP). This group cannot be renamed, deleted, or moved.|
|`Remote Management Users`|This group can be used to grant users remote access to computers via [Windows Remote Management (WinRM)](https://docs.microsoft.com/en-us/windows/win32/winrm/portal)|
|`Schema Admins`|Members can modify the Active Directory schema, which is the way all objects with AD are defined. This group only exists in the root domain of an AD forest. The Administrator account for the forest root domain is the only member of this group by default.|
|`Server Operators`|This group only exists on domain controllers. Members can modify services, access SMB shares, and backup files on domain controllers. By default, this group has no members.|