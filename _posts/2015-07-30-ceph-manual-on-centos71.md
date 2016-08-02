---
layout: post
title: Ceph分布式存储集群在CentOS 7.1上手动安装部署指南
---
1. 集群配置方案
2. 安装配置OS
3. OSD硬盘分区
4. 安装ceph
5. 部署MON节点
6. 部署OSD节点
7. 扩展MON节点

## 1. 集群配置方案
### 1.1 MON服务器
按ceph MON服务器奇数数量要求，部署3台MON 服务器：

- mon1, public network: 192.168.39.231
- mon2, public network: 192.168.39.232
- mon3, public network: 192.168.39.233
 
每台MON服务器都是一台虚拟机，配置为：CentOS 7.1.1503，1个CPU，512M RAM，只配置一个用作OS的20GB硬盘，分区格式用XFS。

### 1.2 OSD服务器
考虑用3个OSD Daemon处理数据条带化，3个OSD Daemon做数据备份，所以一共需要9个OSD Daemon，每台OSD服务器部署3个OSD Daemon，共需3台OSD服务器。

- osd1, public network: 192.168.39.234, cluster network: 192.168.56.51
- osd2, public network: 192.168.39.235, cluster network: 192.168.56.52
- osd3, public network: 192.168.39.236, cluster network: 192.168.56.53

每个OSD服务器都是一台虚拟机，配置为：CentOS 7.1.1503，1个CPU，1 GB RAM，硬盘配置为:

- disk0 (用于OS) = 20 GB
- disk1 (journal盘，用于存放journal) = 15 GB （用SSD性能最佳）
- disk2 (data1盘，用于数据) = 50GB
- disk3 (data2盘，用于数据) = 50GB
- disk4 (date3盘，用于数据) = 50GB

各个分区格式均用XFS。


### 1.3. admin机器
另外再准备一台用于安装集群的admin机器：

  admin, public network: 192.168.39.230

  配置与MON服务器一致。

## 2. 安装配置OS
### 2.1. 安装操作系统

所有机器都安装CentOS 7.1.1503，选择最小化(minimal install)安装类型，安装时创建ceph用户，密码都设为同一个。

```sh
$ cat /etc/redhat-release
CentOS Linux release 7.1.1503 (Core)
```

### 2.2. 将ceph用户加入sudo列表

安装后将ceph用户加入sudo列表并设置为不用输入口令：

```sh
su -   # 输入root口令，切换到root用户
echo "ceph ALL = (root) NOPASSWD:ALL" | tee /etc/sudoers.d/ceph
chmod 0440 /etc/sudoers.d/ceph
```
 
### 2.3. 安装ntp服务器

为保证各个服务器的时间一致，安装ntp服务器

```sh
sudo yum install -y ntp ntpdate ntp-doc
```

访问：<http://www.pool.ntp.org/zone/cn>，获取中国区公用时间同步服务器。如：

- server 0.cn.pool.ntp.org
- server 1.asia.pool.ntp.org
- server 2.asia.pool.ntp.org

将这三个服务器添加到/etc/ntp.conf，删除文件中原有的：

- server 0.centos.pool.ntp.org iburst
- server 1.centos.pool.ntp.org iburst
- server 2.centos.pool.ntp.org iburst
- server 3.centos.pool.ntp.org iburst

再执行下面的命令手工从服务器同步并启动ntp服务：

```sh
sudo ntpdate 0.cn.pool.ntp.org
sudo hwclock -w
sudo systemctl enable ntpd.service
sudo systemctl start ntpd.service
```
 
### 2.4. 停用防火墙

在测试环境可以直接停用防火墙，如果是生产环境则要建立相应iptable规则放行ceph相应的端口 (MON服务器默认使用端口 6789，OSD和MDS服务器默认使用端口范围为： 6800:7810):

停用防火墙使用命令：

```sh
sudo systemctl disable firewalld
sudo systemctl stop firewalld
```
 
### 2.5. 禁用SElinux

同时还可以禁用SElinux（生产环境不停用而是创建相应安全规则）

