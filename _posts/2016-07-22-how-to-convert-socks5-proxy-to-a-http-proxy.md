---
layout: post
title: How to convert socks5 proxy to a HTTP proxy
date: 2016-07-22
---
Install polipo
--------------

[Polipo](https://www.irif.univ-paris-diderot.fr/~jch/software/polipo/) is a small and fast caching web proxy.

configure and run polipo
------------------------

```sh
# cat /etc/polipo/config
socksParentProxy = "127.0.0.1:7070"
socksProxyType = socks5
proxyAddress = "::0"
proxyPort = 8123
# polipo &
```

Play with the HTTP proxy
------------------------

```sh
# http_proxy=http://localhost:8123 apt-get update

# http_proxy=http://localhost:8123 curl www.google.com

# http_proxy=http://localhost:8123 wget www.google.com

# RSYNC_PROXY=localhost:8123 sbopkg -r

# git config --global http.proxy 127.0.0.1:8123
# git clone https://github.com/xxx/xxx.git
# git xxx
# git xxx
# git config --global --unset-all http.proxy
```
