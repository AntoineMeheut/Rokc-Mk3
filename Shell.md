# Deploying OpenShift 3.9 Cluster
## Infrastructure Setup
| Hostname | IP Address | CPU | RAM HDD | eMMC | NVMe SSD | OS | Role |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| master.openshift.hal9000.com | 192.168.1.16 | Intel m3 | 8Gb | 64Gb | 500Gb | Fedora-32 | master Node |
| infra.openshift.hal9000.com | 192.168.1.17 | Intel m3 | 8Gb | 64Gb | 500Gb | Fedora-32 | infra Node |
| node1.openshift.hal9000.com | 192.168.1.30 | Intel m3 | 8Gb | 64Gb | 500Gb | Fedora-32 | Worker Node 1 |
| node2.openshift.hal9000.com | 192.168.1.32 | Intel m3 | 8Gb | 64Gb | 500Gb | Fedora-32 | Worker Node 2 |

### Install Centos-7 on all your server
[CentOS-7-x86_64-Minimal-2003.iso](http://isoredirect.centos.org/centos/7/isos/x86_64/)

### Installing Cockpit Admin Tool on CentOS 7 for all nodes
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
### Connect to your nodes interfaces
You should connect on root on all ur nodes for the main part of this tutoriel, you will have to create a user on the master node to install ansible.

* master [https://192.168.148.130:9090](https://192.168.148.130:9090)
* infra [https://192.168.148.131:9090](https://192.168.148.131:9090)
* node1 [https://192.168.148.132:9090](https://192.168.148.132:9090)
* node2 [https://192.168.148.133:9090](https://192.168.148.133:9090)

## Preparing Nodes
### Step 1: Set the hostname for all nodes
```
hostnamectl set-hostname master.openshift.hal9000.com
```
```
hostnamectl set-hostname infra.openshift.hal9000.com
```
```
hostnamectl set-hostname node1.openshift.hal9000.com
```
```
hostnamectl set-hostname node2.openshift.hal9000.com
```
### Step 2: Configure static ip for all nodes
```
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

Change BOOTPROTO from dhcp to static and add the following lignes to the file. Do that for each nodes with IPADDR 130 for master, 131 for infra, 132, for node1 and 133 for node2.

Realy take care of the GATEWAY cause it depend on your virtualization software, for VMware Fusion it's 192.168.148.2

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
NAME=ens33
DEVICE=ens33
ONBOOT=yes
PROXY_METHOD=none
IPADDR=192.168.148.133
PREFIX=24
NETMASK=255.255.255.0
GATEWAY=192.168.148.2
DNS1=8.8.8.8
UUID=c96bc909-188e-ec64-3a96-6a90982b08ad
```

### Step 2: Configure names resolution for all node
Configure /etc/hosts file for name resolution as following on all nodes.

```
vi /etc/hosts
```

```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.148.130   master.openshift.hal9000.com  master
192.168.148.131   infra.openshift.hal9000.com  infra
192.168.148.132   node1.openshift.hal9000.com  node1
192.168.148.133   node2.openshift.hal9000.com  node2
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

### Step 3: Install docker on all nodes
#### Install docker
##### Install the dnf-plugins-core package (which provides the commands to manage your DNF repositories) and set up the stable repository
```
yum install -y yum-utils
```

```
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
##### Install the latest version of Docker Engine and containerd, or go to the next step to install a specific version
```
yum install docker-ce docker-ce-cli containerd.io
```
##### Start Docker
```
systemctl start docker
```
#### Ennable and status Docker
```
systemctl enable docker && systemctl status docker
```
### Step 4: Configure ansible repository on master node only
#### Install needed packages
```
yum install -y wget git nano net-tools bind-utils iptables-services bridge-utils \
bash-completion kexec-tools sos psacct openssl-devel httpd-tools NetworkManager \
python-cryptography python-devel python-passlib \
java-1.8.0-openjdk-headless "@Development Tools"

```
#### Pepare ansible repo
```
vi /etc/yum.repos.d/ansible.repo
```

```
[ansible]
name = Ansible Repo
baseurl = https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/
enabled = 1
gpgcheck =  0
```

### Step 5: Install ansible package and clone Openshift-Ansible git repo on master machine only
#### Install ansible
```
yum install epel-release
yum -y install python-pip
pip install --upgrade setuptools
pip install ansible==2.6.20
yum -y install pyOpenSSL

```
### Step 6: Install openshift-ansible 3.9 scripts
```
git clone https://github.com/openshift/openshift-ansible.git
cd openshift-ansible
git fetch
git checkout release-3.9
```

### Step 7: Update system and reboot on all nodes

```
yum update -y
reboot
```

### Step 7: Generate SSH keys on master node and install it on all nodes
Disconnect from user account and reconnect witj root account.

```
ssh-keygen -f ~/.ssh/id_rsa -N ''
```

```
for host in master.openshift.hal9000.com node1.openshift.hal9000.com node2.openshift.hal9000.com infra.openshift.hal9000.com ; do ssh-copy-id -i ~/.ssh/id_rsa.pub $host; done
```

### Step 8: Create your own inventory file for Ansible on master node

```
vi ~/inventory.ini
```

```
# Create an OSEv3 group that contains the masters, nodes, and etcd groups
[OSEv3:children]
master
nodes
etcd

[master]
master.openshift.hal9000.com openshift_ip=192.168.148.130

[etcd]
master.openshift.hal9000.com openshift_ip=192.168.148.130

[nodes]
master.openshift.hal9000.com openshift_ip=192.168.148.130 openshift_schedulable=true
infra.openshift.hal9000.com openshift_ip=192.168.148.131 openshift_schedulable=true openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
node1.openshift.hal9000.com openshift_ip=192.168.148.132 openshift_schedulable=true openshift_node_labels="{'region': 'primary', 'zone': 'default'}"
node2.openshift.hal9000.com openshift_ip=192.168.148.133 openshift_schedulable=true openshift_node_labels="{'region': 'primary', 'zone': 'default'}"


[OSEv3:vars]
# SSH user, this user should allow ssh based auth without requiring a password
ansible_user=root
# If ansible_ssh_user is not root, ansible_become must be set to true
# ansible_become=true

deployment_type=origin
openshift_deployment_type=origin

openshift_enable_service_catalog=true
ansible_service_broker_install=true

containerized=false
os_sdn_network_plugin_name='redhat/openshift-ovs-multitenant'
openshift_disable_check=disk_availability,docker_storage,memory_availability,docker_image_availability

openshift_node_kubelet_args={'pods-per-core': ['10']}

openshift_release=v3.9.0
openshift_pkg_version=-3.9.0
openshift_image_tag=v3.9.0
openshift_service_catalog_image_version=v3.9.0
template_service_broker_image_version=v3.9.0
osm_use_cockpit=true

# Install the router on dedicated infra node
openshift_hosted_router_selector='region=infra'
openshift_master_default_subdomain=apps.openshift.hal9000.com
openshift_public_hostname=master.openshift.hal9000.com

# Install the image registry on dedicated infra node
openshift_hosted_registry_selector='region=infra'
```

### Step 9: Check prerequisites to deply OpenShift cluster on master Node

```
ansible-playbook -i ~/inventory.ini playbooks/prerequisites.yml
```

### Step 10: Deploy OpenShift cluster on master node

```
ansible-playbook -i ~/inventory.ini playbooks/deploy_cluster.yml
```

Now you have to wait approx 20-30 minutes to complete the installation. 

### Step 12: Create an admin user in OpenShift with Password from master node

For writing this tutorial I use : PasSSw0rd, remember to replace this password with a much more robust one.

#### Install httpd-tools on master

`htpasswd -c /etc/origin/master/htpasswd admin`

```
New password: PasSSw0rd
Re-type new password: PasSSw0rd
```

`ls -l /etc/origin/master/htpasswd`

`cat /etc/origin/master/htpasswd`

#### Open /etc/origin/master/master-config.yaml file and do the following changes

```
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: allow_all
    provider:
      apiVersion: v1
      kind: HTPasswdPasswordIdentityProvider		## This line
      file: /etc/origin/master/htpasswd			## This line
```

#### Restart the below services

`systemctl restart origin-master-controllers.service`

`systemctl restart origin-master-api.service`

### Step 13: Assign cluster-admin role to admin user

`oc adm policy add-cluster-role-to-user cluster-admin admin`

### Step 14: Login as admin user on Openshift CLI

`oc login`

```
Authentication required for https://master.hal9000.com:8443 (openshift)
Username: admin
Password: PasSSw0rd
Login successful
```

You have access to the following projects and can switch between them with 'oc project <projectname>':

  * default
  * kube-public
  * kube-system
  * logging
  * management-infra
  * openshift
  * openshift-infra
  * openshift-node
  * openshift-web-console

Using project "default".

`oc whoami`

`admin`

### Step 15: List the projects, pods, nodes, replication controllers, services and deployment Config

`oc get projects`

```
NAME                    DISPLAY NAME   STATUS
default                                Active
kube-public                            Active
kube-system                            Active
logging                                Active
management-infra1                       Active
openshift                              Active
openshift-infra1                        Active
openshift-node                         Active
openshift-web-console                  Active
```

`oc get nodes`

```
NAME                 STATUS    ROLES     AGE       VERSION
master.hal9000.com   Ready     master    29m       v1.9.1+a0ce1bc657
node1.hal9000.com    Ready     compute   22m       v1.9.1+a0ce1bc657
node2.hal9000.com    Ready     compute   22m       v1.9.1+a0ce1bc657
```

`oc get pod --all-namespaces`

```
NAMESPACE               NAME                          READY     STATUS    RESTARTS   AGE
default                 docker-registry-1-krmpx       1/1       Running   0          15m
default                 registry-console-1-z9sbd      1/1       Running   0          14m
default                 router-1-q7g5v                1/1       Running   0          15m
openshift-web-console   webconsole-5f649b49b5-hr9dq   1/1       Running   0          14m
```

`oc get rc `

```
NAME                 DESIRED   CURRENT   READY     AGE
docker-registry-1    1         1         1         15m
registry-console-1   1         1         1         14m
router-1             1         1         1         16m
```

`oc get svc`

```
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                   AGE
docker-registry    ClusterIP   172.30.34.127    <none>        5000/TCP                  16m
kubernetes         ClusterIP   172.30.0.1       <none>        443/TCP,53/UDP,53/TCP     35m
registry-console   ClusterIP   172.30.62.197    <none>        9000/TCP                  15m
router             ClusterIP   172.30.141.143   <none>        80/TCP,443/TCP,1936/TCP   16m
```

`oc get dc`

```
NAME               REVISION   DESIRED   CURRENT   TRIGGERED BY
docker-registry    1          1         1         config
registry-console   1          1         1         config
router             1          1         1         config
```

#### Verifying multiple etcd hosts

On the master host, verify the etcd cluster health, substituting for the FQDNs of your etcd hosts in the following.

`yum install etcd -y`

`etcdctl -C https://master.openshift.hal9000.com:2379 --ca-file=/etc/origin/master/master.etcd-ca.crt --cert-file=/etc/origin/master/master.etcd-client.crt --key-file=/etc/origin/master/master.etcd-client.key cluster-health`

```
member b211b9f9d8f23828 is healthy: got healthy result from https://192.168.56.10:2379
cluster is healthy
```

Also verify the member list is correct.

`etcdctl -C https://master.openshift.hal9000.com:2379 --ca-file=/etc/origin/master/master.etcd-ca.crt --cert-file=/etc/origin/master/master.etcd-client.crt --key-file=/etc/origin/master/master.etcd-client.key member list`


```
b211b9f9d8f23828: name=master.hal9000.com peerURLs=https://192.168.56.10:2380 clientURLs=https://192.168.56.10:2379 isLeader=true
```

Now you can access OpenShift Cluster using Web Browser as following.


[https://192.168.148.130:8443 ](https://192.168.148.130:8443)

* Username: admin
* Password: PasSSw0rd


### Step 17: Create a demo project 

* Click on "Create Project"
	* Name: demo
	* Display Name: demo project
	* Description: This is Demo Project
* Click on Create
* Now Deploy an application in demo project for testing
	* Click in demo project -> Click on Brows Catalog -> Select PHP -> Next
	* Version : 7.0 - latest
	* Application Name: demoapp1
	* Git Repo: https://github.com/sureshchandrarhca15/OpenShift39.git
* Next -> Finish
* Now click on Overview in demo project
* See, demoapp1 build is running. so wait for build completion.

* Once build completed, there is a Pod Running for testapp and will have an URL as below
	* [http://demoapp1-demo.apps.openshift.hal9000.com ](http://demoapp1-demo.apps.openshift.hal9000.com)
	* Now click on Pod Icon to get the details.

NOTE: we don't have DNS to resolve App URL. So, we are going to use /etc/hosts file to resolve it using infra node IP Address. Because on infra node our router pod is running so all traffic will be routed from infra node in OpenShift Cluster. 

`vim /etc/hosts`

```
#Add the below line
192.168.0.53	demoapp1-demo.apps.openshift.hal9000.com
```

`:wq`

Now Click on Pod URL Link and application should be accessible. 

NOTE: You have to configure /etc/hosts file on that system from where you are accessing the Openshift Dashboard. 
