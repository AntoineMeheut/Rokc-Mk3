# Install your personal PaaS
This tutorial is designed to share with you my experience of installing PaaS on equipment that you can have at home, without transforming your living room into a server room.

This tutorial is independent and is part of a project that will assemble several of the tutorials that you will find on my GitHub, to create a portable PaaS Cloud, which allows me to do demonstrations and thus try to demistify what the Cloud is.

# Deploying OpenShift Origin 3.11 on bare metal cluster
## Inspiration
* [https://www.server-world.info/en/note?os=CentOS_7&p=openshift311&f=1](https://www.server-world.info/en/note?os=CentOS_7&p=openshift311&f=1)
* [https://www.server-world.info/en/note?os=CentOS_7&p=openshift311&f=6](https://www.server-world.info/en/note?os=CentOS_7&p=openshift311&f=6)

## Diagram of infrastructure
```
--+------------------+------------------+------------------+-------------
  |192.168.1.16      |192.168.1.17      |192.168.1.18      |192.168.1.19
+-+------------+   +-+------------+   +-+------------+   +-+------------+
|[ master ]    |   |[ node1 ]     |   |[ node2 ]     |   |[ node3 ]     |
|(Master Node) |   |(Compute Node)|   |(Compute Node)|   |(Compute Node)|
|(Infra Node)  |   |              |   |              |   |              |
|(Compute Node)|   |              |   |              |   |              |
+--------------+   +--------------+   +--------------+   +--------------+
```

## Infrastructure Setup
| Hostname | IP Address | CPU | Thread | RAM HDD | eMMC | NVMe SSD | OS | Role |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| master.openshift.hal9000.com | 192.168.1.16 | Intel m3 | 4 | 8Gb | 64Gb | 500Gb | Fedora-32 | master Node |
| node1.openshift.hal9000.com | 192.168.1.17 | Intel m3 | 4 | 8Gb | 64Gb | 500Gb | Fedora-32 | Worker Node 1 |
| node2.openshift.hal9000.com | 192.168.1.18 | Intel m3 | 4 | 8Gb | 64Gb | 500Gb | Fedora-32 | Worker Node 2 |
| node3.openshift.hal9000.com | 192.168.1.19 | Intel m3 | 4 | 8Gb | 64Gb | 500Gb | Fedora-32 | Worker Node 3 |

## Preparing all Nodes
### Download and flash Centos-7 on a USB key
Download Centos 7 [CentOS-7-x86_64-Minimal-2003.iso](http://isoredirect.centos.org/centos/7/isos/x86_64/)

Flash a USB key, plug your LattePanda and add : keyboard, mouse, screen and a SD card on the LattePanda.

### Why a SD card ?
There is a small bug in the LattePanda bios which for the moment and to my knowledge has not yet been fixed. Once you have installed Centos on the card, it will permanently display a message indicating that Centos cannot restart the hardware slot of the SD card. The way to get rid of this message is to insert an SD card into the slot.

### Install Centos on LattePanda
Connect everything and start your LattePanda by pressing F7, this allows you to have the menu of choice of the support on which you want to start.

Choose the USB key and start the installation.

Warning: your nodes must be connected to the network switch and to the router, otherwise the configuration of the Ethernet protocol will not be carried out by the Centos installation program and this is not necessarily something that you want to do by hand after installation, we already have a lot to do.

### Installing Cockpit Admin Tool on CentOS 7
Cockpit is very practical when you have to access a lot of servers and especially not to rack your brains to operate a Mackbook keyboard on a Centos server. Cockpit handles this for you and allows you to copy / paste your orders.

Of course you have to think about deactivating it, for security, once you have finished, but here we are doing a test and there are no plans to open this PaaS to the outside. It would then be necessary to address all the notions of security on the servers and the PaaS itself.

So this experiment is planned to make demonstrations on a network not connected to the Internet, which in fact secures the installation and avoids carrying out all the security part of the installation.

#### Install Cockpit
```
yum install -y cockpit
```
#### Install additional Cockpit packages
```
yum install -y cockpit-networkmanager cockpit-dashboard cockpit-storaged cockpit-packagekit
```
#### Enable Cockpit
```
systemctl enable --now cockpit.socket
```
#### Add cockpit to firewall
```
firewall-cmd --permanent --add-service=cockpit
firewall-cmd --reload
```

Before connecting with the cockpit to the node, you must locate the IP address which has been assigned to it with its current configuration in DHCP. We will use this IP to connect, modify the IP configuration file to have a static one, which will facilitate access to each node.

```
ip addr
```

From now on you can use this IP address to connect with the cockpit or continue with your current installation in terminal mode directly on the map.

### Network setup
#### Set the hostname for each corresponding node
Master

```
hostnamectl set-hostname master.hal9000.com
```
Node1

```
hostnamectl set-hostname node1.hal9000.com
```
Node2

```
hostnamectl set-hostname node2.hal9000.com
```
Node3

```
hostnamectl set-hostname node3.hal9000.com
```
#### Configure static ip
```
vi /etc/sysconfig/network-scripts/ifcfg-enp2s0
```

Change BOOTPROTO from dhcp to static and add the following lignes to the file. Do that for each nodes with IPADDR 16 for master, 17 for infra, 18, for node1 and 19 for node2.

Realy take care of the GATEWAY cause it allow you to access to internet on my network configuration it's 192.168.1.254

Take care to keep the UUID of the file you are modifying, you can delete all lines except this one.

```
TYPE=Ethernet
PROXY_METHODE="none"
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME=enp2s0
DEVICE=enp2s0
ONBOOT=yes
PROXY_METHOD=none
IPADDR=192.168.1.16
PREFIX=24
NETMASK=255.255.255.0
GATEWAY=192.168.1.254
DNS1=8.8.8.8
UUID=<quit the line in your file>
```

#### Configure names resolution
Configure /etc/hosts file for name resolution as following.

```
vi /etc/hosts
```

```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.1.16   master.hal9000.com  master
192.168.1.17   node1.hal9000.com  node1
192.168.1.18   node2.hal9000.com  node2
192.168.1.19   node3.hal9000.com  node3
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

### Openshift Origin 3.11 installation
#### Installation de OKD 3.11 et de Ansible
On all Nodes, install OpenShift Origin 3.11 repository, Ansible and Docker.

For Ansible, version 2.6, 2.7, 2.8, 2.9 are provided from CentOS Repository, but Openshift-Ansible is not supported on 2.8 or later, so install Ansible 2.7

```
yum -y install centos-release-openshift-origin311 centos-release-ansible-27
yum -y install ansible openshift-ansible docker git pyOpenSSL
systemctl enable --now docker
```

#### Now you can reboot your node
```
reboot
```

#### If you want to stop a node
LattePanda cards are powerful and heat up, no more than normal, but being able to press the tiny switch that will send the stop signal to the card, without feeling the heat of the card on your fingers is not easy.

Just use this command and it will be easier.

```
poweroff
```

**Once here, repeat the section preparing all nodes for node1, node2 and node3.**

## Connect to your nodes cockpit interfaces
You should connect on root on all ur nodes for the main part of this tutoriel.

* master [https://192.168.1.16:9090](https://192.168.1.16:9090)
* node1 [https://192.168.1.17:9090](https://192.168.1.17:9090)
* node2 [https://192.168.1.18:9090](https://192.168.1.18:9090)
* node3 [https://192.168.1.19:9090](https://192.168.1.19:9090)

## Deploying and starting Openshift Origin 3.11 from master node
### Preparation on master only
This declaration of targets for the sharing of the RSA key and the sending of keys which follow simply make it possible not to have to enter the login and password of each node in Ansible executes the installation scripts of Openshift Origin.

#### Creating an RSA key
```
ssh-keygen -q -N ""
```

#### Declare the target nodes for the key

```
vi ~/.ssh/config
```

```
Host master
    Hostname master.hal9000.com
    User root
Host node1
    Hostname node1.hal9000.com
    User root
Host node2
    Hostname node2.hal9000.com
    User root
Host node3
    Hostname node3.hal9000.com
    User root
```

#### Send the public-key to all the nodes
```
chmod 600 ~/.ssh/config
```

```
ssh-copy-id master
ssh-copy-id node1
ssh-copy-id node2
ssh-copy-id node3
```

### Preparing the hosts file for Ansible
```
vi /etc/ansible/hosts
```

```
#
# ansible inventory for OpenShift Origin Platform  3.11
#

###########################################################################
#
# Ansible script that will be used to identify the targets of the deployment
# and use the variables for Openshift Origin
#
###########################################################################

###########################################################################
# Configuring your inventory file
# https://docs.openshift.com/container-platform/3.11/install/configuring_inventory_file.html
# add follows to the end
###########################################################################

###########################################################################
### OpenShift Hosts
###########################################################################
[OSEv3:children]
masters
nodes
etcd

[masters]
master.hal9000.com openshift_schedulable=true containerized=false

[etcd]
master.hal9000.com

[nodes]
# defined values for [openshift_node_group_name] in the file below
# [/usr/share/ansible/openshift-ansible/roles/openshift_facts/defaults/main.yml]
master.hal9000.com openshift_node_group_name='node-config-master-infra'
node1.hal9000.com openshift_node_group_name='node-config-compute'
node2.hal9000.com openshift_node_group_name='node-config-compute'
node3.hal9000.com openshift_node_group_name='node-config-compute'

###########################################################################
### Ansible Vars
###########################################################################
[OSEv3:vars]
# Admin user created in previous section
ansible_ssh_user=root

# ansible_become=true

# Deployment type
openshift_deployment_type=origin
# openshift_deployment_type=openshift-enterprise

# WARNING: only disable these checks in LAB/TEST environments(Do not use in production)
openshift_disable_check="disk_availability,memory_availability"

###########################################################################
### OpenShift Registries Locations
###########################################################################
# use HTPasswd for authentication
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider'}]

###########################################################################
### OpenShift Master Vars
###########################################################################
# define default sub-domain for Master node
openshift_master_default_subdomain=apps.hal9000.com

# image for a cluster with a working registry-console in 3.11
openshift_cockpit_deployer_image='docker.io/timbordemann/cockpit-kubernetes:latest'

# allow unencrypted connection within cluster
openshift_docker_insecure_registries=192.168.1.16/16
```

### Run prerequisites playbook
```
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/prerequisites.yml
```

### Run deploy cluster playbook
```
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/deploy_cluster.yml
```

You have to get something like this.

```
INSTALLER STATUS ****************************************************************************************************************************************************************************************
Initialization               : Complete (0:00:33)
Health Check                 : Complete (0:02:18)
Node Bootstrap Preparation   : Complete (0:11:42)
etcd Install                 : Complete (0:00:55)
Master Install               : Complete (0:05:17)
Master Additional Install    : Complete (0:00:34)
Node Join                    : Complete (0:00:27)
Hosted Install               : Complete (0:00:53)
Cluster Monitoring Operator  : Complete (0:02:49)
Web Console Install          : Complete (0:01:35)
Console Install              : Complete (0:00:28)
metrics-server Install       : Complete (0:00:01)
Service Catalog Install      : Complete (0:04:50)
```

If after the execution you do not have an error message and you get a complete on all the steps, bravo! Openshift Origin is installed.

## Two commands use to verify its operation
### See the state of your nodes
```
oc get nodes
```
You have to get something like this.

```
NAME                 STATUS    ROLES          AGE       VERSION
master.hal9000.com   Ready     infra,master   20m       v1.11.0+d4cacc0
node1.hal9000.com    Ready     compute        16m       v1.11.0+d4cacc0
node2.hal9000.com    Ready     compute        16m       v1.11.0+d4cacc0
node3.hal9000.com    Ready     compute        16m       v1.11.0+d4cacc0
```

### View status with labels
```
oc get nodes --show-labels=true
```
You have to get something like this.

```
NAME                 STATUS    ROLES          AGE       VERSION           LABELS
master.hal9000.com   Ready     infra,master   20m       v1.11.0+d4cacc0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=master.hal9000.com,node-role.kubernetes.io/infra=true,node-role.kubernetes.io/master=true
node1.hal9000.com    Ready     compute        17m       v1.11.0+d4cacc0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=node1.hal9000.com,node-role.kubernetes.io/compute=true
node2.hal9000.com    Ready     compute        17m       v1.11.0+d4cacc0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=node2.hal9000.com,node-role.kubernetes.io/compute=true
node3.hal9000.com    Ready     compute        17m       v1.11.0+d4cacc0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=node3.hal9000.com,node-role.kubernetes.io/compute=true
```

## Configure the registry
We will start by cleaning up the default configuration.

```
oc get pods
```
You have to get something like this.

```
NAME                        READY     STATUS    RESTARTS   AGE
docker-registry-1-5ngph     1/1       Running   0          18m
registry-console-1-deploy   0/1       Error     0          18m
router-1-8dmf5              1/1       Running   0          19m
```

```
oc describe pod docker-registry-1-5ngph  | grep -A3 'Volumes:'
```
You have to get something like this.

```
Volumes:
  registry-storage:
    Type:    EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
```

### Delete related settings
```
oc delete all -l docker-registry=default
```
You have to get something like this.

```
pod "docker-registry-1-5ngph" deleted
replicationcontroller "docker-registry-1" deleted
service "docker-registry" deleted
deploymentconfig.apps.openshift.io "docker-registry" deleted
```

```
oc delete all -l name=registry-console
```
You have to get something like this.

```
replicationcontroller "registry-console-1" deleted
service "registry-console" deleted
deploymentconfig.apps.openshift.io "registry-console" deleted
```

```
oc delete serviceaccount registry
```
You have to get something like this.

```
serviceaccount "registry" deleted
```

```
oc delete oauthclients cockpit-oauth-client
```
You have to get something like this.

```
oauthclient.oauth.openshift.io "cockpit-oauth-client" deleted
```

### Delete if it exists
```
oc delete clusterrolebindings registry-registry-role
```

```
oc get pods
```
You have to get something like this.

```
NAME             READY     STATUS    RESTARTS   AGE
router-1-8dmf5   1/1       Running   0          22m
```

## Configure registry
For example, create a directory for container images on [node1.hal9000.com]. Node which is in [compute] role and set a registry pod which always runs on this node.

```
oc get nodes
```
You have to get something like this.

```
NAME                 STATUS    ROLES          AGE       VERSION
master.hal9000.com   Ready     infra,master   27m       v1.11.0+d4cacc0
node1.hal9000.com    Ready     compute        24m       v1.11.0+d4cacc0
node2.hal9000.com    Ready     compute        24m       v1.11.0+d4cacc0
node3.hal9000.com    Ready     compute        24m       v1.11.0+d4cacc0
```

### Create a directory for images
```
ssh node1 "sudo mkdir /var/lib/origin/registry"
```

```
ssh node1 "sudo chown origin. /var/lib/origin/registry"
```

### Set privilege to the [registry] account
```
oc adm policy add-scc-to-user privileged system:serviceaccount:default:registry
```
You have to get something like this.

```
scc "privileged" added to: ["system:serviceaccount:default:registry"]
```

### Deploy registry
```
oc adm registry \
--config=/etc/origin/master/admin.kubeconfig \
--service-account=registry \
--mount-host=/var/lib/origin/registry \
--selector='kubernetes.io/hostname=node1.hal9000.com' \
--replicas=1
```
You have to get something like this.

```
--> Creating registry registry ...
    serviceaccount "registry" created
    clusterrolebinding.authorization.openshift.io "registry-registry-role" created
    deploymentconfig.apps.openshift.io "docker-registry" created
    service "docker-registry" created
--> Success
```

Few minutes later (wait a good 5 minutes), deploy has finished and pod becomes running state.

```
oc get pods
```
You have to get something like this.

```
NAME                       READY     STATUS              RESTARTS   AGE
docker-registry-1-deploy   1/1       Running             0          4m
docker-registry-1-h55c7    0/1       ContainerCreating   0          27s
router-1-8dmf5             1/1       Running             0          29m
```

```
oc describe pod docker-registry-1-h55c7
```
You have to get something like this.

```
Name:               docker-registry-1-h55c7
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               node1.hal9000.com/192.168.1.17
Start Time:         Sun, 27 Sep 2020 07:40:05 -0400
Labels:             deployment=docker-registry-1
                    deploymentconfig=docker-registry
                    docker-registry=default
Annotations:        openshift.io/deployment-config.latest-version=1
                    openshift.io/deployment-config.name=docker-registry
                    openshift.io/deployment.name=docker-registry-1
                    openshift.io/scc=privileged
Status:             Running
IP:                 10.130.0.59
Controlled By:      ReplicationController/docker-registry-1
Containers:
  registry:
    Container ID:   docker://49f8d5462fff745bb6eb275bed435950231941401bc7d70d651984c12c7bd15e
    Image:          openshift/origin-docker-registry:v3.11.0
    Image ID:       docker-pullable://docker.io/openshift/origin-docker-registry@sha256:5c2fe22619668face238d1ba8602a95b3102b81e667b54ba2888f1f0ee261ffd
    Port:           5000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 27 Sep 2020 07:40:52 -0400
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:      100m
      memory:   256Mi
    Liveness:   http-get http://:5000/healthz delay=10s timeout=5s period=10s #success=1 #failure=3
    Readiness:  http-get http://:5000/healthz delay=0s timeout=5s period=10s #success=1 #failure=3
    Environment:
      REGISTRY_HTTP_ADDR:                                     :5000
      REGISTRY_HTTP_NET:                                      tcp
      REGISTRY_HTTP_SECRET:                                   Vrzay5ziziDdGqVYtu6bYO66mZJz3o15jc36fsMGitw=
      REGISTRY_MIDDLEWARE_REPOSITORY_OPENSHIFT_ENFORCEQUOTA:  false
    Mounts:
      /registry from registry-storage (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from registry-token-4n4cz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  registry-storage:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/origin/registry
    HostPathType:  
  registry-token-4n4cz:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  registry-token-4n4cz
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  kubernetes.io/hostname=node1.hal9000.com
Tolerations:     node.kubernetes.io/memory-pressure:NoSchedule
Events:
  Type    Reason     Age   From                        Message
  ----    ------     ----  ----                        -------
  Normal  Scheduled  2m    default-scheduler           Successfully assigned default/docker-registry-1-h55c7 to node1.hal9000.com
  Normal  Pulling    2m    kubelet, node1.hal9000.com  pulling image "openshift/origin-docker-registry:v3.11.0"
  Normal  Pulled     1m    kubelet, node1.hal9000.com  Successfully pulled image "openshift/origin-docker-registry:v3.11.0"
  Normal  Created    1m    kubelet, node1.hal9000.com  Created container
  Normal  Started    1m    kubelet, node1.hal9000.com  Started container
```

### Create User Accounts
You can use the httpd-tools package to obtain the htpasswd binary that can generate these accounts.

```
yum -y install httpd-tools
```

#### Create a user account

```
touch /etc/origin/master/htpasswd
htpasswd -b /etc/origin/master/htpasswd admin redhat
```

You have created a user, admin, with the password, redhat.

#### Restart OpenShift before going forward
```
master-restart api
master-restart controllers
```

Give this user account cluster-admin privileges, which allows it to do everything.

```
oc adm policy add-cluster-role-to-user cluster-admin admin
```

When running oc adm commands, you should run them only from the first master listed in the Ansible host inventory file, by default /etc/ansible/hosts.

You can use this username/password combination to log in via the web console or the command line. To test this, run the following command.

### 	Deploy a test application to be able to use registry normally
```
oc login -u admin
oc new-project test-project
oc new-app centos/ruby-25-centos7~https://github.com/sclorg/ruby-ex.git
oc expose svc/ruby-ex
```
```
oc status
```
You have to get something like this.

```
In project test-project on server https://master.hal9000.com:8443

svc/ruby-ex - 172.30.6.251:8080
  dc/ruby-ex deploys istag/ruby-ex:latest <-
    bc/ruby-ex source builds https://github.com/sclorg/ruby-ex.git on istag/ruby-25-centos7:latest 
      build #1 pending for 2 seconds
    deployment #1 waiting on image or update


2 infos identified, use 'oc status --suggest' to see details.
```

```
oc get pods
```
You have to get something like this.

```
NAME              READY     STATUS    RESTARTS   AGE
ruby-ex-1-build   1/1       Running   0          1m
```

```
oc describe service ruby-ex
```
You have to get something like this.

```
In project test-project on server https://master.hal9000.com:8443

svc/ruby-ex - 172.30.6.251:8080
  dc/ruby-ex deploys istag/ruby-ex:latest <-
    bc/ruby-ex source builds https://github.com/sclorg/ruby-ex.git on istag/ruby-25-centos7:latest 
      build #1 pending for 2 seconds
    deployment #1 waiting on image or update


2 infos identified, use 'oc status --suggest' to see details.
[root@master ~]# oc get pods
NAME              READY     STATUS    RESTARTS   AGE
ruby-ex-1-build   1/1       Running   0          1m
[root@master ~]# oc describe service ruby-ex
Name:              ruby-ex
Namespace:         test-project
Labels:            app=ruby-ex
Annotations:       openshift.io/generated-by=OpenShiftNewApp
Selector:          app=ruby-ex,deploymentconfig=ruby-ex
Type:              ClusterIP
IP:                172.30.6.251
Port:              8080-tcp  8080/TCP
TargetPort:        8080/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

```
curl 172.30.6.251:8080
```

### 	Enable registry console to use web based UI
oc get routes

