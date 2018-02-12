---
layout: post
title: Configuration protected green background for xpdf
---
## Set paper color

```sh
#cat ~/.Xdefaults
xpdf.paperColor: #cce8cf
```

## Config to scroll like vim

```sh
#cat ~/.xpdfrc
continuousView "yes"
initialZoom "width"
bind j any scrollDown(64)
bind k any scrollUp(64)
bind h any scrollLeft(64)
bind l any scrollRight(64)
bind / any find
bind ctrl-f any pageDown
bind ctrl-b any pageUp
bind G any gotoLastPage
```

## Load .Xdefaults automatically at system startup

You need place following statement to .bash_profile or .bashrc for appropriate user.

```sh
xrdb -load ~/.Xdefaults
```