```sh
sudo sed -i s'/SELINUX=enforcing/SELINUX=disabled'/g /etc/sysconfig/selinux
```
 
### 2.6. 去掉requiretty选项

为了在admin机器上安装各个集群节点不报错，还要去掉sudoer中的requiretty选项：

```sh
sudo sed -i s'/Defaults    requiretty/#Defaults    requiretty'/g /etc/sudoers
```
 
### 2.7. ssh设置

在admin机器上编辑/etc/hosts：

```sh
sudo vim /etc/hosts
```

添加如下内容：

    192.168.39.230    admin
    192.168.39.231    mon1
    192.168.39.232    mon2
    192.168.39.233    mon3
    192.168.39.234    osd1
    192.168.39.235    osd2
    192.168.39.236    osd3

在admin机器上生成ssh key：

```sh
ssh-keygen -t rsa
```

拷贝admin机器上的ssh key到其它各个集群节点。

```sh
ssh-copy-id ceph@osd{n}/mon{n}  # n为编号1、2、3
```

此时需要输入集群节点上ceph用户的口令。

再编辑~/.ssh/config：

```sh
vim ~/.ssh/config
```

内容如下：

    Host admin
        Hostname admin
        User ceph
    Host mon1
        Hostname mon1
        User ceph
    Host mon2
        Hostname mon2
        User ceph
    Host mon3
        Hostname mon3
        User ceph
    Host osd1
        Hostname osd1
        User ceph
    Host osd2
        Hostname osd2
        User ceph
    Host osd3
        Hostname osd3
        User ceph

编辑完后设置文件属性：

```sh
chmod 440 ~/.ssh/config
```

之后就可以在admin机器上通过：`ssh mon{n}/osd{n}` 来登录并操作集群节点。

## 3. OSD硬盘分区
### 3.1. 分区方案  

每个OSD服务器安装了5个硬盘，其中journal盘用于存放journal日志，data1、data2和data3用于存放数据。journal、data1、data2和data3分别用sdb、sdc、sdd和sde表示（sda表示系统盘）。在部署OSD之前，要对硬盘进行分区，分区方案如下：

- 数据盘（data1、data2、data3）：分一个主区，分区格式用xfs，占用全部空间
- Journal盘：分三个分区，不指定分区格式，三个分区各占33%

开始分区前各硬盘的分区表如下：

```sh
$ lsblk -f
NAME               FSTYPE      LABEL     UUID                MOUNTPOINT
sda
├─sda1             xfs                   e9efc.......b87ai   /boot
└─sda2             LVM2_member           BcPJZ.......ct16k 
    ├─centos-root  xfs                   a87696......1a943   /
    └─centos-swap  swap                  9849c77.....45920   [SWAP]
sdb
sdc
sdd
sde
sr0
```

### 3.2. 对数据盘进行分区

```sh
$ sudo parted /dev/sd<c/d/e>
(parted) mklabel gpt
(parted) mkpart primary xfs 0% 100%
(parted) quit
```

然后格式化数据分区：

```sh
sudo mkfs.xfs /dev/sd<x>1
```

### 3.3. 对journal盘进行分区

```sh
$ sudo parted /dev/sdb
(parted) mklabel gpt
(parted) mkpart primary 0% 33%
(parted) mkpart primary 34% 66%
(parted) mkpart primary 67% 100%
(parted) quit
```

注：journal分区不用指定分区格式，也不用格式化。

分区完成后的分区表如下：

```sh
$ lsblk -f
NAME               FSTYPE      LABEL     UUID                MOUNTPOINT
sda
├─sda1             xfs                   e9efc.......b87ai   /boot
└─sda2             LVM2_member           BcPJZ.......ct16k 
    ├─centos-root  xfs                   a87696......1a943   /
    └─centos-swap  swap                  9849c77.....45920   [SWAP]
sdb
  ├─sdb1
  ├─sdb2
  └─sdb3
sdc
  └─sdc1           xfs                   c0427.......ca430   
sdd
  └─sdd1           xfs                   c4456.........5e3   
sde
  └─sde1           xfs                   da243.......f4sr0
sr0
```

### 3.4. 分区批处理脚本

