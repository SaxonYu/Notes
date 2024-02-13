* Active Directory (AD): a distributed, hierarchical structure that allows for centralized management of an organization's resources, including users, computers, groups, network devices and file shares, group policies, servers and workstations, and trusts. 
	* provides authentication and authorization functions within a Windows domain environment. 
	* A directory service, such as Active Directory Domain Services (AD DS) stores information such as usernames and passwords and manages the rights needed for authorized users to access this information.
# AD Structure
- Active Directory is arranged in a hierarchical tree structure, with a forest at the top containing one or more domains, which can themselves have nested subdomains.
	- It is common to see multiple domains (or forests) linked together via trust relationships.<br>![[Pasted image 20230813201743.png]]
		- users in *INLANEFREIGHT.LOCAL* can access resources in *FREIGHTLOGISTICS.LOCAL* and vice versa but child domains in *INLANEFREIGHT.LOCAL* do not necessarily have trusts established with the child domains in *FREIGHTLOGISTICS.LOCAL*. 
# AD Terminology
#### Object
```
An object can be defined as ANY resource present within an Active Directory environment such as OUs, printers, users, domain controllers, etc.
```
#### Attributes
```
Every object in Active Directory has an associated set of attributes used to define characteristics of the given object. For example, a computer object contains attributes such as the hostname and DNS name. 
```
#### Schema
```
It defines what types of objects can exist in the AD database and their associated attributes, and lists definitions corresponding to AD objects and holds information about each object. 
```
#### Domain
```
A domain is a logical group of objects such as computers, users, OUs, groups, etc. Domains can operate entirely independently of one another or be connected via trust relationships.
```
#### Forest
```
A forest is a collection of Active Directory domains. Each forest operates independently but may have various trust relationships with other forests.
```
#### Tree
```
A tree is a collection of Active Directory domains that begins at a single root domain. A parent-child trust relationship is formed when a domain is added under another domain in a tree.
``` 
#### Global Unique Identifier (GUID)
```
Every single object created by Active Directory is assigned a unique 128-bit GUID 
```
#### Security principals
```
Anything that the operating system can authenticate, including users, computer accounts, or even threads/processes that run in the context of a user or computer account (i.e., an application such as Tomcat running in the context of a service account within the domain). In AD, security principles are domain objects that can manage access to other resources within the domain.
```
#### Security Identifier (SID)
```
A unique identifier for a security principal or security group and, in an AD environment, is issued by the domain controller and stored in a secure database.
```
#### Distinguished Name (DN)
```
A Distinguished Name (DN) describes the full path to an object in AD (such as 'cn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local'). 
```

