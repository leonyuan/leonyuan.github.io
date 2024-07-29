Deploy eacloud on CentOS 7.6
============================

## 1. Introduction
Eacloud is a cloud disk application suite for company user. It's based on Ceph object storage. There must be a running Ceph cluster before deploy eacloud server. Please refer to the documentation `deploy-ceph-storage-cluster-on-centos-7.6.pdf` to deploy a Ceph cluster if there is not a Ceph cluster. 

## 2. Install dependency packages

### Install centos packages
```
# yum install lua lua-devel
# yum install luarocks openssl-devel pcre-devel
```

### Build and install openresty manually.

Download openresty-1.13.6.2 source release tarball from
https://openresty.org/download/openresty-1.13.6.2.tar.gz,
```
# wget https://openresty.org/download/openresty-1.13.6.2.tar.gz
```
Extract it, and execute
configure && gmake && gmake install to build and install it.
```
# tar xfz openresty-1.13.6.2.tar.gz
# cd openresty 
# ./configure && gmake && gmake install
```

### Install lua libraries with luarock
```
# luarocks install lapis
# luarocks install xmlua
# luarocks install xxhash
# luarocks install penlight
# luarocks install https://raw.githubusercontent.com/mikejsavage/lua-bcrypt/master/rockspec/bcrypt-1.5-1.rockspec

```

## 3. Initiate eacloud database

### Install postgresql 10 database
```
# rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
# yum install postgresql10-server postgresql10 postgresql10-contrib
```

### Before PostgreSQL can function correctly, the database must be initialized:

```
# /usr/pgsql-10/bin/postgresql-10-setup initdb
```

### Edit postgresql.conf configuration file
```
# vim /var/lib/pgsql/10/data/postgresql.conf
```
Set the `listen_addressess` to `*`.
```
listen_addressess = '*'
```

### Edit HBA configuration file
```
# nano /var/lib/pgsql/10/data/pg_hba.conf
```
Replace ident with md5, as shown in following below:
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5
```
Save and exit.

### Start and enable postgresql10 service
```
# systemctl start postgresql-10
# systemctl enable postgresql-10
```

### Create user to connect to eacloud database

```
# su postgres -c "createuser -P eacloud"
```

### Create database for eacloud

```
# su postgres -c "createdb -O eacloud -U postgres eacloud"
```

### Create tables of eacloud
First, obtain the eacloud source release tarball, e.g: eacloud-1.0.1.tar.gz, then extract it to `/var/www/eacloud` and change to the directory.
```
# mkdir -p /var/www/eacloud
# tar -C /var/www/eacloud -xzf eacloud-1.0.1.tar.gz 
# cd /var/www/eacloud
```

Issue below line to create tables of eacloud.
```
# su postgres -c "psql -U eacloud -f etc/schema-pgsql.sql eacloud"
```

## 4. Create the admin account in rgw and eacloud

### Create S3 admin user and add admin capabilities
```
# radosgw-admin user create --uid=admin --display-name=Administrator --email=admin@yubang168.cn
# radosgw-admin caps add --uid=admin --caps="users=*;buckets=*;metadata=*;usage=*"
```

### Change the s3 access key and secret key of admin user within insertadmin
function in setenv-pgsql.sh before create admin user information in eacloud user db table

```
# source setenv-pgsql.sh
# insertadmin
```

### Create an account for test
```
# ./test/create-account.py -u leon -p 1qazse4 -m leon.yuen@qq.com -a 0 -N LeonYuen -H localhost
```

## 5. Configure systemd service for eacloud
Copy the systemd service file `etc/eacloud.service` to system, then enable and start the service.
```
# cp etc/eacloud.service /usr/lib/systemd/system/
# systemctl enable eacloud
# systemctl start eacloud
```

-- The end --
