---
title: RootMe It Happens, Sometimes Writeup
date: 2023-10-05
categories: [RootMe, Realistic]
tags: [RootMe, RootMe - Realistic, WriteUp]
author: barabulkit
---

# RootMe - It Happens, Sometimes

When we first open challenge we can see this
![fist_view](/assets/img/rootme/realistic/happens/1.png)

I openned source code and in format.css we can find that there is images dir
![images](/assets/img/rootme/realistic/happens/2.png)

When we open this dir we will find thumbs.db (which is auto generated files with thumbgs for images in windows)
![thumbs.db](/assets/img/rootme/realistic/happens/3.png)

Alas, there is nothing that can be compromised in this file (we can see its content with [Thumbs Viewer](https://thumbsviewer.github.io/) or extract it with vinetto that comes with kali)
![thumbs_viewer](/assets/img/rootme/realistic/happens/4.png)

Then i just typed /admin instead of /images in url and got invited to input login and password for basic auth
Sent this to burp and started to bruteforce request methods, solution was found at the DELETE method 
![solutiuon](/assets/img/rootme/realistic/happens/5.png)