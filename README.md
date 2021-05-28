# harvest_install


Prerequisits
* Ansible
** community.docker collection
*** This collection needs the python library ‘docker’
* Docker
* Inbound ports 3000 and 9090 open

Setup
Edit the harvest/harvest.yml file and add your ONTAP cluster(s).  Here is an example from my lab.
Pollers:
  Cluster-1:
    datacenter: DC-01
    addr: 10.0.0.1
    auth_style: basic_auth
    prometheus_port: 25000
    username: myuser
    password: mypasw
  Cluster-2:
    datacenter: DC-01
    addr: 10.0.0.2
    auth_style: basic_auth
    prometheus_port: 25001
    username: myuser
    password: mypasw
Where my example says ‘Cluster-1’ and ‘Cluster-2’ this is the name of the cluster.  This doesn’t have to match the cluster hostname – it can be anything.  The ‘datacenter:’ section is how you can group clusters together via region or purpose.  Finally for each cluster you add be sure you increment the value of ‘prometheus_port:’.
Once you have entered your clusters in the harvest.yml file, you can start the services with the command
$ ansible-playbook manage_harvest.yml
This will pull the three image files you need from docker hub, update the Prometheus.yml file with the systems and ports you included in harvest.yml and deploy Prometheus, Grafana, and one (1) Harvest container per cluster.  So if you have just one cluster to monitor, 3 containers will deploy.  Two clusters will deploy 4 containers and so on.  Since the containers are all from the same image, they don’t take up extra space.
Now that the containers are all running, run the playbook again with an ‘api’ tag to setup the Prometheus datasource and Harvest dashboards in Grafana.
$ ansible-playbook manage_harvest.yml --tags api
You now have Harvest setup and running.  Point your browser at the IP or hostname of the machine where you did the installation.
http://localhost:3000
username: admin
password: pass

