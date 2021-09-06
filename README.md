# Galera-cluster
csdb01 = 
csdb02 = 
csd03 = 


Adding Mariadb repo to all node
1. vi /etc/yum.repos.d/mariadb.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64/
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
2.
yum install mariadb-server
3. vi /etc/selinux/config
SELINUX=disabled
4. systemctal start mariadb
CREATE USER 'mariabackup'@'localhost' IDENTIFIED BY 'YOUR_PASS';
GRANT PROCESS, RELOAD, LOCK TABLES, REPLICATION CLIENT ON *.* TO 'mariabackup'@'localhost' IDENTIFIED BY ' 'YOUR_PASS' ' ;
5 vim /etc/my.cnf.d/server.conf[mysqld]
general_log = on
general_log_file=/var/lib/mysql/mysql.log
log_warnings = 1
log_error=/var/lib/mysql/mysql.log
bind-address=0.0.0.0
innodb_doublewrite=1
innodb_autoinc_lock_mode=2
innodb_flush_log_at_trx_commit=0
innodb_buffer_pool_size=2560M
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=10000
wsrep_on=ON
wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
wsrep_cluster_address="gcomm://csdb01,csdb02,csdb03"
binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
wsrep_cluster_name="csdb"
wsrep_node_address="csdb01"
# You have to change for every node
wsrep_node_name="csdb01"
# You have to change for every node
wsrep_sst_method=mariabackup
wsrep_sst_auth="mariabackup:YOUR_PASS"
wsrep_provider_options="socket.ssl_key=/etc/my.cnf.d/ssl/csdb-key.pem;socket.ssl_cert=/etc/my.cnf.d/ssl/csdb-
cert.pem;socket.ssl_ca=/etc/my.cnf.d/ssl/ca-cert.pem;socket.checksum=2;socket.ssl_cipher=AES128-SHA256"
[sst]
encrypt=4
tkey=/etc/my.cnf.d/ssl/csdb-key.pem
tcert=/etc/my.cnf.d/ssl/csdb-cert.pem
sst-log-archive=1
6.
galera_new_cluster
7. rm /var/lib/mysql/*
( node 1)
(node2, node3)
8. systemcstrat start mariadb ( node2,node3)
How to create SSL

# CA
openssl genrsa 2048 > ca-key.pem
openssl req -new -x509 -nodes -days 999999 \
-key ca-key.pem -out ca-cert.pem
# csdb
openssl req -newkey rsa:2048 -days 999999 \
-nodes -keyout csdb-key.pem -out csdb-req.pem
openssl rsa -in csdb-key.pem -out csdb-key.pem
openssl x509 -req -in csdb-req.pem -days 999999 \
-CA ca-cert.pem -CAkey ca-key.pem -set_serial 01 \
-out csdb-cert.pem
