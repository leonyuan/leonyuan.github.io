---
layout: post
title: Sed replacement with regular expression in file
---

the escaped parentheses (that is, parentheses with backslashes before them) remember a substring of the characters matched by the regular expression. You can use this to exclude part of the characters matched by the regular expression. The "\1" is the first remembered pattern, and the "\2" is the second remembered pattern. Sed has up to nine remembered patterns.

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
