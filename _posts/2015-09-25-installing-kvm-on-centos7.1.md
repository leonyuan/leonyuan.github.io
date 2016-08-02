---
layout: post
title: KVM 在CentOS 7.1上安装、配置、操作指南
---
介绍
----

KVM是基于linux内核的虚拟化管理程序，在centos7中默认集成。

查看内核是否装载了kvm模块。

```sh
# lsmod | grep kvm
kvm
kvm_intel (仅在intel处理器平台)
```

检测CPU硬件是否支持虚拟化：

```sh
egrep 'vmx|svm' /proc/cpuinfo --color=always
```

- vmx：intel 平台的虚拟化特性
- svm: amd 平台的虚拟化特性

安装
----

### 2.1 安装KVM软件包

```sh
yum -y install qemu-kvm libvirt libvirt-python libguestfs-tools virt-install
```

### 2.2 开启并启动libvirtd服务

```sh
systemctl enable libvirtd && systemctl start libvirtd 
```

配置
----

### 3.1 网络配置

建立网桥：

建立网桥配置文件：/etc/sysconfig/network-scripts/ifcfg-br0，内容如下：

```sh
# cat /etc/sysconfig/network-scripts/ifcfg-br0
DEVICE=br0
TYPE=Bridge
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.101.10
NETMASK=255.255.255.0
GATEWAY=192.168.101.1
DNS1=192.168.101.1
```

原网卡配置文件内容按如下修改：

```sh
# cat /etc/sysconfig/network-scripts/ifcfg-eth0
NAME=eth0
TYPE=Ethernet
ONBOOT=yes
HWADDR=00:1d:60:a2:c1:3c
BRIDGE=br0
```

操作 
----

### 4.1 创建vm

```sh
virt-install \
    --name=vm1 \
    --controller type=scsi,model=virtio-scsi \
    --disk path=/var/lib/libvirt/images/vm1.img,size=8,sparse=true,cache=none,bus=scsi \
    --graphics vnc,listen=0.0.0.0,port=5950 \
    --network bridge=br0 \
    --vcpus=2 --ram=1024 \
    --cdrom=/var/storage/os/CentOS-7.1-1503-x86_64-DVD.iso \
    --os-type=linux \
    --os-variant=rhel7
```

### 4.2 克隆vm

#### 4.2.1 先暂停vm

```sh
virsh suspend vm1
```

#### 4.2.2 执行克隆命令

```sh
virt-clone \
    --original vm1 \
    --name vm1-clone \
    --file /var/lib/libvirt/images/vim1-clone.img
```

这将需要花费数分钟，视vm1虚拟磁盘的大小而不同。

#### 4.2.3 恢复vm

```sh
virsh resume vm1
```

#### 4.2.4 启动新克隆的vm

```sh
virsh start vm1-clone
```

### 4.3 管理vm

#### 4.3.1 一般管理

列出所有vm，包括运行中的和没在运行的。

```sh
virsh list --all
```
......


### 4.3 修改vm的名字

- 先停掉要改名的vm

```sh
virsh destroy name_of_vm
```

- 导出xml

```sh
virsh dumpxml name_of_vm > name_of_vm.xml
```

- 取消定义旧vm

```sh
virsh undefine name_of_vm
```

- 修改xml文件中的<name></name>节点内容后，重新定义新名字vm

```sh
virsh define name_of_vm.xml
```

- 启动新vm

```sh
virsh start new_name_of_vm
```
