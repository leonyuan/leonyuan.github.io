Deploy Ceph storage cluster on CentOS 7.6
=========================================

## 1. Introduction
This documentation describes how to install ceph and deploy ceph storage cluster on CentOS Linux release 7.6.
The installed version of ceph is luminous 12.2.12. There are three nodes in the cluster:

```
^ hostname  ^     IP      ^     roles       ^
|cephnode01 |172.31.30.2  |mon,mgr,osd,rgw |
|cephnode02 |172.31.30.3  |mon,mgr,osd,rgw |
|cephnode03 |172.31.30.4  |mon,mgr,osd,rgw |
```

CentOS Linux release 7.6.1810 has been installed on the three nodes, and there are 4x8TB SAS disks used to deploy ceph OSDs in the each node.

```
# cat /etc/centos-release
CentOS Linux release 7.6.1810 (Core)
```

## 2. Install software

### Install ceph packages in the each node.

```
# sudo yum -y install ceph ceph-radosgw
```

### Check if ceph is installed successfully.
```
# ceph -v
ceph version 12.2.12 (1436006594665279fe734b4c15d7e08c13ebd777) luminous (stable)
```

## 3. Deployment the first MON daemon on cephnode01

### Generate a uuid for fsid of the ceph cluster
```
[root@cephnode01]# uuidgen
9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
```
### Create ceph conf file /etc/ceph/ceph.conf with the following lines.
```
[root@cephnode01]# cat /etc/ceph/ceph.conf
[global]
fsid = 9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
#设置node1为mon节点
mon initial members = cephnode01
#设置mon节点地址
mon host = 172.31.30.2
public network = 172.31.30.0/24
cluster network = 172.31.31.0/24
auth cluster required = cephx
auth service required = cephx
auth client required = cephx
osd journal size = 1024
#设置副本数
osd pool default size = 2
#设置最小副本数
osd pool default min size = 2
osd pool default pg num = 64
osd pool default pgp num = 64
osd crush chooseleaf type = 1
osd_mkfs_type = xfs
max mds = 5
mds max file size = 100000000000000
mds cache size = 1000000
#设置osd节点down后900s，把此osd节点逐出ceph集群，把之前映射到此节点的数据映射到其他节点。
mon osd down out interval = 900
mon pg warn max per osd = 800

[mon]
#把时钟偏移设置成0.5s，默认是0.05s,由于ceph集群中存在异构PC，导致时钟偏移总是大于0.05s，为了方便同步直接把时钟偏移设置成0.5s
mon clock drift allowed = .50
```

### Create a keyring for the first MON daemon
```
[root@cephnode01]# ceph-authtool --create-keyring /tmp/ceph.mon.keyring --gen-key -n mon. --cap mon 'allow *'
creating /tmp/ceph.mon.keyring
```

### Create admin user's keyring for the ceph cluster
```
[root@cephnode01]# ceph-authtool --create-keyring /etc/ceph/ceph.client.admin.keyring --gen-key -n client.admin --set-uid=0 --cap mon 'allow *' --cap osd 'allow *' --cap mds 'allow *' --cap mgr 'allow *'
creating /etc/ceph/ceph.client.admin.keyring
```

### Create a bootstrap-osd keyring for the ceph cluster
```
[root@cephnode01]# ceph-authtool --create-keyring /var/lib/ceph/bootstrap-osd/ceph.keyring --gen-key -n client.bootstrap-osd --cap mon 'profile bootstrap-osd'
creating /var/lib/ceph/bootstrap-osd/ceph.keyring
```

### Import admin keyring and bootstrap-osd keyring to the MON keyring
```
[root@cephnode01]# ceph-authtool /tmp/ceph.mon.keyring --import-keyring /etc/ceph/ceph.client.admin.keyring
importing contents of /etc/ceph/ceph.client.admin.keyring into /tmp/ceph.mon.keyring
[root@cephnode01]# ceph-authtool /tmp/ceph.mon.keyring --import-keyring /var/lib/ceph/bootstrap-osd/ceph.keyring
importing contents of /var/lib/ceph/bootstrap-osd/ceph.keyring into /tmp/ceph.mon.keyring
```

