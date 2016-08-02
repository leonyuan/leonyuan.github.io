---
layout: post
title: inkscope在CentOS 7.1上安装部署指南
---
inkscope是一个Ceph管理和监视工具，它的功能依赖于Ceph提供的API，使用mongodb存储实时采集到的数据和历史数据。项目首页：<https://github.com/inkscope>


1. 软件包介绍
-------------
- inkscope-sysprobe

  用于收集Ceph节点主机的系统信息，需要在所有Ceph节点上安装，包括mon和osd。

- inkscope-cephrestapi

  用于启动ceph rest api服务，只需在一个Ceph节点上安装。

- inkscope-cephprobe

  用于收集Ceph集群信息，只需在一个Ceph节点上安装。

  通常将inkscope-cephrestapi和inkscope-cephprobe安装在同一节点主机上。

- inkscope-admviz

  Web管理控制界面，通常安装在Ceph集群以外的管理主机上。

- inkscope-common

  inkscope的配置文件，需要在所有主机上安装。

下载inkscope软件包：

打开<https://github.com/inkscope/inkscope-packaging/tree/master/RPMS>分别下载：

    inkscope-admviz-1.2.0-0.noarch.rpm,  
    inkscope-cephrestapi-1.2.0-0.noarch.rpm,  
    inkscope-cephprobe-1.2.0-0.noarch.rpm,  
    inkscope-common-1.2.0-0.noarch.rpm,  
    inkscope-sysprobe-1.2.0-0.noarch.rpm

2. 安装策略 
-----------

本文档描述在如下Ceph集群环境下安装inkscope：

MON节点主机：mon1, mon2, mon3

OSD节点主机：osd1, osd2

管理主机：admin2

将inkscope-cephrestapi和inkscope-cephprobe安装在mon2节点主机上；

将inkscope-admviz安装在admin2主机上；

在mon1主机上安装radosgw（inkscope在运行时还调用了radosgw rest api）；

在所有主机上安装inkscope-common和inkscope-sysprobe。

3. 安装inkscope-cephrestapi和inkscope-cephprobe
-----------------------------------------------

以下安装操作在mon2节点上进行。

### 3.1 安装依赖软件包

```sh
yum -y install python-devel
easy_install pip
yum -y install gcc
pip install pymongo psutil flask
```

### 3.2 安装common、sysprobe、restapi、cephprobe软件包

```sh
yum -y install inkscope-common-1.2.0-0.noarch.rpm inkscope-sysprobe-1.2.0-0.noarch.rpm inkscope-cephrestapi-1.2.0-0.noarch.rpm inkscope-cephprobe-1.2.0-0.noarch.rpm
```


启动sysprobe服务：

```sh
chkconfig sysprobe on
service sysprobe start
```

启动cephprobe服务：

```sh
chkconfig cephprobe on
service cephprobe start
```

启动ceph-rest-api服务：

```sh
chkconfig ceph-rest-api on
service ceph-rest-api start
```

4. 安装web管理控制台
--------------------

以下安装操作在admin2管理主机上进行。

### 4.1 安装依赖软件包

- 安装apache http server

```sh
yum -y install httpd
```

- 安装mongodb

从[mongodb官方仓库](http://repo.mongodb.org/yum/redhat/)下载yum的repo文件：mongodb-org-3.0.repo，并复制到：/etc/yum.repos.d目录下。

```sh
wget http://repo.mongodb.org/yum/redhat/mongodb-org-3.0.repo
mv mongodb-org-3.0.repo /etc/yum.repos.d
yum update
yum -y install mongodb-org
```

- 安装epel（如果已经安装则跳过）

```sh
yum -y install epel-release
yum update
```

- 安装python-simplejson、python-bson

```sh
yum -y install python-simplejson python-bson
```

- 安装pip、flask

```sh
easy_install pip
pip install flask
```

### 4.2 安装admviz、common、sysprobe软件包：

```sh
yum -y install inkscope-admviz-1.2.0-0.noarch.rpm inkscope-common-1.2.0-0.noarch.rpm inkscope-sysprobe-1.2.0-0.noarch.rpm
```




