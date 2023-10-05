---
title: RootMe AppArmor Jail - Indroduction Writeup
date: 2023-10-03
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# RootMe AppArmor Jail - Inroduction

in this challenge we have apparmor profile listing

```
#include <tunables/global>

profile docker_chall01 flags=(attach_disconnected,mediate_deleted) {
   #include <abstractions/base>
   network,
   capability,
   file,
   umount,
   signal (send,receive),
   deny mount,

   deny /sys/[^f]*/** wklx,
   deny /sys/f[^s]*/** wklx,
   deny /sys/fs/[^c]*/** wklx,
   deny /sys/fs/c[^g]*/** wklx,
   deny /sys/fs/cg[^r]*/** wklx,
   deny /sys/firmware/** rwklx,
   deny /sys/kernel/security/** rwklx,

   deny @{PROC}/* w,   # deny write for all files directly in /proc (not in a subdir)
   # deny write to files not in /proc/<number>/** or /proc/sys/**
   deny @{PROC}/{[^1-9],[^1-9][^0-9],[^1-9s][^0-9y][^0-9s],[^1-9][^0-9][^0-9][^0-9]*}/** w,
   deny @{PROC}/sys/[^k]** w,  # deny /proc/sys except /proc/sys/k* (effectively /proc/sys/kernel)
   deny @{PROC}/sys/kernel/{?,??,[^s][^h][^m]**} w,  # deny everything except shm* in /proc/sys/kernel/
   deny @{PROC}/sysrq-trigger rwklx,
   deny @{PROC}/kcore rwklx,

   /home/app-script-ch27/bash px -> bashprof1,
 
}
profile bashprof1 flags=(attach_disconnected,mediate_deleted) {
   #include <abstractions/base>
   #include <abstractions/bash>
   
   network,
   capability,
   deny mount,
   umount,
   signal (send,receive),

   deny /sys/[^f]*/** wklx,
   deny /sys/f[^s]*/** wklx,
   deny /sys/fs/[^c]*/** wklx,
   deny /sys/fs/c[^g]*/** wklx,
   deny /sys/fs/cg[^r]*/** wklx,
   deny /sys/firmware/** rwklx,
   deny /sys/kernel/security/** rwklx,

   deny @{PROC}/* w,   # deny write for all files directly in /proc (not in a subdir)
   # deny write to files not in /proc/<number>/** or /proc/sys/**
   deny @{PROC}/{[^1-9],[^1-9][^0-9],[^1-9s][^0-9y][^0-9s],[^1-9][^0-9][^0-9][^0-9]*}/** w,
   deny @{PROC}/sys/[^k]** w,  # deny /proc/sys except /proc/sys/k* (effectively /proc/sys/kernel)
   deny @{PROC}/sys/kernel/{?,??,[^s][^h][^m]**} w,  # deny everything except shm* in /proc/sys/kernel/
   deny @{PROC}/sysrq-trigger rwklx,
   deny @{PROC}/kcore rwklx,

   / r,
   /** mrwlk,
   /bin/** ix,
   /usr/bin/** ix,
   /lib/x86_64-linux-gnu/ld-*.so mrUx,
   deny /home/app-script-ch27/flag.txt r,
}
```

The most important lines there for us is the last 2 lines

```
/lib/x86_64-linux-gnu/ld-*.so mrUx,
deny /home/app-script-ch27/flag.txt r
```

We can not read flag file, but we can launch ld-* files without apparmor restictions (Ux flag)
so we can use

```shell
/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2 /bin/cat flag.txt
```

And receive flag `M4nd4t0ry_4cc3ss_C0ntr0l_J0k3`