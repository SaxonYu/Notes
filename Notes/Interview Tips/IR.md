- Cyber Kill Chain: `Recon`, `Weaponize`, `Deliver`, `Exploit`, `Install`, `Command & Control`, `Action`
# Incident Handling Process 
## Overview: 
- `Preparation`, `Detection & Analysis`, `Containment, Eradication & Recovery`, `Post-Incident Activity.
	- Two main activities: investigating and recovering
	- `cyclic` instead of `linear`.![[Pasted image 20240319182851.png]]
## Preparation
- Two main objectives:
	- establishment of incident handling capability within the organization.
	- ability to protect against and prevent IT security incidents by implementing appropriate protective measures.
	- `Prerequisites`:
		- Skilled incident handling team members
		- Trained workforce
		- Clear policies and documentation
		- Tools (software and hardware)
	- Common Protections:
		- `DMARC`
		- `Endpoint Hardening`
		- `Network Protection` 
		- `Privilege Identity Management/MFA`
		- `Vulnerability Scanning`
		- `User Awareness Training`
		- `Active Directory Security Assesement`
		- `Purple Team Exercises`
## Detection & Analysis
![[Pasted image 20240320000927.png]]
- `Sources`: 
	- An employee that notices abnormal behavior
	- An alert from one of our tools (EDR, IDS, Firewall, SIEM, etc.)
	- Threat hunting activities
	- A third-party notification informing us that they discovered signs of our organization being compromised
- `Initial Investigation`:
	- Date/Time when the incident was reported along with who reported.
	- What was the incident? 
	- Assemble a list of impacted systems
	- Physical location, operating systems, IP addresses and hostnames, system owner, system's purpose, current state of the system
- `Incident Severity & Extent`:
	- Are any business-critical systems be affected by the incident?
	- How many systems have been impacted?
	- Is the exploit being used in the wild?
	- Does the exploit have any worm-like capabilities?
- `Incident Confidentiality & Communication`
- `3-step cyclic process`:
	- Creation and usage of indicators of compromise (IOC)
	- Identification of new leads and impacted systems
	- Data collection and analysis from the new leads and impacted systems
## Containment, Eradication, & Recovery
# Post-Incident Activity
- 