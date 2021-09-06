# Galera-cluster
csdb01 = 
csdb02 = 
csd03 = 


Adding Mariadb repo to all node
1. vi /etc/yum.repos.d/mariadb.repo
[mariadb]
6.
galera_new_cluster
7. rm /var/lib/mysql/*
( node 1)
(node2, node3)
8. systemcstrat start mariadb ( node2,node3)

