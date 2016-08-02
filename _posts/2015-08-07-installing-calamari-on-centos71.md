---
layout: post
title: Calamari在CentOS 7.1上编译安装部署指南
---
1. Calamari简介
2. 编译Calamari服务端安装包
3. 编译Calamari客户端安装包

## 1. Calamari简介

Calamari是管理和监控Ceph集群的工具，它提供了一个使得监控惊人地简单方便漂亮的控制面板界面。Calamari 最初是Inktank公司Ceph企业产品包的一部分，在几个月前被Red Hat公司开源了（注：Inktank公司被Red Hat公司收购了）。因此，Calamari是一个成熟的开源企业软件。

如果想要知道如何为Ceph集群部署Calamari，这篇文档将以手把手的介绍引导你。到目前为止，官方还没有为Calamari提供预编译好了的安装包。我们将需要从源代码编译得到安装包，进而再安装并运行Calamari。

Calamari是管理和监控Ceph集群的工具，它展现了一个高级REST API。

Calamari由两个部分组成(分别有两个源代码仓库，目前存在于Ceph Github项目仓库里)。

### 1.1 Calamari服务端

Calamari服务端程序用Python 2.6编写的，使用了Saltstack, ZeroRPC, gevent, Django, django-rest-framework, graphite工具或包，还为同其它系统进行集成而实现了一个新的REST API，这是一个重要的特性，因为Calamari的早期版本是基于Ceph自己的REST API的。新的Calamari REST API被设计为更加广泛使用并将成为希望与Ceph集群相互影响的新的开发尝试的基础。项目源码：<https://github.com/ceph/calamari>

### 1.2. Calamari客户端

Calamari客户端是主要由使用了Calamari REST API的Javascript脚本实现的WEB浏览器界面，项目源码: <https://github.com/ceph/calamari-clients>

Calamari服务端组件包含：Apache, salt-master , supervisord , cthulhu , carbon-cache
Calamari客户端组件包含：salt-minion , diamond

### 1.3. 从源代码编译Calamari

不象Ceph , Calamari没有提供预编译好的RPM/DEB安装包。如果你对Calamari感兴趣，你需要编译基于你的Linux发行版的相应安装包。为部署Calamari , 你需要分别编译Calamari服务端安装包和Calamari客户端安装包。

Building Calamari Server packages
- See more at: http://ceph.com/category/calamari/#sthash.mwvVuQLn.dpuf

## 2. 编译Calamari服务端安装包

编译Calamari安装包，可以在个人工作用机器上编译，编译机器的OS也不要求一定是CentOS 7.1。

### 2.1 安装git、Vagrant和Virtualbox

大多数Linux发行版自带git工具，在当初安装OS时就已经自动安装了。如果没有安装，参考其他相关文档安装。

