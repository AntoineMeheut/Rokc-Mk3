<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![GNU License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]



<!-- PROJECT LOGO -->
<br />
<p align="center">
  <a href="https://github.com/AntoineMeheut/Rokc-Mk2">
    <img src="images/lattepanda.png" alt="LattePanda" width="150" height="150">
    <img src="images/openshift.png" alt="Openshift" width="150" height="150">
    <img src="images/kubernetes.png" alt="Kubernetes" width="150" height="150">
  </a>

  <h3 align="center">LattePanda Openshift Kubernetes Cluster</h3>

  <p align="center">
    Rokc Mark 2 was born out of a wall I ran into while trying to compile Openshift for ARM. It doesn't matter, his little brother is a new adventure of reflection, discovery, reading, training and sharing. By keeping the same technical objective to run a private cloud with Openshift, deploy applications and work on security to improve and share my knowledge on the Sec of DevSecOps.
    <br />
    <br />
    <a href="https://github.com/AntoineMeheut/Rock-Mk2/issues">Report Bug</a>
    ·
    <a href="https://github.com/AntoineMeheut/Rock-Mk2/issues">Request Feature</a>
  </p>
</p>



<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [My goals](#My-goals)
  * [Features](#Features)
  * [Feedback](#Feedback)
  * [Experience sharing](#Experience-sharing)
* [Hardware](#Hardware)
  * [Shopping list](#Shopping-list)
  * [Why not Intel](#Why-not-Intel)
* [Software](#Software)
  * [Clone this repo](#Clone-this-repo)
  * [Setup SD Cards](#Setup-SD-Cards)
  * [Install flash](#Install-flash)
  * [Start your Raspberry pi](#Start-your-Raspberry-pi)
  * [Configure the network](#Configure-the-network)
  * [How to Connect to a Raspberry](#How-to-Connect-to-a-Raspberry)
* [Openshift installation](#Openshift-installation) **TODO**
  * [Install Openshift on the cluster](#Install-Openshift-on-the-cluster) **TODO**
  * [Use Openshift](#Use-Openshift) **TODO**
* [Applications deployments](#Applications-deployments) **TODO**
  * [Registry](#Registry) **TODO**
  * [Who am I](#Who-am-I) **TODO**
  * [MySQL](#MySQL) **TODO**
  * [Other applications](#Other-applications) **TODO**
* [DevSecOps](#DevSecOps) **TODO**
	* [What is this](#What-is-this) **TODO**
	* [How to do](#How-to-do) **TODO**
* [Roadmap](#roadmap)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)



<!-- ABOUT THE PROJECT -->
## About The Project
This project is largely based on the :
- great repository made by [Roland Huß](https://github.com/rhuss):
[Project31/ansible-kubernetes-openshift-pi3](https://github.com/Project31/ansible-kubernetes-openshift-pi3).
- great repository made by [SitoCH](https://github.com/SitoCH):
[rpi-kubernetes-cluster](https://github.com/SitoCH/rpi-kubernetes-cluster).
- great repository made by [StefanScherer](https://github.com/hypriot):
[flash](https://github.com/hypriot/flash).

Their work was very inspiring and allowed me to learn and above all helped me a lot to achieve my goal of having a private demonstration cloud equipped for my dual [Openshit](https://www.openshift.com/) and [Kubernetes](https://kubernetes.io/) installation purpose.

### My goals

* Create a private cloud cluster from 4 [Raspberry Pi](https://www.raspberrypi.org/)
* Connect my MacBook to this cluster to manage and monitor it
* Create an arm Openshift image
* Install Kubernetes and Openshift with [Ansible](https://www.ansible.com/)
* Deploy applications on this cluster
* Start studying security, the famous Sec of DevSecOps
* Being able to move this cluster, use it for demos and therefore : all works in environments where WiFi and network are not accessible to the cluster
* Share my knowledge and recipes acquired

### Features

* Hardware pattern
* Use a bash script to prepare Raspberry Pi SD Card with [Hypriot](https://github.com/hypriot/flash)
* Explain Pi networking from a [Freebox](https://www.free.fr/freebox/)
* Use a bash script to prepare and start a running cluster with a few Ansible playbook
* Install [Docker](https://www.docker.com/)
* Install Kubernetes
* Create an arm Openshift image **TODO**
* Install Openshift **TODO**
* Install applications **TODO**
* Secure the DevOps process with best practices and tools **TODO**
* Cluster aware storage available out of the box using [GlusterFS](https://www.gluster.org/)
* Dashboard and Heapster deployed by default along with [Traefik](https://containo.us/traefik/)

### Feedback

* Building the raspberry cluster is easy, flashing SD cards with Hypriot makes it really fast and makes booting machines very fast
* Once you get to this point, your cluster is not quite active and this is where the really complicated things start
* The bash scripts that I built and the Ansible playbooks that I used from projects that inspired me and that I modified are really good, but time goes by and the structuring evolutions of the versions of Docker, Kubernetes, Kubeadm and others make it necessary to update the playbooks
* We must therefore be aware that being DevSecOps is also having to maintain these playbooks and therefore consider them as a real computer application that evolves with the changes that occur in its environment
* Building infrastructure like code and rolling out IT applications or microservices continuously involves thinking of building and deploying scripts as code
* It's therefore necessary to apply to this code all the good development practices and permanently manage versions that can be used according to the roadmap of the products used

### Experience sharing
* You may need to adapt the playbooks and scripts from this project to create your own cluster
* The way I do it's simple, I open a terminal to run scripts and playbooks, at the same time I open the project in an IDE and adapt the code of the project when necessary
* To save time in problem analysis, you can add to each ansible command the --vvv option, which allows you to have verbose mode
* I am trying to see how to create unit tests of project playbooks, in the same way that I create unit tests for projects in Java or Python **TODO**


## Hardware
### Shopping list
Here is the first part of a shopping list for a Raspberry Pi 3 cluster, with links (not affiliated) to references that I have used. I let you search for other equipment and especially how to buy this equipment according to your favorite stores.

This cluster must be able to operate without a network other than the local loop constituted by the cluster itself and the macbook which is connected to it, this allows the cluster to be taken for demonstrations in environments where the network is closed for reasons of security.

![Raspberry](images/raspberrypi.png)

| Number | Parts |
| ------ | ---- |
| 4 | [Raspberry Pi 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) |
| 4 | [Micro SD Card 64 Go](https://shop.westerndigital.com/products/memory-cards/sandisk-ultra-uhs-i-microsd#SDSQUNC-064G-AN6MA) |
| 1 | [Network switch](https://www.tp-link.com/fr/home-networking/soho-switch/ls108g/) |
| 4 | [USB Cables](https://www.startech.com/Cables/USB-2.0/Micro/mobile-charge-sync-micro-usb-cable-15cm~USBAUB15CMBK) |
| 5 | [Cat 6 Cables](https://www.startech.com/Cables/Network/Cat-6/#N6PATCH1BL) |
| 1 | [PowerPort](https://www.anker.com/products/variant/powerport-5/A2124112) |
| 1 | [Rpi Case Tower](https://www.reichelt.com/us/de/fr/fr/gehaeuse-fuer-raspberry-pi-4-tower-transparent-rpi-case-tr-19-p261681.html?r=1) |
| 1 | [Router (for demo)](https://eu.dlink.com/fr/fr/for-home/routers-and-modems) |

The router is not compulsory, it allows demonstrations outside your home.

### Why not Intel
Below is a link to a more powerful configuration based on an Intel® architecture. I started with Raspberry I had already bought for other projects and I must admit that I had some problems because of the ARM architecture, some software that I use are often built in priority for Intel® type architectures.

[SBC de type Intel®](https://www.lattepanda.com)

## Software

### Clone this repo

	git clone https://github.com/AntoineMeheut/Rokc.git Rokc
	cd Rokc

### Setup SD Cards

![SDcard](images/sdcard.jpg)

A few preliminary words on Hypriot's flash.

It's a project which allows to burn images of the Linux kernels, already ready, which embed a version of Docker, all for arm.

These images are stable and light which is an advantage to have the maximum possible space on the SD card of each Raspberry for future application deployments. For the moment there are no HDDs connected to the cluster.

You will quickly understand the advantages of using such a project, this allows you to have formatted and correctly configured cards for the cluster. Because flash has a number of options, which I let you discover to customize the flashed bone.

I will still detail the options used here to flash the cards.

* -- ssid : your wifi ssid, to allow the pi to connect to wifi
* -- password : your wifi password
* -- version : Hypriot version, something like 1.7.1 or higher, for this project I used 1.7.1

### Install flash
Flash will flash your SD Card, if you don't have it on your computer.

Download the appropriate version for Linux or Mac with this command

	curl -LO https://github.com/hypriot/flash/releases/download/2.5.0/flash

	chmod +x flash

	sudo mv flash /usr/local/bin/flash

In the scripts directory, you will use
    
	./0-flash-4sd-cards.sh
    
Run this script, it's configured to use Hypriot and flash 4 SD cards. If you need to flash more cards, you just need to change the length of a loop in the script.

At the end of the execution of this script, your 4 SD cards will be ready to be inserted in the Raspberry Pi.

![Kubernetes](images/docker.png)

### Start your Raspberry pi

It's not yet a private cloud cluster, but what a joy to finally start your assembly!

![My Cluster](images/Rokc_cluster.jpg)

Well done! We are not yet deploying thousands of microservices, but we already have a nice sound of fans.

### Configure the network

To continue it will be necessary to assign fixed DHCP addresses to each of the Raspberries. For that nothing very complicated, you can use the management console of your Internet provider router.

For me it's : [http://mafreebox.freebox.fr/index.php](http://mafreebox.freebox.fr/index.php)

I'm not using a WLAN router, because when I'm at home, I benefit from the box router of my access provider.
When I move my cluster for demos, I do not connect it to internet, I reconfigure the addresses in the host file and if necessary I run this script.

    ./1-cleanup-known-hosts.sh

The addresses I have chosen are:

| DHCP-IP                               | Device          |
| ------------------------------------- | --------------- |
| `192.168.1.9`                         | My MacBook      |
| `192.168.1.10`                        | n0 Pi master    |
| `192.168.1.11` ... `192.168.1.13`     | Nodes n1,n2,n3  |


### How to Connect to a Raspberry
Before going further you should note that it's possible to access nodes from the MacBook using ssh with **user: pirate** and **password: hypriot**
    
    ssh pirate@192.168.1.10

## Cluster creation with Kubernetes

![Cloud](images/cloud.jpg)

### Install Ansible 2.9
On a MacBook if it's not already installed you will have to execute the following commands, assuming that you have already installed brew on your computer.

    brew install ansible

### Create the hosts file

	cp hosts.example hosts
	vi hosts

   * **pis** contains all members of your cluster where one is marked as "master" in the field `host_extra`. This group will be added to every node in its `/etc/hosts`. **It's important that one host is marked as "master", since the playbooks rely on this host alias for accessing the API server**.
   * **master** IP address of the Master
   * **nodes** All nodes which are not Master
   * **volumes** Volumes to create on GlusterFS

Example

    [pis]
    192.168.1.10 name=n0 host_extra="master"
    192.168.1.11 name=n1
    192.168.1.12 name=n2
    192.168.1.13 name=n3
    
    [master]
    192.168.1.10
    
    [nodes]
    192.168.1.11
    192.168.1.12
    192.168.1.13
    
    [volumes]
    volume-1
    volume-2
    volume-3
    volume-4
    volume-5

### Setup your pis system and configuration

#### Installing sshpass
To avoid sshpass Error you will have to install:

	brew install http://git.io/sshpass.rb

#### Launching your Pis configuration
The ssh password: **hypriot**

    ansible-playbook -k -i hosts 2-setup.yml

### Install Kubernetes, star your master and nodes

    ansible-playbook -k -i hosts 3-kubernetes.yml


And now a good cup of coffee, because it will take a little time.

![Coffee](images/hot-cup-of-coffee.jpg)


### Some useful commands on kubernetes

#### Install Kubectl

	brew install kubernetes-cli

#### Configure Kubectl
You will have to connect in ssh to the master node, than copy the config file that is in ~/.kube to your Mac abd then install this file.

	mkdir ~/.kube
	vim ~/.kube/config

and copy the config file from master node to this file.

#### Kubectl commands
Now that the cluster is properly deployed and started, you want to know if it works!

Nothing simpler, type this command on your computer or after an ssh on one of the nodes of your cluster.

    kubectl get nodes

This command is also useful for checking the configuration of your cluster and checking the url on which Kubernetes is configured.

    kubectl cluster-info

If you want to know what is running on your cluster, use this command.

	kubectl -n kube-system get pod

### Deploy default applications

Once that the cluster is up and running this playbook will deploy GlusterFS endpoints, Heapster, Dashboard and Traefik:

    ansible-playbook -k -i hosts 4-applications.yml

#### Create a simple anonymous user
For testing we can do that.

	kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=system:anonymous

**Attention use RBAC if you want to secure your cluster access**

#### Download Kubernetes dashboard
	kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

#### Launch the proxy for accessing the dashboard
	kubectl proxy
	
#### Access to Kuberbetes dashboard

[http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default](http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default)

#### Kubernetes dashboard
![Dashboard](images/Dashboard.png)

## Openshift installation **TODO** 

### Compiling OpenShift for ARM **TODO**

#### Check out OpenShift Origin
	git clone https://github.com/openshift/origin

#### Select the version tag you want
	git checkout v3.11.0

  However, please note that the cross compile build only works on current master (because of [this](https://github.com/openshift/origin/commit/659ba8dadfeb25506a56da2f8a6bdc194ec4acc7) introduced lately). So the step above is probably best used when on v1.1.5 or later.

#### Install Vagrant
Vagrant works on Mac, Linux, Windows, and more. For Mac os x users use this [Vagrant](https://releases.hashicorp.com/vagrant/2.2.7/vagrant_2.2.7_x86_64.dmg)

#### Install the Vagrant box for your provider **TODO**
I use vmware fusion or docker provider for Vagrant, if you need informations on [Boxes & Providers setup](https://www.vagrantup.com/docs/providers/basic_usage.html). Here you can find the Vagrant boxes [catalogue](https://app.vagrantup.com/boxes/search). For a vmware fusion provider you could do this:

	vagrant box add hashicorp/precise64
	
**Attention** : openshift require docker, hyperv or virtualbox, so you should do this:

	vagrant box add generic/centos7

#### Startup Vagrant Developer VM **TODO**


#### Enter VM **TODO**


#### Cross compile target **TODO**


#### Cross compile run **TODO**


### Install Openshift on the cluster **TODO** 

    ansible-playbook -i hosts 5-openshift.yml

### Use Openshift **TODO** 


## Manual applications deployments

For convenience useful deployments can be found in the directory *manual-deployments*, please note that you must install the Registry in order to use other manual deployments because the images will be centrally cached.

### Registry

This container installs a Docker registry that acts as a pass-trough cache, this way only the first node will download an image from Internet and all the other requests will be served from "inside" the cluster.

    kubectl --kubeconfig run/admin.conf create -f manual-deployments/registry/registry.yml

### Who am I

Simple container that deploys on 3 nodes a website that prints it's container ID.

Thanks to Traefik you can access it on `http://master-node/whoami/`

    kubectl --kubeconfig run/admin.conf create -f manual-deployments/whoami/whoami.yml

### MySQL

MySQL database server with persistent storage.

    kubectl --kubeconfig run/admin.conf create -f manual-deployments/mysql/mysql.yml

### Other applications
**TODO** 

## Other scripts
### Reboot your cluster
If you need to reboot your cluster you can use this script, Ansible will take care of asking you to ask each Raspberry in your cluster to restart.
    
    ansible pis -i hosts --become --args "/sbin/reboot" --background 30 --forks 4 --user pi

### Halt your cluster
The operating system of raspberry pis supports very well a shutdown by power cut. It still leaves me perplexed and if you are like me, use this script, ansible will ask each of the operating systems in your cluster to stop, then you can cut the power.


    ansible pis -i hosts --become --args "/sbin/halt" --forks 4 --user pi

## DevSecOps
**TODO** 
### What is this
**TODO** 
### How to do
**TODO** 
<!-- ROADMAP -->
## Roadmap

See the [Project](https://github.com/AntoineMeheut/Rokc/projects) for a list of proposed features (and known issues).

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE` for more information.

<!-- CONTACT -->
## Contact

Antoine Méheut - [@Linkedin_antoine-meheut](https://www.linkedin.com/in/antoine-meheut)

Project Link: [https://github.com/AntoineMeheut/Rokc](https://github.com/AntoineMeheut/Rokc)

<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements

* [Roland Huß](https://github.com/rhuss) for doing the heavy lifting and creating a project ready to use and easy to deploy.

* [Lucas Käldström](https://github.com/luxas) for porting Kubernetes to the Raspberry Pi / ARM.

* [SitoCH](https://github.com/SitoCH) for doing a great job of clarifying ansible playbooks, adding Traefik installation and manual deployments

* [StefanScherer](https://github.com/hypriot) for doing a great job on Hypriot and flash, which really save a lot of time

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/AntoineMeheut/Rokc-Mk2?color=green
[contributors-url]: https://github.com/AntoineMeheut/Rokc-Mk2/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/AntoineMeheut/Rokc-Mk2
[forks-url]: https://github.com/AntoineMeheut/Rokc-Mk2/network/members
[stars-shield]: https://img.shields.io/github/stars/AntoineMeheut/Rokc-Mk2
[stars-url]: https://github.com/AntoineMeheut/Rokc-Mk2/stargazers
[issues-shield]: https://img.shields.io/github/issues/AntoineMeheut/Rokc-Mk2
[issues-url]: https://github.com/AntoineMeheut/Rokc-Mk2/issues
[license-shield]: https://img.shields.io/github/license/AntoineMeheut/Rokc-Mk2
[license-url]: https://github.com/AntoineMeheut/Rokc-Mk2/blob/master/LICENSE
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=flat-square&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/antoine-meheut
