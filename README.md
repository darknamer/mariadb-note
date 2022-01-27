# mariadb-note

Example code in Redhat 7.9

## Download links
[Community link download](https://dlm.mariadb.com/browse/mariadb_server/)

## Packages
### Suggest package install list
- MariaDB-backup-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-client-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-common-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-connect-engine-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-server-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-shared-10.6.5-1.el7.centos.x86_64.rpm
- galera-4-26.4.9-1.el7.centos.x86_64.rpm

### Full list download file
- galera-4-26.4.9-1.el7.centos.x86_64.rpm
- MariaDB-backup-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-client-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-columnstore-engine-10.6.5_6.2.1-1.el7.centos.x86_64.rpm
- MariaDB-common-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-compat-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-connect-engine-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-cracklib-password-check-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-devel-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-gssapi-server-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-oqgraph-engine-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-rocksdb-engine-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-server-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-shared-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-s3-engine-10.6.5-1.el7.centos.x86_64.rpm
- MariaDB-test-10.6.5-1.el7.centos.x86_64.rpm

# Upgrade
Show list of installed packages in OS
```
yum list installed
```

Show the current version of MariaDB Server
```
mariadbd --version
```

Before upgrade, backup the data
```
mysqldump -u root -p --all-databases > all_databases.sql
```

Remove old version
```
yum erase MariaDB* galera.x86_64
```

Install new version
```
cd <path rpm packages>
yum install -y MariaDB* galera*
```

After upgrade successfully and start service again. Run command mysql_upgrade for migrate data from old version to new version in mysql.
```
mysql_upgrade -u root -p
```


# Setup galera cluster

Edit /etc/my.cnf.d/server.cnf

```
vi /etc/my.cnf.d/server.cnf
```

Edit follow by syntax
```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster"
wsrep_cluster_address="gcomm://First_Node_IP,Second_Node_IP,Third_Node_IP"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="This_Node_IP"
wsrep_node_name="This_Node_Name"
```

Example config in server.cnf

```
#
# These groups are read by MariaDB server.
# Use it for options that only the server (but not clients) should see
#
# See the examples of server my.cnf files in /usr/share/mysql/
#

# this is read by the standalone daemon and embedded servers
[server]

# this is only for the mysqld standalone daemon
[mysqld]

#
# * Galera-related settings
#
[galera]
# Mandatory settings
wsrep_on=ON
wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
wsrep_cluster_name="casanova_cluster"
wsrep_cluster_address="gcomm://10.198.74.160,10.197.78.222"
wsrep_sst_method=rsync
wsrep_node_address="10.198.74.160"
wsrep_node_name="be_sila"

binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2

#
# Allow server to accept connections on all interfaces.
#
bind-address=0.0.0.0

# Optional setting
#wsrep_slave_threads=1
#innodb_flush_log_at_trx_commit=0

# this is only for embedded server
[embedded]	

# This group is only read by MariaDB servers, not by MySQL.
# If you use the same .cnf file for MySQL and MariaDB,
# you can put MariaDB-only options here
[mariadb]

# This group is only read by MariaDB-10.6 servers.
# If you use the same .cnf file for MariaDB of different versions,
# use this group for options that older servers don't understand
[mariadb-10.6]
```

# Edit /etc/my.cnf.d/galera.cnf

Edit follow by syntax

```
# Galera Node Configuration
wsrep_node_address="This_Node_IP"
wsrep_node_name="This_Node_Name"
```

## Start MariaDB Server in First Node

Use commands
```
galera_new_cluster
```

## Start MariaDB Server in Other Node

Use commands
```
systemctl start mariadb
```

# References

[Mariadb galera cluster on centos 7 - Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-configure-a-galera-cluster-with-mariadb-on-centos-7-servers)

[how to restart mariadb galera cluster](https://dba.stackexchange.com/questions/151941/how-to-restart-mariadb-galera-cluster)