```sh
sudo parted -s /dev/sdc mklabel gpt
sudo parted -s /dev/sdc mkpart primary xfs 0% 100%
sudo mkfs.xfs /dev/sdc1 -f
sudo parted -s /dev/sdd mklabel gpt
sudo parted -s /dev/sdd mkpart primary xfs 0% 100%
sudo mkfs.xfs /dev/sdd1 -f
sudo parted -s /dev/sde mklabel gpt
sudo parted -s /dev/sde mkpart primary xfs 0% 100%
sudo mkfs.xfs /dev/sde1 -f
sudo parted -s /dev/sdb mklabel gpt
sudo parted -s /dev/sdb mkpart primary 0% 33%
sudo parted -s /dev/sdb mkpart primary 34% 66%
sudo parted -s /dev/sdb mkpart primary 67% 100%
```

## 4. 安装ceph
### 4.1. 添加key

```sh
sudo rpm --import 'https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc'
```

### 4.2. 建立ceph的repo文件

```sh
sudo vim /etc/yum.repos.d/ceph.repo
```

内容如下：（**确认优先级选项priority=2**）

    [ceph]
    name=Ceph packages for $basearch
    baseurl=http://ceph.com/rpm-hammer/el7/$basearch
    enabled=1
    priority=2
    gpgcheck=1
    type=rpm-md
    gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc

    [ceph-noarch]
    name=Ceph noarch packages
    baseurl=http://ceph.com/rpm-hammer/el7/noarch
    enabled=1
    priority=2
    gpgcheck=1
    type=rpm-md
    gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc

    [ceph-source]
    name=Ceph source packages
    baseurl=http://ceph.com/rpm-hammer/el7/SRPMS
    enabled=0
    priority=2
    gpgcheck=1
    type=rpm-md
    gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc

### 4.3. 安装yum插件yum-plugin-priorities

```sh
sudo yum -y install yum-plugin-priorities
```

执行完后确认存在 /etc/yum/pluginconf.d/priorities.conf 文件，同时确认其已开启，文件内容应为：

    [main]
    enabled = 1
 
### 4.4. 安装依赖的软件包

安装ceph之前需要安装下列第三方软件包：

- snappy
- leveldb
- gdisk
- python-argparse
- gperftools-libs

这些软件包不全部包含在系统自带的YUM源里，leveldb，gperftools-libs包含在EPEL(Extra Packages for Enterprise Linux)里，安装EPEL的YUM源：

```sh
sudo yum -y install epel-release
```

执行成功后在/etc/yum.repos.d目录下生成两个文件：epel.repo和epel-testing.repo。手工编辑这两个文件，将baseurl配置项后的url以下面的URL为基础进行相应修改。

    http://mirrors.neusoft.edu.cn/epel/7
  并注释mirrorlist配置项。（这么做的原因是直接从某个访问速度较快的特定mirror站下载安装，免去fastestmirror插件费时地查找）

修改后的配置如下（只选取epel节示例）：

    [epel]
    name=Extra Packages for Enterprise Linux 7 - $basearch
    baseurl=http://mirrors.neusoft.edu.cn/epel/7/$basearch
    #baseurl=http://download.fedoraproject.org/pub/epel/7/$basearch
    #mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=$basearch
    failovermethod=priority
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7

注：http://mirrors.neusoft.edu.cn/epel/7 是怎么获得到的？

http://mirrors.neusoft.edu.cn/epel/7 是国内东软信息学院的epel mirror，通过访问mirrorlist配置项后的：<https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=x86_64> 获得的。

再执行清除缓存并更新源：

```sh
sudo yum clean all
sudo yum update
```

安装第三方软件包：

```sh
sudo yum -y install snappy leveldb gdisk python-argparse gperftools-libs
```

安装ceph

```sh
sudo yum -y install ceph
```

## 5. 部署MON节点

部署MON节点前先要了解以下几个概念：

