---
title: RootMe Unquoted Expression Inject Writeup
date: 2023-10-05
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# RootMe Unquoted Expression Inject

In this challenge we have next script

```shell
#!/bin/bash
 
PATH=$(/usr/bin/getconf PATH || /bin/kill $$)
 
PASS=$(cat .passwd)
 
if test -z "${1}"; then
    echo "USAGE : $0 [password]"
    exit 1
fi
 
if test $PASS -eq ${1} 2>/dev/null; then
    echo "Well done you can validate the challenge with : $PASS"
else
    echo "Try again ,-)"
fi
 
exit 0
```

this script uses test without quotes around argument it passes to test
so we can execute this script like this

```shell
./wrapper "1 -o 2 -eq 2"
```
and this will check if $pass equals to 1 or 2 equals to 2, and will give us the flag `8246320937403`
