# galera-cluster-ubuntu
Playbook to deploy Galera Cluster

<img src="http://galeracluster.com/wp-content/uploads/2014/05/Galera-Cluster-logo.jpg">


```bash

apt-get install ansible 
pip install ansible==XXX

```

* Ansible Version == 2.2.1.0
* Galera Version == 5.6.35
* S.O == Ubuntu Xenial 16.04.2 LTS



# Steps before deploy:

1º Change IP address in vars/main.yml

```bash

mysql_galera1: "IP-1-MACHINE-GALERA"
mysql_galera2: "IP-2-MACHINE-GALERA"
mysql_galera3: "IP-3-MACHINE-GALERA"
```

2º Also change ROOT Password Galera

```bash
mysql_password: "XXXXXXXXXXXXXXXXXX"
```



# Bring up the First Node

The way we've configured our cluster, each node that comes online tries to connect to at least one other node specified in its galera.cnf file to get its initial state. A normal systemctl start mysql would fail because there are no nodes running for the first node to connect with, so we need to pass the wsrep-new-cluster parameter to the first node we start. However, neither systemd nor service will properly accept the --wsrep-new-cluster argument at this time, so we'll need to start the first node using the startup script in /etc/init.d. Once you've done this, you can start the remaining nodes with systemctl.

Note: If you prefer them all to be started with systemd, once you have another node up, you can kill the initial node. Since the second node is available, when you restart the first one with sudo systemctl start mysql it will be able to join the running cluster


```bash

sudo /etc/init.d/mysql start --wsrep-new-cluster && tail -f /var/log/mysql/error.log

```



# Bring up the Second & Third Node

```bash

sudo systemctl start mysql && tail -f /var/log/mysql/error.log

```



# Testing

If everything is working well, the cluster size should be set to three:


```bash

myql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"


Output
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 3     |
+--------------------+-------+

```
# Licence

MIT

# Author Information

David Álvarez Quiroga
