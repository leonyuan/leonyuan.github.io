---
layout: post
title: 在CentOS7.1上安装vagrant及virtualbox
---
## 安装vagrant

访问vagrant官方网站下载列表<http://www.vagrantup.com/downloads>，下载vagrant的RPM安装包。本文档编写时的版本为：<https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4_x86_64.rpm>

```sh
sudo rpm -ivh vagrant_1.7.4_x86_64.rpm
```

## 安装virtualbox作为vagrant的虚拟机提供者

```sh
cd /etc/yum.repos.d
sudo wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo
```

如果没有安装dkms，则先安装它：

```sh
sudo yum -y install dkms
```

安装virtualbox：


```sh
sudo yum -y install VirtualBox-4.3
```

将需要运行virtualbox的用户添加到virtualbox的组。

```sh
sudo usermod -a -G vboxusers {username}
```
---
（完）