Given DN  'cn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local':
#### Relative Distinguished Name (RDN)
```
A Relative Distinguished Name (RDN) is a single component of the Distinguished Name that identifies the object as unique from other objects at the current level in the naming hierarchy. For example, the object `cn=bjones,dc=dev,dc=inlanefreight,dc=local` would be recognized as different from `cn=bjones,dc=inlanefreight,dc=local`.
```
#### sAMAccountName
```
User's logon name. Here it would just be `bjones`.
```
#### userPrincipalName
```
An attribute to identify users in AD. This attribute consists of a prefix (the user account name) and a suffix (the domain name) in the format of `bjones@inlanefreight.local`. 
```
#### FSMO Roles
```
To resolve this single point of failure model, Microsoft separated the various responsibilities that a DC can have into Flexible Single Master Operation (FSMO) roles. There are five FMSO roles: Schema Master and Domain Naming Master(one of each per forest), Relative ID (RID) Master(one per domain), Primary Domain Controller (PDC) Emulator(one per domain), and Infrastructure Master(one per domain). All five roles are assigned to the first DC in the forest root domain in a new AD forest. Each time a new domain is added to a forest, only the RID Master, PDC Emulator, and Infrastructure Master roles are assigned to the new domain. 
```
#### Global Catalog
```
The GC is a domain controller that stores a full copy of all objects in the current domain and a partial copy of objects that belong to other domains in the forest. The GC allows both users and applications to find information about any objects in ANY domain in the forest. GC is a feature that can be enabled on a domain controller.
```
#### Read-Only Domain Controller (RODC)
```
A read-only Active Directory database. No AD account passwords are cached on an RODC (other than the RODC computer account & RODC KRBTGT passwords) .
```
#### Service Principal Name (SPN)
```
Uniquely identifies a service instance. They are used by Kerberos authentication to associate an instance of a service with a logon account, allowing a client application to request the service to authenticate an account without needing to know the account name.
```
#### Group Policy Object (GPO)
```
Virtual collections of policy settings. Each GPO has a unique GUID. GPO settings can be applied to both user and computer objects. They can be applied to all users and computers within the domain or defined more granularly at the OU level.
```
#### Access Control Entities (ACEs)
```
Each ONE identifies a trustee (user account, group account, or logon session) and lists the access rights that are allowed, denied, or audited for the given trustee.
```
#### Access Control List (ACL)
```
An ordered collection of Access Control Entities (ACEs) that apply to an object.
```
#### Discretionary Access Control List (DACL)
```
DACLs define which security principles are granted or denied access to an object; it contains a list of ACEs. When a process tries to access a securable object, the system checks the ACEs in the object's DACL in sequentially order to determine whether or not to grant access.
```
#### System Access Control Lists (SACL)
```
Allows for administrators to log access attempts that are made to secured objects. ACEs specify the types of access attempts that cause the system to generate a record in the security event log.
```
#### Fully Qualified Domain Name (FQDN)
```
An FQDN is the complete name for a specific computer or host. It is written with the hostname and domain name in the format [host name].[domain name].[tld]. The FQDN can be used to locate hosts in an Active Directory without knowing the IP address. An example would be the host `DC01` in the domain `INLANEFREIGHT.LOCAL`. The FQDN here would be `DC01.INLANEFREIGHT.LOCAL`.
```
#### AD Recycle Bin
```
When the AD Recycle Bin is enabled, any deleted objects are preserved for a period of time, facilitating restoration if needed. The biggest advantage of using the AD Recycle Bin is that most of a deleted object's attributes are preserved, which makes it far easier to fully restore a deleted object to its previous state.
```
#### Tombstone
```
A container object in AD that holds deleted AD objects. If an object is deleted in a domain that does not have an AD Recycle Bin, it will become a tombstone object. When this happens, the object is stripped of most of its attributes and placed in the `Deleted Objects` container for the duration of the `tombstoneLifetime`. It can be recovered, but any attributes that were lost can no longer be recovered.
```
#### SYSVOL
```
The folder, or share, stores copies of public files in the domain such as system policies, Group Policy settings, logon/logoff scripts, and often contains other types of scripts that are executed to perform various tasks in the AD environment. 
```
#### AdminSDHolder
```
The object is used to manage ACLs for members of built-in groups in AD marked as privileged. The SDProp (SD Propagator) process runs on a schedule on the PDC Emulator Domain Controller. When this process runs, it checks members of protected groups to ensure that the correct ACL is applied to them. It runs every hour by default. 
```

```
For example, suppose an attacker is able to create a malicious ACL entry to grant a user certain rights over a member of the Domain Admins group. In that case, unless they modify other settings in AD, these rights will be removed (and they will lose any persistence they were hoping to achieve) when the SDProp process runs on the set interval.
```
#### dsHeuristics
```
The attribute is a string value set on the Directory Service object used to define multiple forest-wide configuration settings. One of these settings is to exclude built-in groups from the Protected Groups list. Groups in this list are protected from modification via the `AdminSDHolder` object.
```

```
If a group is excluded via the `dsHeuristics` attribute, then any changes that affect it will not be reverted when the SDProp process runs.
```
#### adminCount
```
The attribute determines whether or not the SDProp process protects a user. If the value is set to `0` or not specified, the user is not protected.
```

```
Attackers will often look for accounts with the `adminCount` attribute set to `1` to target in an internal environment. These are often privileged accounts and may lead to further access or full domain compromise.
```
#### Active Directory Users and Computers (ADUC)
```
ADUC is a GUI console commonly used for managing users, groups, computers, and contacts in AD. Changes made in ADUC can be done via PowerShell as well.
```
#### ADSI Edit
```
ADSI Edit is a GUI tool used to manage objects in AD. It provides access to far more than is available in ADUC and can be used to set or delete any attribute available on an object, add, remove, and move objects as well.
```
#### sIDHistory
```
The attribute holds any SIDs that an object was assigned previously. It is usually used in migrations so a user can maintain the same level of access when migrated from one domain to another. 
```

```
This attribute can potentially be abused if set insecurely, allowing an attacker to gain prior elevated access that an account had before a migration if SID Filtering is not enabled.
```
#### NTDS.DIT
```
The NTDS.DIT file can be considered the heart of Active Directory. It is stored on a Domain Controller at `C:\Windows\NTDS\` and is a database that stores AD data such as information about user and group objects, group membership, and, most important to attackers and penetration testers, the password hashes for all users in the domain. 
```
# AD Objects
#### Users
```
Users are considered `leaf objects`, which means that they cannot contain any other objects within them. A user object is considered a security principal and has a security identifier (SID) and a global unique identifier (GUID). User objects have many possible attributes, such as their display name, last login time, date of last password change, email address, account description, manager, address, and more.
```
#### Contacts
```
A contact object is usually used to represent an external user and contains informational attributes such as first name, last name, email address, telephone number, etc. They are `leaf objects` and are NOT security principals (securable objects), so they don't have a SID, only a GUID.
```
#### Printers
```
Like a contact, a printer is a `leaf object` and not a security principal, so it only has a GUID. Printers have attributes such as the printer's name, driver information, port number, etc.

```
#### Computers
```
Computers are `leaf objects` because they do not contain other objects. However, they are considered security principals and have a SID and a GUID. 
```
#### Shared Folders
```
Shared folders are NOT security principals and only have a GUID. A shared folder's attributes can include the name, location on the system, security access rights.
```

