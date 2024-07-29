---
layout: post
title: Deployment ceph cluster within docker
---

### Create specific network for ceph nodes
```
$ docker network create --driver bridge ceph-net
$ docker network inspect ceph-net
{
    "Subnet": "172.18.0.0/16",
    "Gateway": "172.18.0.1/16"
}
```

### Deploy the first monitor daemon
```
$ docker run -d --net=ceph-net \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
-e MON_IP=172.18.0.2 \
-e CEPH_PUBLIC_NETWORK=172.18.0.0/16 \
--name mon1 \
ceph/daemon:latest-luminous mon
```

### Check if the monitor daemon is ok
```
$ docker exec -ti mon1 ceph -v
ceph version 12.2.12 (1436006594665279fe734b4c15d7e08c13ebd777) luminous (stable)
$ docker exec -ti mon1 ceph -s
  cluster:
    id:     60e46743-3cf3-43fb-bd5f-c70cc3390280
    health: HEALTH_OK
 
  services:
    mon: 1 daemons, quorum 0dcaba64ad8d
    mgr: no daemons active
    osd: 0 osds: 0 up, 0 in
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0B
    usage:   0B used, 0B / 0B avail
    pgs:     
```

### Deploy the second monitor daemon
```
$ docker run -d --net=ceph-net \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
-e MON_IP=172.18.0.3 \
-e CEPH_PUBLIC_NETWORK=172.18.0.0/16 \
--name mon2 \
ceph/daemon:latest-luminous mon
```

### Deploy the third monitor daemon
```
$ docker run -d --net=ceph-net \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
-e MON_IP=172.18.0.4 \
-e CEPH_PUBLIC_NETWORK=172.18.0.0/16 \
--name mon3 \
ceph/daemon:latest-luminous mon
```

### Deploy a Manager daemon
$ docker run -d --net=ceph-net \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
--name mgr1 \
ceph/daemon:latest-luminous mgr

### Deploy the first OSD daemon
```
$ docker run -d --net=ceph-net \
--privileged=true --pid=host \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
-v /dev/:/dev/ \
-e OSD_DEVICE=/dev/sdb \
--name osd1 \
ceph/daemon:latest-luminous osd
```
Perhaps occure error, you could zap the disk before try again.
```
$ docker run -d --privileged=true \
-v /dev/:/dev/ \
-e OSD_DEVICE=/dev/sdb \
ceph/daemon:latest-luminous zap_device
```

### Deploy the second OSD daemon
```
$ docker run -d --net=ceph-net \
--privileged=true --pid=host \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
-v /dev/:/dev/ \
-e OSD_DEVICE=/dev/sdc \
--name osd2 \
ceph/daemon:latest-luminous osd
```

### Deploy a Rados Gateway
$ docker run -d --net=ceph-net \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph/:/var/lib/ceph/ \
-v /var/log/ceph/:/var/log/ceph/ \
-p 8080:8080 \
--name rgw1 \
ceph/daemon:latest-luminous rgw