安装Vagrant和Virtualbox可以参考：[在CentOS7.1上安装vagrant及virtualbox](http://ceph.org.cn/topic/xxx)

### 2.2. Clone Calamari服务端和Diamond项目源码

```sh
mkdir ~/calamari-repo
cd ~/calamari-repo
```

```sh
# git clone https://github.com/ceph/calamari.git
Cloning into 'calamari'...
remote: Counting objects: 10253, done.
remote: Compressing objects: 100% (4433/4433), done.
remote: Total 10253 (delta 5434), reused 10188 (delta 5387)
Receiving objects: 100% (10253/10253), 20.53 MiB | 3.55 MiB/s, done.
Resolving deltas: 100% (5434/5434), done.
Checking connectivity... done.
```

```sh
# git clone https://github.com/ceph/Diamond.git --branch=calamari
Cloning into 'Diamond'...
remote: Counting objects: 16225, done.
remote: Compressing objects: 100% (9229/9229), done.
remote: Total 16225 (delta 6170), reused 16225 (delta 6170)
Receiving objects: 100% (16225/16225), 3.79 MiB | 1.19 MiB/s, done.
Resolving deltas: 100% (6170/6170), done.
Checking connectivity... done.
```

### 2.3 查看确认Clone下来的源码目录结构：

```sh
# cd calamari
# ls -l
total 152
-rw-rw-r--  1 1000 1000   251 Jul 31 17:12 COPYING
-rw-rw-r--  1 1000 1000 26436 Jul 31 17:12 COPYING-LGPL2.1
-rw-rw-r--  1 1000 1000  9087 Jul 31 17:12 Makefile
-rw-rw-r--  1 1000 1000  1826 Jul 31 17:12 README.rst
-rwxrwxr-x  1 1000 1000  2961 Jul 31 17:12 adduser.py
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 alembic
-rwxrwxr-x  1 1000 1000   979 Jul 31 17:12 build-rpm.sh
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 calamari-common
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 calamari-web
-rw-rw-r--  1 1000 1000  6086 Jul 31 17:12 calamari.spec
drwxrwxr-x  7 1000 1000  4096 Jul 31 17:12 conf
drwxrwxr-x  4 1000 1000  4096 Jul 31 17:12 cthulhu
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 debian
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 dev
drwxrwxr-x  7 1000 1000  4096 Jul 31 17:12 doc
-rwxrwxr-x  1 1000 1000   643 Jul 31 17:12 get-flavor.sh
-rwxrwxr-x  1 1000 1000   874 Jul 31 17:12 get-versions.sh
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 minion-sim
-rwxrwxr-x  1 1000 1000   326 Jul 31 17:12 pre-commit.py
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 repobuild
drwxrwxr-x  4 1000 1000  4096 Jul 31 17:12 requirements
drwxrwxr-x  4 1000 1000  4096 Jul 31 17:12 rest-api
drwxrwxr-x  4 1000 1000  4096 Jul 31 17:12 salt
drwxrwxr-x  2 1000 1000  4096 Jul 31 17:12 selinux
drwxrwxr-x  2 1000 1000  4096 Jul 31 17:12 tests
-rw-rw-r--  1 1000 1000   792 Jul 31 17:12 tox.ini
drwxrwxr-x 11 1000 1000  4096 Jul 31 17:12 vagrant
-rw-rw-r--  1 1000 1000   230 Jul 31 17:12 vps_bootstrap.sh
drwxrwxr-x  3 1000 1000  4096 Jul 31 17:12 webapp
```

```sh
# cd vagrant
# ls -l
total 40
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 centos6-build
drwxrwxr-x 4 1000 1000 4096 Jul 31 18:03 centos7-build
drwxrwxr-x 4 1000 1000 4096 Jul 31 17:12 devmode
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 precise-build
drwxrwxr-x 2 1000 1000 4096 Jul 31 17:12 production
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 rhel-build
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 rhel7-build
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 trusty-build
-rwxrwxr-x 1 1000 1000  371 Aug  3 09:38 urllib-bootstrap-salt.sh
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 wheezy-build
```

```sh
# cd centos7-build
# ls -l
-rw-rw-r-- 1 1000 1000  940 Jul 31 21:42 Vagrantfile
drwxrwxr-x 3 1000 1000 4096 Jul 31 17:12 salt
```

### 2.4. 初始化并启动编译虚拟机

Calamari的编译过程在Vagrant虚拟机里进行，`vagrant up`命令获取一个虚拟机镜像，并以该镜像作为模板克隆出一个虚拟机实例，然后初始化这个实例并启动它。

```sh
# pwd
/root/calamari-repo/calamari/vagrant/centos7-build
# vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
default: Adapter 1: nat
==> default: Forwarding ports...
default: 22 => 2201 (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
default: SSH address: 127.0.0.1:2201
default: SSH username: vagrant
default: SSH auth method: private key
default: Warning: Connection timeout. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
default: The guest additions on this VM do not match the installed version of
default: VirtualBox! In most cases this is fine, but in rare cases it can
default: prevent things such as shared folders from working properly. If you see
default: shared folder errors, please make sure the guest additions within the
default: virtual machine match the version of VirtualBox you have installed on
default: your host and reload your VM.
default: 
default: Guest Additions Version: 5.0.0
default: VirtualBox Version: 4.3
==> default: Mounting shared folders...
default: /git => /root/calamari-repo
default: /vagrant => /root/calamari-repo/calamari/vagrant/centos7-build
default: /srv/salt => /root/calamari-repo/calamari/vagrant/centos7-build/salt/roots
=> default: Running provisioner: salt...
Copying salt minion config to vm.
Checking if salt-minion is installed
salt-minion was not found.
Checking if salt-call is installed
salt-call was not found.
Bootstrapping Salt... (this may take a while)
Salt successfully configured and installed!
run_overstate set to false. Not running state.overstate.
run_highstate set to false. Not running state.highstate.
```

执行vagrant up后，系统会自动去vagrant的box收录站<https://atlas.hashicorp.com>去下载Centos 7.1的虚拟机镜像文件。

因为首次vagrant up要从远程收录站下载镜像导致很费时，我们也可以手动下载：

```sh
wget https://atlas.hashicorp.com/boxcutter/boxes/centos71/versions/2.0.1/providers/virtualbox.box
```

然后修改Vagrantfile文件，在config.vm.box下面添加下面这行：

    config.vm.box_url = "file:///root/work/virtualbox.box"

保存文件，以后再vagrant up就从本地镜像克隆出新的虚拟机并启动。

注：通过下载镜像文件并从本地加载镜像的意义在于：如果vagrant虚拟机在操作过程中出现了不明原因的故障问题，一个较快且简单的解决办法是重新初始化vagrant虚拟机。这时就不用再从远程收录站下载镜像。

### 2.5. 登录vagrant虚拟机

登录到在上一步中启动的centos 7.1 vagrant虚拟机。

```sh
# vagrant ssh
[vagrant@localhost ~]$
[vagrant@localhost ~]$ df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   42G  1.4G   41G   4% /
devtmpfs                 488M     0  488M   0% /dev
tmpfs                    497M   12K  497M   1% /dev/shm
tmpfs                    497M  6.5M  491M   2% /run
tmpfs                    497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                497M  120M  378M  24% /boot
/dev/mapper/centos-home   21G  264M   21G   2% /home
none                     145G   46G   99G  32% /git
none                     145G   46G   99G  32% /vagrant
none                     145G   46G   99G  32% /srv/salt
[vagrant@localhost ~]$
```

### 2.6. 编译Calamari服务端安装包

```sh
[vagrant@localhost ~]$ sudo salt-call state.highstate
[INFO ] Loading fresh modules for state activity
[INFO ] Creating module dir '/var/cache/salt/minion/extmods/modules'
[INFO ] Syncing modules for environment 'base'
[INFO ] Loading cache from salt://_modules, for base)
......此处省略数千行的输出......
----------
      ID: cp-artifacts-up Diamond/dist/diamond-*.rpm
      Function: cmd.run
      Name: cp Diamond/dist/diamond-*.rpm /git
      Result: True
      Comment: Command "cp Diamond/dist/diamond-*.rpm /git" run
      Started: 02:36:57.625655
      Duration: 10.75 ms
      Changes:   
                ----------
                pid:
                   7141
                retcode:
                   0
                stderr:
                stdout:
 Summary
 ------------
 Succeeded: 9 (changed=9)
 Failed:    0
 ------------
 Total states run:     9
```

最后出现上面的输出表明编译成功。这时会在虚拟机的/git目录（即主机的/root/calamari-repo目录）下生成编译好的RPM安装包。

```sh
[vagrant@localhost ~]$ ls /git -l
total 32508
drwxrwxr-x. 1 vagrant vagrant     4096 Jul 31 09:12 calamari
-rw-r--r--. 1 vagrant vagrant  8395468 Aug  3 02:36 calamari-repo-el6.tar.gz
-rw-r--r--. 1 vagrant vagrant 20215248 Aug  3 02:36 calamari-server-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
-rw-r--r--. 1 vagrant vagrant  3813340 Aug  3 02:36 calamari-server-debuginfo-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
drwxrwxr-x. 1 vagrant vagrant     4096 Jul 31 09:31 Diamond
-rw-r--r--. 1 vagrant vagrant   558632 Aug  3 02:36 diamond-3.4.67-0.noarch.rpm
-rw-r--r--. 1 vagrant vagrant   290313 Aug  3 02:36 diamond-3.4.67-0.src.rpm
```

退出vagrant 虚拟机。

## 3. 编译Calamari客户端安装包

由于至目前Calamari编译过程还不是很成熟，需要用ubuntu vagrant虚拟机来编译适合Centos的客户端安装包。编译成功后将生成一个tar格式的安装包，我们用这个tar包在centos里进行安装。没有相应的RPM安装包。

### 3.1 Clone Calamari客户端项目源码

Clone Calamari客户端项目源码，与Calamari服务端项目源码放在同个一目录下。 

```sh
# git clone https://github.com/ceph/calamari-clients.git
Cloning into 'calamari-clients'...
remote: Counting objects: 16731, done.
remote: Total 16731 (delta 0), reused 0 (delta 0), pack-reused 16731
Receiving objects: 100% (16731/16731), 29.20 MiB | 15.00 KiB/s, done.
Resolving deltas: 100% (9799/9799), done.
Checking connectivity... done.
```

这时calamari-repo目录里有下列子目录和文件：

```sh
# pwd
/root/calamari-repo
# ls -l
total 32512
drwxrwxr-x 10 1000 1000     4096 Jul 31 17:31 Diamond
drwxrwxr-x 20 1000 1000     4096 Jul 31 17:12 calamari
drwxr-xr-x  3 root root     4096 Aug  3 14:44 calamari-clients
-rw-r--r--  1 root root  8395468 Aug  3 10:36 calamari-repo-el6.tar.gz
-rw-r--r--  1 root root 20215248 Aug  3 10:36 calamari-server-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
-rw-r--r--  1 root root  3813340 Aug  3 10:36 calamari-server-debuginfo-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
-rw-r--r--  1 root root   558632 Aug  3 10:36 diamond-3.4.67-0.noarch.rpm
-rw-r--r--  1 root root   290313 Aug  3 10:36 diamond-3.4.67-0.src.rpm
```

### 3.2 初始化并启动vagrant虚拟机

```sh
# cd calamari-clients/vagrant/precise-build
# ls -l
total 8
-rw-r--r-- 1 root root 789 Aug  3 15:11 README.rst
-rw-r--r-- 1 root root 882 Aug  3 15:11 Vagrantfile
```

由于ubuntu precise在后面的vagrant up时会导致：python-requests : Depends: python-urllib3 (>= 1.7.1) but it is not installable 错误，所以不采用源码里的12.04 precise版本，改为采用14.04 trusty版。

修改Vagrantfile为：

    config.vm.box = "boxcutter/ubuntu1404"
    config.vm.box_url = "file:///root/work/ubuntu1404.box"

并下载对应的[ubuntu 14.04 box文件](https://atlas.hashicorp.com/boxcutter/boxes/ubuntu1404/versions/2.0.0/providers/virtualbox.box) 放到/root/work目录下。

vagrant up时还会触发无法安装tornado的错误，解决办法：先查看../urllib-bootstrap-salt.sh，下载URL后的<https://raw.githubusercontent.com/saltstack/salt-bootstrap/stable/bootstrap-salt.sh>保存为bootstrap-salt.sh，再按下面两行修改Vagrantfile中对应位置内容。

    salt.bootstrap_script = "../bootstrap-salt.sh"
    salt.bootstrap_options = "-P"

```sh
# vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'boxcutter/ubuntu1404'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: precise-build_default_1438657383036_16143
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
default: Adapter 1: nat
==> default: Forwarding ports...
default: 22 => 2202 (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
default: SSH address: 127.0.0.1:2202
default: SSH username: vagrant
default: SSH auth method: private key
default: Warning: Connection timeout. Retrying...
default: 
default: Vagrant insecure key detected. Vagrant will automatically replace
default: this with a newly generated keypair for better security.
default: 
default: Inserting generated public key within guest...
default: Removing insecure key from the guest if its present...
default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
default: The guest additions on this VM do not match the installed version of
default: VirtualBox! In most cases this is fine, but in rare cases it can
default: prevent things such as shared folders from working properly. If you see
default: shared folder errors, please make sure the guest additions within the
default: virtual machine match the version of VirtualBox you have installed on
default: your host and reload your VM.
default: 
default: Guest Additions Version: 5.0.0
default: VirtualBox Version: 4.3
==> default: Mounting shared folders...
default: /git => /root/calamari-repo
default: /vagrant => /root/calamari-repo/calamari-clients/vagrant/precise-build
default: /srv/salt => /root/calamari-repo/calamari-clients/vagrant/salt/roots
==> default: Running provisioner: salt...
Copying salt minion config to vm.
Checking if salt-minion is installed
salt-minion was not found.
Checking if salt-call is installed
salt-call was not found.
Bootstrapping Salt... (this may take a while)
Salt successfully configured and installed!
run_overstate set to false. Not running state.overstate.
run_highstate set to false. Not running state.highstate.
```

### 3.3. 登录vagrant虚拟机

登录到在上一步中启动的ubuntu vagrant虚拟机。

```sh
# vagrant ssh
vagrant@precise64:~$
vagrant@precise64:~$ df -h
Filesystem                    Size  Used Avail Use% Mounted on
/dev/mapper/vagrant--vg-root   63G  1.2G   58G   3% /
none                          4.0K     0  4.0K   0% /sys/fs/cgroup
udev                          486M  4.0K  486M   1% /dev
tmpfs                         100M  392K   99M   1% /run
none                          5.0M     0  5.0M   0% /run/lock
none                          497M   12K  497M   1% /run/shm
none                          100M     0  100M   0% /run/user
/dev/sda1                     236M   37M  187M  17% /boot
git                           145G   48G   98G  33% /git
vagrant                       145G   48G   98G  33% /vagrant
srv_salt                      145G   48G   98G  33% /srv/salt
```

### 3.4. 安装依赖包

```sh
sudo apt-get install ruby1.9.1 ruby1.9.1-dev python-software-properties g++ make git debhelper build-essential devscripts
```

### 3.5. 安装node.js

```sh
sudo apt-add-repository http://ppa.launchpad.net/chris-lea/node.js/ubuntu
sudo apt-get update
sudo apt-get install nodejs
sudo npm install -g bower@1.3.8
sudo npm install -g grunt-cli
sudo gem install compass
```

### 3.6. 启动编译

```sh
vagrant@precise64:~$ sudo salt-call state.highstate
```

按照官方的文档直接运行编译会输出下面的错误并终止编译：

    [INFO    ] Running state [cp /git/calamari-clients*tar.gz /home/vagrant/clients] at time 01:10:40.203619
    [INFO    ] Executing state cmd.run for cp /git/calamari-clients*tar.gz /home/vagrant/clients
    [INFO    ] Executing command 'cp /git/calamari-clients*tar.gz /home/vagrant/clients' as user 'vagrant' in directory '/home/vagrant'
    [ERROR   ] Command 'cp /git/calamari-clients*tar.gz /home/vagrant/clients' failed with return code: 1
    [ERROR   ] stderr: cp: cannot stat '/git/calamari-clients*tar.gz': No such file or directory
    [ERROR   ] retcode: 1
    [ERROR   ] {'pid': 14907, 'retcode': 1, 'stderr': "cp: cannot stat '/git/calamari-clients*tar.gz': No such file or directory", 'stdout': ''}
    ......
    ----------
          ID: copyin_build_product
    Function: cmd.run
        Name: cp /git/calamari-clients*tar.gz /home/vagrant/clients
      Result: False
     Comment: Command "cp /git/calamari-clients*tar.gz /home/vagrant/clients" run
     Started: 01:10:40.203619
    Duration: 46.147 ms
     Changes:
    ......
    Summary
    ------------
    Succeeded: 3 (changed=3)
    Failed:    3
    ------------
    Total states run:     6

错误信息表明没有需要的/git/calamari-clients*tar.gz文件，该文件要在运行salt-call state.highstate前先编译出来。编译方法：

```sh
sudo chown -R vagrant:vagrant ~/.npm
cd /git/calamari-client
export REAL_BUILD=y
make
make build-product
```

编译完成后会在/git/calamari-client下生成calamari-clients-build-output.tar.gz文件。将该文件复制到/git。

至此，Calamari服务端和客户端安装包均已生成，calamari-repo目录下应该有如下文件：

```sh
$ cd /git
$ ls -l
total 35012
drwxrwxr-x 1 vagrant vagrant     4096 Jul 31 09:12 calamari
drwxr-xr-x 1 vagrant vagrant     4096 Aug  5 02:55 calamari-clients
-rw-r--r-- 1 vagrant vagrant   838610 Aug  5 02:50 calamari-clients_1.2.2-32-g931ee58_all.deb
-rw-r--r-- 1 vagrant vagrant      797 Aug  5 02:41 calamari-clients_1.2.2-32-g931ee58_amd64.changes
-rw-r--r-- 1 vagrant vagrant  1712529 Aug  5 02:49 calamari-clients-build-output.tar.gz
-rw-r--r-- 1 vagrant vagrant  8395468 Aug  3 02:36 calamari-repo-el6.tar.gz
-rw-r--r-- 1 vagrant vagrant 20215248 Aug  3 02:36 calamari-server-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
-rw-r--r-- 1 vagrant vagrant  3813340 Aug  3 02:36 calamari-server-debuginfo-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
drwxrwxr-x 1 vagrant vagrant     4096 Jul 31 09:31 Diamond
-rw-r--r-- 1 vagrant vagrant   558632 Aug  3 02:36 diamond-3.4.67-0.noarch.rpm
-rw-r--r-- 1 vagrant vagrant   290313 Aug  3 02:36 diamond-3.4.67-0.src.rpm
```

## 4. 部署Calamari

### 4.1. 安装Calamari服务端

将下面这些安装包文件复制到要配置为Calamari主机的机器上。

    calamari-server-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
    diamond-3.4.67-0.noarch.rpm
    calamari-clients-build-output.tar.gz

#### 4.1.1. 安装Calamari服务端和Diamond

```sh
sudo yum install -y calamari-server-1.3.0.1-74_g57e4860.el7.centos.x86_64.rpm
sudo yum install -y diamond-3.4.67-0.noarch.rpm
```

#### 4.1.2. 解压并复制Calamari客户端文件

```sh
$ pwd
/tmp
$ sudo tar xfz calamari-clients-build-output.tar.gz
$ cd /tmp/opt/calamari/webapp
$ sudo cp -rp content/ /opt/calamari/webapp/content
$ sudo ls -l /opt/calamari/webapp/content
total 16
drwxr-xr-x. 7 ceph ceph 4096 Aug  5 10:41 admin
drwxr-xr-x. 7 ceph ceph 4096 Aug  5 10:41 dashboard
drwxr-xr-x. 7 ceph ceph 4096 Aug  5 10:41 login
drwxr-xr-x. 8 ceph ceph 4096 Aug  5 10:41 manage
```

#### 4.1.3. 停用防火墙和禁用SElinux

在测试环境可以直接停用防火墙，如果是生产环境则要建立相应iptable规则放行相应的WEB端口(通常是80):

停用防火墙使用命令：

```sh
sudo systemctl disable firewalld
sudo systemctl stop firewalld
```
 
同时还要禁用SElinux（生产环境不停用而是创建相应安全规则）

```sh
sudo sed -i s'/SELINUX=enforcing/SELINUX=disabled'/g /etc/sysconfig/selinux
```

#### 4.1.4 初始化Calamari服务器

```sh
[ceph@ceph-admin calamari]$ sudo calamari-ctl initialize
[INFO] Loading configuration..
[INFO] Starting/enabling salt...
[INFO] Starting/enabling postgres...
[INFO] Initializing database...
[INFO] Initializing web interface...
[INFO] You will now be prompted for login details for the administrative user account. This is the account you will use to log into the web interface once setup is complete.
Username (leave blank to use 'root'):
Email address: karan.singh@csc.fi
Password:
Password (again):
Superuser created successfully.
[INFO] Starting/enabling services...
[INFO] Restarting services...
```

给出用于登录到Calamari控制面板的用户名和口令。

#### 4.1.5 登录Calamari控制面板

打开浏览器输入Calamari服务器的IP地址打开Calamari控制面板登录页面，输入在上一步聚里给出的用户名和口令登录。

### 4.2. 将被监控的Ceph集群节点添加到Calamari控制面板

#### 4.2.1 在被监控的Ceph节点上安装Diamond

```sh
sudo yum install -y diamond-3.4.67-0.noarch.rpm
sudo chkconfig diamond on
```

#### 4.2.2 创建一个默认的Diamond配置文件

```sh
sudo mv /etc/diamond/diamond.conf.example /etc/diamond/diamond.conf
```

#### 4.2.3 安装salt-minion

```sh
sudo yum install -y salt-minion
sudo systemctl enable salt-minion
```

#### 4.2.4 修改salt-minion配置文件

修改/etc/salt/minion，内容为：

    master: admin

admin为Calamari服务器的主机名，在Ceph节点主机的/etc/hosts中已配置。

#### 4.2.5 启动服务

```sh
sudo systemctl start salt-minion
sudo systemctl start diamond
```

### 4.3 授权添加Ceph节点

当配置完所有Ceph集群节点后，需要在Calamari服务器上接受Ceph节点的salt key。

#### 4.3.1 查看可接受的Ceph节点

在Calamari服务器上执行：

```sh
$ sudo salt-key -L

```








