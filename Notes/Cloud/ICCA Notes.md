# Cloud Management
## Management Foundations
- **Shared Responsibility**
	- Management
		-  IaaS![[Pasted image 20230809152301.png]]
		- PaaS![[Pasted image 20230809152348.png]]
		- SaaS: provider is responsible for managing the full stack but client may be responsible for managing customizations, usage and adhering to good practices and compliance.
	-  Security
		- Provider
			1. Physical security
			2. Infrastructure security
			3. Platform security
			4. Identity system security
			5. ==Standards compliance==
		- Client
			1. Identity security
			2. Data security
			3. Application security
			4. ==Standards compliance==
## Resources Management
* Monitoring
	* Cloud Monitoring
		* Resources Monitoring 
		* System Monitoring
			* Azure Monitor
			* AWS CloudWatch
			* Google Cloud Monitoring
	* Unified Monitoring
		* Nagios
		* Splunk
		* PRTG
* Proactive Resource Management
	* Cloud Alerts
	* Cloud Automation
# Cloud Identity
* Cloud-Based Identity
	* Root User controls
		* Identities (e.g. users, groups)
		* Resources (e.g. ec2, s3 buckets)
	* Identities>Groups>Roles>Resources<br>![[Pasted image 20230810185817.png]]
* Access Management 
	* AWS
		* Users, Groups, Permissions
		* Federated users
		* Policies grant permissions
			* Effect (allow/deny)
			* Action
			* Resources
			* Conditions
	* Azure
		* Users, Groups, Roles
		* Federated users
	* GCP
		* Users, Groups, Roles, Policies
		* Federated users
		* Policies link users to roles on resources
			* User 
			* Role
			* Resources
			* Conditions
# Cloud Security
* Defense in Depth![[Pasted image 20230809213403.png]]
* Common Cloud Attacks
	* Targets:
		* Identities:
			* SaaS identities
			* Cloud platform identities
			* Data plane identities
		* Data
		* Services
	* Methods:
		* Misconfiguration
		* Account Hijacking
		* Service Hijacking
		* Malware
## Identity Protection
* Identity and Access Management(IAM)
	* Managing Cloud Users
		* Minimize "root" user access
		* Organize into groups
		* Use dynamic management when possible
		* Audit and review user configuration
	* Managing Access to Resources
		* Always apply least privilege
		* Use dynamic access policies when possible
		* Audit resource access
		* Separate control plane and data plane access
* Multifactor Authentication
* <br>![[Pasted image 20230810185900.png]]
* Incident Response:
	* The Five R's
		* Revoke, Reset, Review, Remediate, Return
* Best Practice
	* Apply password policy
	* Implement conditional access
	* Implement MFA
	* Monitoring
	* Audit unused accounts
## Resource Protection
### Data Protection
* Data Types: <br>![[Pasted image 20230811185316.png]]
* Data at Rest:
	* Network Controls
	* Permissions
	* Encryption
	* Hardware Security Module (HSM)
	* Replication/Backup 
* Data in Transit:
	* Encryption
* Best Practices:
	* Limit access
		* Resource
		* Data
		* Network
	* Always encrypt
		* At rest
		* In transit
		* End-to-end
	* Protect from failure
		* Replicate
		* Backup
	* Audit
### Network Protection
* Provider Protection:
	* DDoS protection
	* General threat protection
* Tenant Protection:
	* AWS
		* nACL
		* Security Group
	* Azure
		* Network Security Group
	* GCP
		* Firewall rules
* Security Services:
	* <br>![[Pasted image 20230812100921.png]]
* Best Practices
	* Leverage provider tools
	* Limit public attack surface
	* Monitor
	* Alert atypical usage patterns
	* Have a playbook
### Compute Protection
* Infrastructure Compute Protection:
	* Patch management
	* Attack surface minimization
	* OS hardening
	* Resource protection
	* Monitoring
* Platform Compute Protection:
	* What IaaS protection offers along with patch management for services(web server, databases, etc.)
* Confidential Compute:
	* Enclave/Trusted Execution Environment
		* Protects against:
			* Unauthorized data access
			* Code corruption
## Cloud Compliance
* Cloud Provider Regulatory Support: <br>![[Pasted image 20230812132358.png]]
* Tenant Responsibilities:
	* Understand your regulatory requirements
	* Document provider compliance with regulations
	* Implement your responsibilities
	* Use provider tools to achieve, maintain, and document compliance
* Common Protected Data:
	* Types of Data:
		* Person Identifiable Information (PII)
		* Health Care 
			* Health Insurance Portability and Accountability Act of 1996  (HIPPA)
		* Financial Services
	* Business Relevance
		* Intellectual Property
		* Legal Considerations
		* Business Reputation
	* Regional Considerations
		* GDPR-EU
		* California
		* US Federal Protected Data Type (research, technology, weapons, etc.)