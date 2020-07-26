# MariaDB_cluster_admin

### MASTER ###
#1 add repo repo
# MariaDB 10.5 CentOS repository list - created 2020-07-25 21:34 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.5/centos8-amd64
module_hotfixes=1
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

#2 install the packages
sudo yum -u install MariaDB-server MariaDB-client galera-4

#3 firewall config

sudo firewall-cmd --zone=public --add-port=4567/tcp --permanent
sudo firewall-cmd --zone=public --add-port=4568/tcp --permanent
sudo firewall-cmd --zone=public --add-port=4444/tcp --permanent
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --zone=public --add-port=13306/tcp --permanent
sudo firewall-cmd --reload

#4 selinux config
sudo semanage port -m -t mysqld_port_t -p tcp 4567
sudo semanage port -a -t mysqld_port_t -p tcp 4568
sudo semanage port -m -t mysqld_port_t -p tcp 4444
sudo semanage permissive -a mysqld_t


#5 create /etc/my.cnf.d/node0.cnf
[mysqld]
bind-address=78.141.212.193

[galera]
wsrep_on=ON
wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
binlog_format=ROW
wsrep_cluster_name='galera_cluster'
wsrep_node_name='node0'
wsrep_cluster_address='gcomm://'

#6 start the service
sudo systemctl start mariadb.service

#7 confirm cluster address
SHOW GLOBAL VARIABLES LIKE 'wsrep_cluster_address';

#8 confirm cluster status
SHOW GLOBAL STATUS WHERE Variable_name IN ('wsrep_ready', 'wsrep_cluster_size', 'wsrep_cluster_satus','wsrep_connected');

#NODE 1##

#1 repeate the steps until #4

#2 create /etc/my.cnf.d/node1.cnf
[mysqld]
bind-address=node1 IP address

[galera]

wsrep_on=ON
wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
binlog_format=ROW
wsrep_cluster_address='gcomm://master ip address'
wsrep_cluster_name='galera_cluster'
wsrep_node_name='nodeX'
