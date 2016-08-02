---
layout: post
title: 使用Chrony同步时间
---
在CentOS7中，可用新的工具Chrony来为主机作时间同步。Chrony可同时扮演NTP Client和NTP Server的角色，而且它非常简单。

## 安装Chrony

```sh
sudo yum install -y chrony
```

## 配置Chrony

编辑：/etc/chrony.conf

注释原有的server，加入本地区公共时间同步服务器。
访问：<http://www.pool.ntp.org/zone/cn>，获取中国区公用时间同步服务器。如：

- server 3.cn.pool.ntp.org
- server 3.asia.pool.ntp.org
- server 2.asia.pool.ntp.org

```sh
cat /etc/chrony.conf
```
    ......
    # 注释原有的server
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst

    # 加入本地区公共时间同步服务器
    server 3.cn.pool.ntp.org iburst
    server 3.asia.pool.ntp.org iburst
    server 2.asia.pool.ntp.org iburst
    ......

## 启用Chrony

```sh
sudo systemctl enable chronyd.service
sudo systemctl start chronyd.service
```

## 检查NTP来源状态

```sh
$ chronyc sourcestats
210 Number of sources = 3
Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
==============================================================================
202.118.1.130              10   4   461     23.358    166.175  +2734us    16ms
dns1.synet.edu.cn          12   7   525    -13.554    114.141  -2144us    15ms
ns2.sol.az                  8   5   397    -47.513      8.539    -40ms   629us
```

## 查看NTP详细同步状态

```sh
$ chronyc sources -v
210 Number of sources = 3

.-- Source mode  '^' = server, '=' = peer, '#' = local clock.
/ .- Source state '*' = current synced, '+' = combined , '-' = not combined,
| /   '?' = unreachable, 'x' = time may be in error, '~' = time too variable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||                                                /   xxxx = adjusted offset,
||         Log2(Polling interval) -.             |    yyyy = measured offset,
||                                  \            |    zzzz = estimated error.
||                                   |           |                         
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^+ 202.118.1.130                 2   6   337    95  -7332us[-4357us] +/-   75ms
^* dns1.synet.edu.cn             2   6   377    30  +2326us[+3677us] +/-   81ms
^- ns2.sol.az                    2   6   377   158    -32ms[  -31ms] +/-  224ms
```
---
（完）
