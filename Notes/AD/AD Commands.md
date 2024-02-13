# PowerShell
- [PowerShell Reference](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)
- Import AD Module:
```powershell
Import-Module -Name ActiveDirectory"
```
- Add Users:
```powershell
New-ADUser -Name "<name>" -Accountpassword (ConvertTo-SecureString -AsPlainText "<password>" -Force ) -Enabled $true 
-ChangePasswordAtLogon $true -OtherAttributes @{'title'="<title>";'mail'="<email>"}
```
- Remove Users:
```powershell
Remove-ADUser -Identity <logon name>
```
- Unlock Users:
```powershell
Unlock-ADAccount -Identity <logon name>
```
- Reset Passwords:
```powershell
Set-ADAccountPassword -Identity '<logon name>' -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "<password>" -Force) -ChangePasswordAtLogon $true
```
- Create New OU:
```powershell
New-ADOrganizationalUnit -Name "Security Analysts" -Path "OU=IT,OU=HQ-NYC,OU=Employees,OU=CORP,DC=INLANEFREIGHT,DC=LOCAL"
```
- Create New Group:
```powershell
New-ADGroup -Name "Security Analysts" -SamAccountName analysts -GroupCategory Security -GroupScope Global -DisplayName "Security Analysts" -Path "OU=Security Analysts,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL" -Description "Members of this group are Security Analysts under the IT OU"
```
- Add Users to Group:
	- Here we use the `SAMAccountName` of the users to add them to the Analysts group.
```powershell
Add-ADGroupMember -Identity <SamAccountName>/<DN> -Members ACepheus,OStarchaser,ACallisto
```
- Duplicate GPO:
```powershell
Copy-GPO -SourceName "Logon Banner" -TargetName "Security Analysts Control"
```
- Link GPO:
```powershell
New-GPLink -Name "Security Analysts Control" -Target "ou=Security Analysts,ou=IT,OU=HQ-NYC,OU=Employees,OU=Corp,dc=INLANEFREIGHT,dc=LOCAL" -LinkEnabled Yes
```