### Create monitor map with the hostname and IP of the first MON node as well as fsid.
```
[root@cephnode01]# monmaptool --create --add cephnode01 172.31.30.2 --fsid 9c8d61b0-0ef8-4a75-ad1c-f059442d46a2 /tmp/monmap
monmaptool: monmap file /tmp/monmap
monmaptool: set fsid to 9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
monmaptool: writing epoch 0 to /tmp/monmap (1 monitors)
```

### Create the data directory for the MON daemon
```
[root@cephnode01]# sudo -u ceph mkdir /var/lib/ceph/mon/ceph-cephnode01
```

### Chown ceph.mon.keyring to ceph user & group
```
[root@cephnode01]# chown ceph.ceph /tmp/ceph.mon.keyring
```

### Make files to initiate the first MON daemon
```
[root@cephnode01]# sudo -u ceph ceph-mon --mkfs -i cephnode01 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring
ceph-mon: set fsid to 9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
ceph-mon: created monfs at /var/lib/ceph/mon/ceph-cephnode01 for mon.cephnode01
```

### Create a empty file to prevent re-initiate the MON daemon
```
[root@cephnode01]# touch /var/lib/ceph/mon/ceph-cephnode01/done
```

### Start the first mon daemon
```
[root@cephnode01]# systemctl start ceph-mon@cephnode01
```

### Query the status of the ceph cluster
```
[root@cephnode01]# ceph -s
  cluster:
    id:     9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
    health: HEALTH_OK
 
  services:
    mon: 1 daemons, quorum cephnode01
    mgr: no daemons active
    osd: 0 osds: 0 up, 0 in
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0B
    usage:   0B used, 0B / 0B avail
    pgs:     
```

### Enable the first MON daemon to start automatically when the host booting
```
[root@cephnode01]# systemctl enable ceph-mon@cephnode01
```

## 4. Deployment the second MON daemon on cephnode02

### Copy config file and keyring files to the second node
```
[root@cephnode01]# scp /etc/ceph/* root@cephnode02:/etc/ceph/
[root@cephnode01]# scp /var/lib/ceph/bootstrap-osd/ceph.keyring root@cephnode02:/var/lib/ceph/bootstrap-osd/
[root@cephnode01]# scp /tmp/ceph.mon.keyring root@cephnode02:/tmp/ceph.mon.keyring
[root@cephnode01]# scp /tmp/monmap root@cephnode02:/tmp/monmap
```

### Create data directory of the mon node2
```
[root@cephnode02]# sudo -u ceph mkdir /var/lib/ceph/mon/ceph-cephnode02
```

### Chown ceph.mon.keyring to ceph user & group
```
[root@cephnode02]# chown ceph.ceph /tmp/ceph.mon.keyring
```

### Initiate mon node2
```
[root@cephnode02]# sudo -u ceph ceph-mon --mkfs -i cephnode02 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring
ceph-mon: set fsid to 9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
ceph-mon: created monfs at /var/lib/ceph/mon/ceph-cephnode02 for mon.cephnode02
```

### Create a empty file to prevent re-initiate the mon 
```
[root@cephnode02]# touch /var/lib/ceph/mon/ceph-cephnode02/done
```

### Add the mon node2 to mon list of the cluster
```
[root@cephnode02]# ceph mon add cephnode02 172.31.30.3:6789
adding mon.cephnode02 at 172.31.30.3:6789/0
```

### Start the mon node2
```
[root@cephnode02]# systemctl start ceph-mon@cephnode02
```

### Enable the mon daemon to start automatically when the host booting
```
[root@cephnode02]# systemctl enable ceph-mon@cephnode02
```

## 5. Deployment the third MON daemon on cephnode03
The deployment process of the third MON daemon is same to the second MON daemon's, thus ignore this process.

## 6. Deployment a manager daemon on cephnode01

### Create an authentication keyring for the manager daemon, which to be installed on cephnode01
```
[root@cephnode01]# ceph auth get-or-create mgr.cephnode01 mon 'allow profile mgr' osd 'allow *' mds 'allow *'
```