- fsid：集群唯一标识，在多个集群中必须唯一，通常用uuid表示。
- cluster name：集群名称，默认为ceph。执行ceph客户端命令时需要指定集群名称，如果不指定，则用默认值。
- monitor name：MON节点名称，集群内的各个MON节点的名称必须唯一，通常以MON节点主机短名来表示，主机短名可用`hostname -s`获得。
- monitor map：集群内MON节点的map，用于MON节点之间互相发现。
- monitor keyring：集群内MON节点之间通信所用的加密KEY。
- administrator keyring：ceph客户端命令需要一个admin用户及加密KEY与ceph服务器节点通信。

接下来以主机名：mon1，IP地址：192.168.39.231为例说明具体的部署步骤。


### 5.1. SSH到要安装MON节点的主机

```sh
ssh mon1
```

### 5.2. 配置文件目录

确认主机上有一个用来存放ceph配置文件的目录，默认为：/etc/ceph。这个目录会在安装 ceph时自动创建。

```sh
$ ls /etc/ceph
```

### 5.3. 建立配置文件

在配置文件目录下建立一个配置文件，文件名为：ceph.conf。ceph.conf文件是ceph系统的主要配置文件，在部署ceph集群时和用客户端命令行查询监控集群状态时用到。

```sh
sudo vim /etc/ceph/ceph.conf
```

### 5.4. 生成一个UUID用于fsid

```sh
uuidgen
```

把这个UUID加到ceph.conf中

    fsid = 6f771912-af63-49cb-93d0-9ce6240345a7

### 5.5. 加入初始MON节点主机名

    mon_initial_members = mon1


### 5.6. 加入初始MON节点主机IP地址

    mon_host = 192.168.39.231


### 5.7. 创建monitor keyring

```sh
sudo ceph-authtool --create-keyring /etc/ceph/ceph.mon.keyring --gen-key -n mon. --cap mon 'allow *'
```

### 5.8. 创建administrator keyring

创建一个 administrator keyring，生成client.admin 用户和密钥

```sh
sudo ceph-authtool --create-keyring /etc/ceph/ceph.client.admin.keyring --gen-key -n client.admin --set-uid=0 --cap mon 'allow *' --cap osd 'allow *' --cap mds 'allow'
```

### 5.9. 把client.admin 密钥导入到monitor keyring中

```sh
sudo ceph-authtool /etc/ceph/ceph.mon.keyring --import-keyring /etc/ceph/ceph.client.admin.keyring
```

### 5.10. 建立monitor map

使用主机短名、IP地址和fsid建立mornitor map：

    monmaptool --create --add {hostname} {ip-address} --fsid {uuid} monmap

执行：

```sh
sudo monmaptool --create --add mon1 192.168.39.231 --fsid 6f771912-af63-49cb-93d0-9ce6240345a7 /etc/ceph/monmap
```

### 5.11. 为MON节点建立数据存放目录

    sudo mkdir /var/lib/ceph/mon/{cluster-name}-{hostname}

执行：

```sh
sudo mkdir /var/lib/ceph/mon/ceph-mon1
```

### 5.12. 初始化MON节点

用monitor map和monitor keyring初始化MON节点。

    ceph-mon [--cluster {cluster-name}] --mkfs -i {hostname} --monmap monmap --keyring ceph.mon.keyring

执行：

```sh
sudo ceph-mon --mkfs -i mon1 --monmap /etc/ceph/monmap --keyring /etc/ceph/ceph.mon.keyring
```

### 5.13. 完成的ceph.conf文件

编辑好的ceph.conf文件内容如下：

    [global]
    fsid = 6f771912-af63-49cb-93d0-9ce6240345a7
    mon_initial_members = mon1
    mon_host = 192.168.39.231
    auth_cluster_required = cephx
    auth_service_required = cephx
    auth_client_required = cephx
    filestore_xattr_use_omap = true
    public_network = 192.168.39.0/24   # 公共访问网络
    cluster_network = 192.168.56.0/24  # OSD间数据复制专用网络
    osd_pool_default_size = 3          # 写三次对象
    osd_pool_default_min_size = 2      # 允许在degraded状态下只写两次对象
    osd_pool_default_pg_num = 512
    osd_pool_default_pgp_num = 512
    osd_crush_chooseleaf_type = 1

