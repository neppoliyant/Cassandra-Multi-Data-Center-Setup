#Cassandra Deployment

Cassandra on CentOS
-------------------
Java Install
```sh
yum -y update
yum -y install java
```

Add the DataStax Community Repository
--------------------------------------
```sh
vim /etc/yum.repos.d/datastax.repo

[datastax]
name = DataStax Repo for Apache Cassandra
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0

Install Apache Cassandra 2

yum -y install dsc20
```

Basic Configuration
-------------------
```sh
vim /etc/cassandra/conf/cassandra.yaml

cluster_name: 'Name'
initial_token: Token
seed_provider:
    - seeds:  "Seed IP"
listen_address: Droplet's IP
rpc_address: 0.0.0.0
endpoint_snitch: GossipingPropertyFileSnitch
```

Configure MultiData Center:
---------------------------
 
cassandra.yaml
--------------
```sh
Cluster Name update (Cluster name should be same accross all the data center otherwise application will connect to only one datacenter and if that is down apllication wont connect to other datacenter but if cluster name is same it will connect to all the datacanter.)
Cqlsh <<ip>>
UPDATE system.local SET cluster_name = 'NGC DEV Cluster' where key='local';
Exit
nodetool flush (to commit the update and never use remove data files that will messs the cqlsh)
```

cassandra-rackdc.properties
---------------------------
```sh
dc=DC1
rack=rack1
```
cassandra-topology.properties
-----------------------------
```sh
# Cassandra Node IP=Data Center:Rack
<<DC2 IP>>=DC2:rack1
<<DC2 IP2>>=DC2:rack1
<<DC1 IP>>=DC1:rack1
<<DC1 IP2>>=DC1:rack1
<<DC1 IP3>>=DC1:rack1
```

Commands
--------
```sh
service cassandra start
service cassandra stop
service cassandra restart
```

Authentication
--------------
```sh
vim /etc/cassandra/conf/cassandra.yaml
authenticator: PasswordAuthenticator

User Creation 
CREATE USER <<USERNAME>> WITH PASSWORD '<<PASSWORD>>’;
```sh

Cassandra OpsCenter & Agent
---------------------------
```sh
1.     Open the Yum repository specification /etc/yum.repos.d for editing. For example:
a.     sudo vi /etc/yum.repos.d/datastax.repo
2.     In this file, add the repository for OpsCenter.
[opscenter]
name = DataStax Repository
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0
3.     Install the OpsCenter package.
a.     sudo yum install opscenter
4.     Start OpsCenter:
a.     sudo service opscenterd start
5.     Connect to OpsCenter in a web browser using the following URL:
a.     http://opscenter-host:8888/
```

Agent
-----
```sh
1.     Add the DataStax Yum repository in the /etc/yum.repos.d/datastax.repo file.
[datastax]
name = DataStax Repo for Apache Cassandra
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0
2.     Install the DataStax agent.
a.     yum install datastax-agent
3.     In address.yaml set stomp_interface to the IP address that OpsCenter is using. You might have to create the file.
a.     echo "stomp_interface: reachable_opscenterd_ip" | sudo tee -a /var/lib/datastax-agent/conf/address.yaml
4.     If SSL communication is enabled in opscenterd.conf, use SSL in address.yaml.
a.     echo "use_ssl: 1" | sudo tee -a /var/lib/datastax-agent/conf/address.yaml
5.     Start the DataStax agent.
a.     sudo service datastax-agent start
```