### Export keyring information of mgr.cephnode01 to a keyring file and copy it to mgr data directory as filename `keyring`
```
[root@cephnode01]# ceph auth get mgr.cephnode01 > /tmp/mgr-keyring
[root@cephnode01]# cat /tmp/mgr-keyring
[mgr.cephnode01]
        key = AQBAHlZdlVxqKhAACQrCMiSkU0Kyrjx+ujCr7A==
        caps mds = "allow *"
        caps mon = "allow profile mgr"
        caps osd = "allow *"

[root@cephnode01]# sudo -u ceph mkdir -p /var/lib/ceph/mgr/ceph-cephnode01
[root@cephnode01]# cp /tmp/mgr-keyring /var/lib/ceph/mgr/ceph-cephnode01/keyring
[root@cephnode01]# chown -R ceph.ceph /var/lib/ceph/mgr/ceph-cephnode01
```

### Start the manager daemon
```
[root@cephnode01]# systemctl start ceph-mgr@cephnode01
```

### Query the status of the ceph cluster
```
[root@cephnode01]# ceph -s
  cluster:
    id:     9c8d61b0-0ef8-4a75-ad1c-f059442d46a2
    health: HEALTH_OK
 
  services:
    mon: 2 daemons, quorum cephnode01,cephnode02
    mgr: cephnode01(active)
    osd: 0 osds: 0 up, 0 in
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0B
    usage:   0B used, 0B / 0B avail
    pgs:     
```

### Enable the manager start automatically when os booting
```
[root@cephnode01]# systemctl enable ceph-mgr@cephnode01
```

## 7. Deployment a manager daemon on cephnode02 and cephnode03
The deployment process on cephnode02 or cephnode03 is same to cephnode01's, thus omit it.

## 8. Prepare OSDs deployment

### Create 3 buckets in the cluster crush before deploy the first osd
```
[root@cephnode01]# ceph osd crush add-bucket cephnode01 host
added bucket cephnode01 type host to crush map
[root@cephnode01]# ceph osd crush add-bucket cephnode02 host
added bucket cephnode02 type host to crush map
[root@cephnode01]# ceph osd crush add-bucket cephnode03 host
added bucket cephnode03 type host to crush map
```

### Move the 3 buckets to default root
```
[root@cephnode01]# ceph osd crush move cephnode01 root=default
moved item id -2 name 'cephnode01' to location {root=default} in crush map
[root@cephnode01]# ceph osd crush move cephnode02 root=default
moved item id -3 name 'cephnode02' to location {root=default} in crush map
[root@cephnode01]# ceph osd crush move cephnode03 root=default
moved item id -4 name 'cephnode03' to location {root=default} in crush map
```

## 9. Create some OSDs on cephnode01 node.
First in all, because of there are 4x8TB SAS disks setup in every nodes, thus we'll deploy 4 OSD daemons on every nodes.
The four SAS disks are /dev/sde, /dev/sdf, /dev/sdg, /dev/sdh.

### Prepare disk for OSD 0
```
[root@cephnode01]# ceph-volume lvm create --data /dev/sde
```

### Prepare disk for OSD 1
```
[root@cephnode01]# ceph-volume lvm create --data /dev/sdf
```

### Prepare disk for osd 2
```
[root@cephnode01]# ceph-volume lvm create --data /dev/sdg
```

### Prepare disk for osd 3
```
[root@cephnode01]# ceph-volume lvm create --data /dev/sdh
```

## 10. Create some OSDs on cephnode02 node.
The deployment process is same to cephnode01's.

### Prepare disk for OSD 4
```
[root@cephnode02]# ceph-volume lvm create --data /dev/sde
```

### Prepare disk for OSD 5
```
[root@cephnode02]# ceph-volume lvm create --data /dev/sdf
```

### Prepare disk for osd 6
```
[root@cephnode02]# ceph-volume lvm create --data /dev/sdg
```

### Prepare disk for osd 7
```
[root@cephnode02]# ceph-volume lvm create --data /dev/sdh
```

## 11. Create some OSDs on cephnode03 node.
The deployment process is same to cephnode01's.

### Prepare disk for OSD 8
```
[root@cephnode03]# ceph-volume lvm create --data /dev/sde
```

### Prepare disk for OSD 9
```
[root@cephnode03]# ceph-volume lvm create --data /dev/sdf
```

### Prepare disk for osd 10
```
[root@cephnode03]# ceph-volume lvm create --data /dev/sdg
```

