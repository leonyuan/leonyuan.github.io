---
layout: post
title: Sed replacement with regular expression in file
---

```sh
$ cat test.html 
<html>
<head>
</head>
<body>
  <p>some text 1</p>
  <img src="abc.jpg"/>
  <p>some text 2</p>
</body>
</html>
```

```sh
$ sed 's/<img src="\(.*\)"/<img src="http:\/\/ogwsrv:8888\/web\/abc.doc?preview\&f=\1"/' <test.html >test2.html
$ cat test2.html 
<html>
<head>
</head>
<body>
  <p>some text 1</p>
  <img src="http://ogwsrv:8888/web/abc.doc?preview&f=abc.jpg"/>
  <p>some text 2</p>
</body>
</html>
```