```
Shared folders can have stringent access control applied to them and can be either accessible to everyone (even those without a valid AD account), open to only authenticated users (which means anyone with even the lowest privileged user account OR a computer account (`NT AUTHORITY\SYSTEM`) could access it), or be locked down to only allow certain users/groups access. 
```
#### Groups
```
A group is considered a `container object` because it can contain other objects, including users, computers, and even other groups. A group is regarded as a security principal and has a SID and a GUID. Groups in AD can have many attributes, the most common being the name, description, membership, and other groups that the group belongs to.
```
#### Organizational Units (OUs)
```
A container that systems administrators can use to store similar objects for ease of administration. OUs are often used for administrative delegation of tasks without granting a user account full administrative rights. A few OU attributes include its name, members, security settings, and more.
```
#### Domain
```
A domain is the structure of an AD network. Domains contain objects such as users and computers, which are organized into container objects: groups and OUs. Every domain has its own separate database and sets of policies that can be applied to any and all objects within the domain. 
```
#### Domain Controllers
```
Domain Controllers are essentially the brains of an AD network. They handle authentication requests, verify users on the network, and control who can access the various resources in the domain. All access requests are validated via the domain controller and privileged access requests are based on predetermined roles assigned to users. It also enforces security policies and stores information about every other object in the domain.
```
#### Sites
```
A site in AD is a set of computers across one or more subnets connected using high-speed links. They are used to make replication across domain controllers run efficiently.
```
#### Built-in
```
In AD, built-in is a container that holds default groups in an AD domain. They are predefined when an AD domain is created.
```
#### Foreign Security Principals
```
A foreign security principal (FSP) is an object created in AD to represent a security principal that belongs to a trusted external forest.
```
# AD Functionality
#### Flexible Single Master Operation(FSMO) Roles
|**Roles**|**Description**|
|---|---|
|`Schema Master`|This role manages the read/write copy of the AD schema, which defines all attributes that can apply to an object in AD.|
|`Domain Naming Master`|Manages domain names and ensures that two domains of the same name are not created in the same forest.|
|`Relative ID (RID) Master`|The RID Master assigns blocks of RIDs to other DCs within the domain that can be used for new objects. The RID Master helps ensure that multiple objects are not assigned the same SID. Domain object SIDs are the domain SID combined with the RID number assigned to the object to make the unique SID.|
|`PDC Emulator`|The host with this role would be the authoritative DC in the domain and respond to authentication requests, password changes, and manage Group Policy Objects (GPOs). The PDC Emulator also maintains time within the domain.|
|`Infrastructure Master`|This role translates GUIDs, SIDs, and DNs between domains. This role is used in organizations with multiple domains in a single forest. The Infrastructure Master helps them to communicate. If this role is not functioning properly, Access Control Lists (ACLs) will show SIDs instead of fully resolved names.|
#### Trusts
```
A trust is used to establish `forest-forest` or `domain-domain` authentication, allowing users to access resources in (or administer) another domain outside of the domain their account resides in.
```
* Trusts can be transitive or non-transitive.
	- A transitive trust means that trust is extended to objects that the child domain trusts.
	- In a non-transitive trust, only the child domain itself is trusted.
* Trusts can be set up to be one-way or two-way (bidirectional).
	- In bidirectional trusts, users from both trusting domains can access resources.
	- In a one-way trust, only users in a trusted domain can access resources in a trusting domain, not vice-versa. The direction of trust is opposite to the direction of access.
|**Trust Type**|**Description**|
|---|---|
|`Parent-child`|Domains within the same forest. The child domain has a two-way transitive trust with the parent domain.|
|`Cross-link`|a trust between child domains to speed up authentication.|
|`External`|A non-transitive trust between two separate domains in separate forests which are not already joined by a forest trust. This type of trust utilizes SID filtering.|
|`Tree-root`|a two-way transitive trust between a forest root domain and a new tree root domain. They are created by design when you set up a new tree root domain within a forest.|
|`Forest`|a transitive trust between two forest root domains.|