### Prepare disk for osd 11
```
[root@cephnode03]# ceph-volume lvm create --data /dev/sdh
```

### Query OSDs list
```
[root@cephnode01]# ceph osd tree
ID CLASS WEIGHT   TYPE NAME           STATUS REWEIGHT PRI-AFF 
-1       87.32867 root default                                
-2       29.10956     host cephnode01                         
 0   hdd  7.27739         osd.0           up  1.00000 1.00000 
 1   hdd  7.27739         osd.1           up  1.00000 1.00000 
 2   hdd  7.27739         osd.2           up  1.00000 1.00000 
 3   hdd  7.27739         osd.3           up  1.00000 1.00000 
-3       29.10956     host cephnode02                         
 4   hdd  7.27739         osd.4           up  1.00000 1.00000 
 5   hdd  7.27739         osd.5           up  1.00000 1.00000 
 6   hdd  7.27739         osd.6           up  1.00000 1.00000 
 7   hdd  7.27739         osd.7           up  1.00000 1.00000 
-4       29.10956     host cephnode03                         
 8   hdd  7.27739         osd.8           up  1.00000 1.00000 
 9   hdd  7.27739         osd.9           up  1.00000 1.00000 
10   hdd  7.27739         osd.10          up  1.00000 1.00000 
11   hdd  7.27739         osd.11          up  1.00000 1.00000 
```

## 12. Deployment rados gateway daemon on cephnode01

### Create some rados pools for rgw
The required pools are:
- .rgw.root
- default.rgw.control
- default.rgw.meta
- default.rgw.log
- default.rgw.buckets.index
- default.rgw.buckets.data

```
# ceph osd pool create .rgw.root 64
# ceph osd pool set .rgw.root size 2
# ceph osd pool set .rgw.root pgp_num 64
```

Or create all pools via a shell script:

```
# cat create-rgw-pools.sh
#!/bin/bash

PG_NUM=64
PGP_NUM=64
SIZE=2

for i in `cat pools`; do
  ceph osd pool create $i $PG_NUM
  ceph osd pool set $i size $SIZE
done

for i in `cat pools`; do
  ceph osd pool set $i pgp_num $PGP_NUM
done

# cat pools
.rgw.root
default.rgw.control
default.rgw.meta
default.rgw.log
default.rgw.buckets.index
default.rgw.buckets.data

# sh create-rgw-pools.sh
```
Note that, this step *MUST* be done only one time.

### Create a keyring for rgw
```
[root@cephnode01]# ceph-authtool --create-keyring /etc/ceph/ceph.client.radosgw.keyring
creating /etc/ceph/ceph.client.radosgw.keyring
```

### Change the owner of the keyring file to ceph
```
[root@cephnode01]# chown ceph:ceph /etc/ceph/ceph.client.radosgw.keyring
```

### Generate user and key for rgw 
```
[root@cephnode01]# ceph-authtool /etc/ceph/ceph.client.radosgw.keyring -n client.rgw.cephnode01 --gen-key
```

### Add caps to rgw user
```
[root@cephnode01]# ceph-authtool -n client.rgw.cephnode01 --cap osd 'allow rwx' --cap mon 'allow rwx' /etc/ceph/ceph.client.radosgw.keyring
```

### Import the keyring to the ceph cluster
```
[root@cephnode01]# ceph -k /etc/ceph/ceph.client.admin.keyring auth add client.rgw.cephnode01 -i /etc/ceph/ceph.client.radosgw.keyring
added key for client.rgw.cephnode01
```

### Edit /etc/ceph/ceph.conf to append the following config

```
[client.rgw.cephnode01]
host=cephnode01
keyring=/etc/ceph/ceph.client.radosgw.keyring
log file=/var/log/ceph/radosgw.log
rgw_s3_auth_use_keystone = False
rgw_frontends = civetweb port=8080
debug rgw = 20
```

### Start and enable the rgw daemon
```
[root@cephnode01]# systemctl start ceph-radosgw@rgw.cephnode01
[root@cephnode01]# systemctl enable ceph-radosgw@rgw.cephnode01
```

## 13. Deploy rgw daemon on cephnode02 and cephnode03
The deployment process on cephnode02 is same to cephnode01's, thus omit it.

-- The end --