配置双网络的原因是：将OSD之间的大流量数据同步和复制放到另一个专用的网络，可以减少外部的数据访问网络流量使外部访问更有效率。下图是从 ceph官网上摘录下来的，说明了ceph集群的网络结构。
![ceph network](http://ceph.com/docs/master/_images/ditaa-2452ee22ef7d825a489a08e0b935453f2b06b0e6.png "ceph double network")


### 5.14. 完成MON节点初始化

在数据目录下创建文件名为done的空文件以标记MON节点初始化完成。
 
```sh
sudo touch /var/lib/ceph/mon/ceph-mon1/done
```

在数据目录下创建文件名为sysvinit的空文件标记可以通过sysvinit方式启动MON节点服务。

```sh
sudo touch /var/lib/ceph/mon/ceph-mon1/sysvinit
```
 
### 5.15. 启动MON节点

```sh
sudo /etc/init.d/ceph start mon.mon1
```
 
### 5.16. 验证MON节点

```sh
ceph -s
```

输出如下：

    cluster 6f771912-af63-49cb-93d0-9ce6240345a7
     health HEALTH_ERR
            64 pgs stuck inactive
            64 pgs stuck unclean
            no osds
     monmap e1: 1 mons at {mon1=192.168.39.231:6789/0}
            election epoch 2, quorum 0 mon1
     osdmap e1: 0 osds: 0 up, 0 in
      pgmap v2: 64 pgs, 1 pools, 0 bytes data, 0 objects
            0 kB used, 0 kB / 0 kB avail
               64 creating
 
健康状态显示为HEALTH_ERR，placement group为inactive和unclean是因为还没有部署OSD节点。一旦部署了OSD节点后，这些错误信息就不再出现。

### 5.17. 设置ceph系统服务自启动

```sh
sudo chkconfig ceph on
```

系统重新启动后，ceph服务会自动启动。
 
## 6. 部署OSD节点

部署MON节点后，还要部署OSD节点，集群才具备数据存储功能。一个OSD节点上可以部署多个OSD Daemon，每个OSD Daemon用osd-number区分，osd-number从0开始向上以自然数增长。

接下来以主机名：osd1，IP地址：192.168.39.234为例说明OSD节点部署步骤。
 
### 6.1. 快速部署
#### 6.1.1. 查看主机硬盘及分区情况

```sh
sudo ceph-disk list
```

输出：

    /dev/sda :
     /dev/sda1 other, xfs, mounted on /boot
     /dev/sda2 other, LVM2_member
    /dev/sdb :
     /dev/sdb1 other
     /dev/sdb2 other
     /dev/sdb3 other
    /dev/sdc :
     /dev/sdc1 other, xfs
    /dev/sdd :
     /dev/sdd1 other, xfs
    /dev/sde :
     /dev/sde1 other, xfs
    /dev/sr0 other, unknown

如前所述，我给每个OSD节点除了用于安装OS的主硬盘外还安装了4个用于OSD存储的硬盘，/dev/sdb用于共享存放日志，/dev/sdc、/dev/sdd、/dev/sde用于存放数据。
 
#### 6.1.2. 复制相应配置文件

复制MON主机/etc/ceph/ceph.conf、ceph.client.admin.keyring到 OSD主机的/etc/ceph目录。

复制MON主机/var/lib/ceph/bootstrap-osd/ceph.keyring到 OSD主机的/var/lib/ceph/bootstrap-osd目录。
 
#### 6.1.3. 准备OSD

```sh
sudo ceph-disk prepare /dev/sdc1 /dev/sdb1
sudo ceph-disk prepare /dev/sdd1 /dev/sdb2
sudo ceph-disk prepare /dev/sde1 /dev/sdb3
```
 
#### 6.1.4. 激活OSD

```sh
sudo ceph-disk activate /dev/sdc1
sudo ceph-disk activate /dev/sdd1
sudo ceph-disk activate /dev/sde1
```
 
#### 6.1.5. 设置自动mount OSD分区

编辑/etc/fstab文件，

```sh
sudo vim /etc/fstab
```

添加以下行：

    /dev/sdc1    /var/lib/ceph/osd/ceph-0    xfs    defaults 0 0
    /dev/sdd1    /var/lib/ceph/osd/ceph-1    xfs    defaults 0 0
    /dev/sde1    /var/lib/ceph/osd/ceph-2    xfs    defaults 0 0

注：后面的OSD2和OSD3主机上mount的目录应该和osd_number相一致，OSD2主机上的三个osd_number是3,4,5，相应地，应该mount到的目录是ceph-3、ceph-4、ceph-5。OSD3同理类推。

#### 6.1.6. 设置ceph系统服务自启动

```sh
sudo chkconfig ceph on
```

系统重新启动后， ceph服务会自动启动。
 
### 6.2. 手工部署（未完成，先参考快速部署）
#### 6.2.1. SSH到OSD节点主机

```sh
ssh osd1
```
 
#### 6.2.2. 创建OSD节点

```sh
ceph osd create
```

命令成功执行后输出osd-number：

    0

这个数字表示OSD Daemon编号
 
#### 6.2.3. 建立OSD文件目录

    mkdir /var/lib/ceph/osd/{cluster-name}-{osd-number}

执行：

```sh
sudo mkdir /var/lib/ceph/osd/ceph-0
```
 
#### 6.2.4. Mount数据分区

    mount -o user_xattr /dev/{hdd} /var/lib/ceph/osd/{cluster-name}-{osd-number}

执行：

```sh
sudo mount -o user_xattr /dev/sdc1 /var/lib/ceph/osd/ceph-0
```
 
#### 6.2.5. 初始化OSD数据目录

    ceph-osd -i {osd-num} --mkfs --mkkey

执行：

```sh
sudo ceph-osd -i 0 --mkfs --mkkey
```

（此节未完待续。。。。。。）

 
## 7. 扩展MON节点
  因为已经安装并成功运行了一个MON节点，扩展节点变得比较容易，下面以mon2为例说明扩展MON步骤。

### 7.1. 复制相应配置文件

复制mon1主机上的/etc/ceph/ceph.conf、ceph.client.admin.keyring到 mon2主机的/etc/ceph目录。

### 7.2. 创建MON数据目录

在mon2主机上创建服务monitor的默认数据目录

    mkdir /var/lib/ceph/mon/ceph-{mon-id}

执行：

```sh
sudo mkdir /var/lib/ceph/mon/ceph-mon2
```
 
### 7.3. 获取keyring

```sh
sudo ceph auth get mon. -o /etc/ceph/ceph.mon.keyring
```
 
### 7.4. 获取monitor map

```sh
sudo ceph mon getmap -o /etc/ceph/monmap
```
 
### 7.5. 将新MON节点添加到monmap

```sh
monmaptool --add mon2 192.168.39.232 /etc/ceph/monmap
```
 
### 7.6. 初始化MON 数据目录

初始化在7.2节中创建的数据目录。必须指定monmap路径和keyring路径。

```sh
sudo ceph-mon -i mon2 --mkfs --monmap /etc/ceph/monmap --keyring /etc/ceph/ceph.mon.keyring
```
 
### 7.7. 将MON节点添加到集群

    ceph mon add <mon-id> <ip>[:<port>]

执行：

```sh
sudo ceph mon add mon2 192.168.39.232
```

注：回车执行该命令后，控制台输出：monclient: hunting for new mon提示，并进入长时间等待不能结束，这时按Ctrl-C强制退出。

### 7.8. 完成MON节点初始化

在数据目录下创建文件名为done的空文件以标记MON节点初始化完成。

```sh
sudo touch /var/lib/ceph/mon/ceph-mon2/done
```

在数据目录下创建文件名为sysvinit的空文件标记可以通过sysvinit方式启动MON节点服务。

```sh
sudo touch /var/lib/ceph/mon/ceph-mon2/sysvinit
```
 
### 7.9. 启动MON节点

```sh
sudo /etc/init.d/ceph start mon.mon2
```
 
### 7.10. 设置ceph系统服务自启动

```sh
sudo chkconfig ceph on
```

系统重新启动后， ceph服务会自动启动。

（全文完）
