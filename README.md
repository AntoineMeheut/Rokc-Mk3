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
    Rokc Mark 2 was born out of a wall I ran into while trying to compile Openshift for ARM on Rokc-Mk1 project. It doesn't matter, his little brother is a new adventure of reflection, discovery, reading, training and sharing. By keeping the same technical objective to run a private cloud with Openshift, deploy applications and work on security to improve and share my knowledge on the Sec of DevSecOps.
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
* [Create a private cloud](#Create-a-private-cloud)
  * [Why create a private cloud](#Why-create-a-private-cloud)
  * [Some information about data](#Some-information-about-data)
  * [OpenShift presentation](#OpenShift-presentation)
  * [Why use Openshift](#Why-use-Openshift)
  * [What can be done with Openshift](#What-can-be-done-with-Openshift)
  * [A bit of vocabulary](#A-bit-of-vocabulary)
  * [How Openshift works](#How-Openshift-works)
* [Hardware](#Hardware)
  * [Shopping list](#Shopping-list)
* [Software](#Software)
  * [Clone this repo](#Clone-this-repo)
  * [Setup SD Cards](#Setup-SD-Cards)
  * [Install flash](#Install-flash)
  * [](#)

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
The previous version of Rokc allowed me to create a Kubernetes Cloud on Raspberry Pi and to share the knowledge acquired
for the hardware, the various installations, the ssh, the network and Ansible. I hit a wall, when I tried to compile
openshift origin for ARM, I unfortunately did not manage to get an installable image on recent versions of openshift.
It does not matter, after some research I decided to use Lattepanda machines, because I always want to be able to carry
my Cloud with me, to show it to all the people who are wondering what a Cloud really is. .

I hope that this sharing will be useful to you and will allow you to embark in your turn in the understanding
and the manufacture of a Cloud. You will see it is not that complex.

So let's go ...

### My goals

* Create a private cloud cluster from 4 [Lattepanda](https://www.lattepanda.com)
* Connect my MacBook to this cluster to manage and monitor it
* Install Openshift with [Ansible](https://www.ansible.com/)
* Deploy applications on this cluster
* Start studying security, the famous Sec of DevSecOps
* Being able to move this cluster, use it for demos and therefore : all works in environments where WiFi and network
are not accessible to the cluster
* Share my knowledge and recipes acquired

### Features

* Hardware pattern
* 

### Feedback

* 

### Experience sharing
* You may need to adapt the playbooks and scripts from this project to create your own cluster
* The way I do it's simple, I open a terminal to run scripts and playbooks, at the same time I open the project
in an IDE and adapt the code of the project when necessary
* To save time in problem analysis, you can add to each ansible command the --vvv option, which allows you to have
verbose mode

## Create a private cloud
### Why create a private cloud
The first question we could ask ourselves is: "Why have a private cloud in my information system".

Well the answer is quite simple: "I do not wish to entrust my data and my applications to third parties". Indeed,
before enjoying the advantages of a Cloud, you must think about the value and the confidentiality of the data
and computer processing that you are going to deploy on this Cloud. I remain personally convinced that what has
the most values is and will always restore the customers. And it is true that more and more companies are concerned
about the value of the data that its customers entrust to it and also of the algorithms that process this data.

It then becomes obvious that you cannot entrust just anything to just anyone and that reading the GCU of the different
Cloud providers is very important.

To convince you, I have placed in the next section some links to very current topics about data and
what can be done with it.

### Some information about data
Many governments, companies and individuals are aware today and take action to know and regulate the use
of the data they entrust. To convince you, I can quote:

* The Cloud Act which in 2018 raised awareness, because this law notably allows American courts to solicit
from operators the personal communications of an individual without the latter being informed, nor his country
of residence is not, nor that the country where these data are stored is.
[CLOUD Act] (https://www.congress.gov/bill/115th-congress/senate-bill/2383/text)
* The GDPR (General Data Protection Regulation) is a regulation of the European Union which strengthens
and unifies data protection for individuals within the European Union.
[GDPR] (https://afcdp.net/reglement-europeen-rgpd-indexe-commente/)
* An example of the Google Drive GCU, which explains very clearly that Google can use your information to improve
its services. [Google Drive GCU] (https://support.google.com/drive/answer/2450387?hl=en)

### OpenShift presentation
OpenShift is a PaaS (Platform-as-a-Service) solution. In general, companies use it to build,
deploy and run applications in containers. It is available in open source under the Apache 2.0 license,
it is available in two products: Origin (community) or Enterprise.

Openshift is clearly carried by Red Hat and the main contributors to this project mostly work at Red Hat.

Since version 3 and the big redesign, Openshift relies completely on Docker and Kubernetes from Google. The life of this
product will therefore depend on these two backbones.

### Why use Openshift
If I had to retain a main reason for using Openshift, well I would say that it is much easier for a Cloud team
to benefit from the Docker encapsulation work and especially Kubernetes which is carried out by the Red Hat teams.
This makes it possible to concentrate on the services to be provided to end customers in companies such as trades
and projects.

Creating and running a private cloud in production requires a lot of technical skills and time, using Openshift
has a cost, of course, and this allows you to have support and a stabilized base by the Red Hat teams.

### What can be done with Openshift
Typically, build and deploy applications automatically. There are several ways to do this, the main ones
are :

* Docker File mode, which allows you to automatically build a Docker container by providing OpenShift the address
of a source code manager pointing to a Docker File and its dependencies. We use it a lot to migrate projects from
old virtual environments to our Cloud.
* Source To Image mode which allows you to automatically build an application by pushing the source code application
in OpenShift. I use it to make starter-kits for trades, it indicates a Git repository where is the code that a startup
has built with them, their credentials and presto the application starts.
* Custom Build mode which allows you to provide your own logic for building an application by providing OpenShift
a Docker image designed for this purpose. It can be used for applications which have an original stack compared
to most of the usual applications deployed on a Cloud. This allows for tailor-made.
 
One of the very interesting advantages of OpenShift is that it allows you to define an automated deployment strategy
of an application when a new image version is published in the registry or when the configuration
of the application is updated. Typically it is taking advantage of the best advantages of DevSecOps for projects
or trades.

In addition to these build and deployment modes, OpenShift offers the possibility of defining its own application
“blue prints” in the form of “template” files in Json or Yaml format. These "blue prints" describe both the topology
of the application architecture and the container deployment policy. This makes it possible to industrialize application
 deployments for a company that has already worked on the standardization of its development stacks.

### A bit of vocabulary
Some concepts that are important to understand before starting:

* POD: it is a Docker container execution environment local to a server,
* Service: it is an entry point (VIP) making abstract "load-balanced" access to a group of identical containers.
In principle, we deploy a Service by third of the architecture,
* Deployment Config: this is an object that describes a deployment policy for a container based on triggers
(for example: redeploy when a new version of an image is available in the Docker registry),
* Replication Controller: it is a technical component in charge of POD resilience,
* Route: A route exposes an entry point (DNS hostname or VIP) outside of an application.

### How Openshift works
Quickly, the node or nodes which control the others serve:

* processing requests to the administration API,
* to carry out image build operations and container deployment,
* to ensure the resilience (replication) of the PODs where your applications operate,
* he or they use a distributed etcd directory for configuration sharing and service discovery.

The managed nodes host the PODs and run containers (application and / or Registry).

It is possible to interact with the platform through its REST API, CLI or via its Web portal.

## Hardware
### Shopping list
Here is the first part of a shopping list, with links (not affiliated) to references that I have used.
I let you search for other equipment and especially how to buy this equipment according to your favorite stores.

This cluster must be able to operate without a network other than the local loop constituted by the cluster itself
and the macbook which is connected to it, this allows the cluster to be taken for demonstrations in environments
where the network is closed for reasons of security.

![LattePanda Alpha 864](images/LattepandaAlpha864s.jpg)

| Number | Parts |
| ------ | ---- |
| 4 | [LattePanda Alpha 864](https://www.lattepanda.com/products/lattepanda-alpha-864s.html) |
| 4 | [Crucial P1 500Go](https://www.crucial.fr/ssd/p1/ct500p1ssd8) |
| 1 | [Network switch](https://www.tp-link.com/fr/home-networking/soho-switch/ls108g/) |
| 5 | [Cat 6 Cables](https://www.startech.com/Cables/Network/Cat-6/#N6PATCH1BL) |
| 1 | [Router (for demo)](https://eu.dlink.com/fr/fr/for-home/routers-and-modems) |

### Cluster assembly
You will have to mount the SSD disks in their location on the back of the Lattepanda Alpha, it is not very complicated.

The WiFi antennas are not mounted and available separately in the box, it is not mandatory to mount them, because
they are intended to be glued on a box for the Lattepanda and that for this project we will use
a card connection with RJ45.

In the end, I still mounted the WiFi antennas, in case I need this type of connection. As I chose
to mount the 4 Lattepanda with the support screws of the Rapsberry cluster from the previous project, so I glued
Wifi antennas on SSDs, it's not very clean as an installation, but it does the trick.

![Cluster](images/cluster.jpg)

## Software
### Lattepanda software installation
The first thing to do is to install an OS on the cards, I chose to use Fedora, because it is
an open source distribution that is closest to RHEL and that I find very well designed and practical to use
when you start on the creation of a Cloud.

[https://getfedora.org](https://getfedora.org)

The version I used is a server ISO: Fedora-Server-dvd-x86_64-32-1.6.iso

With this version you will therefore not have a desktop on your OS, but do not panic, we will see below, how
use the web console of each server, to avoid a lot of command line.

### Comment installer les OS
The first thing to do is download the iso and burn it on a usb key. For that I use a standard 16 Gb usb key
and this software [etcher](https://www.balena.io/etcher/)

Then it is very important to proceed in this way, I carry out the assembly part of the cards in cluster and
their connection to the network before installing the OS. Apart from the fact that this is always how I do it
usually this allows the OS installer software to detect the network and activate the software layer
network at the time of installation. If you installed a Fedora OS server on a card without it being connected to the
network, when you start it, the network software layer will not be active and you will not be able to connect
in ssh à la carte.

After mounting the cluster, connecting them to the network switch and to the portable router, we get that.

![Cluster](images/cluster2.jpg)

### Router settings
So that the OS installation software can access the internet and also so that you can access your
SSH servers from your computer, whether at home or on the go, you need to install a mini network
local.

For this we will use the dlink Router and configure it to have the operating mode in Range Extender
Fashion. The documentation of these small pieces of equipment is well done, you shouldn't have too many problems. And like
everything related to networks can sometimes seem confusing, I will try to show you what to do.

![Cluster](images/cluster4.jpg)

### Router Operation
The mode of action of the router that we are going to choose is this. This allows you to connect your computer by RJ45
to the network loop in which your cluster is located and this allows you to access the internet through your
home wifi network. This allows you to make the necessary updates to your Cloud and it allows you to take
your Cloud with you for demonstrations.

![Routeur](images/routeur1.jpg)

### Installing the OS on the cards
You must then connect a screen, a keyboard, a mouse and the installation usb key, in turn on
each card and install the OS.

### End of assembly and SSH access to servers
Once the 4 cards with their OS installed, you can then connect your computer to the local network of your cluster
and access the web interface that will allow you to update your OS and perform all the operations you
will need and this even with the command line. You can therefore access your 4 cards from the same computer
and therefore: we put the screen away, the calvier and the smile!

You must then locate the IP address of each of your cards and connect to the Fedora server interface of
each card.

The address is like this: https: //XXX.XXX.X.XX: 9090

![fedora interface](images/fedora1.jpg)

### Clone this repo

	git clone https://github.com/AntoineMeheut/Rokc-Mk2.git Rokc-Mk2
	cd Rokc-Mk2

### Ansible playbook for Fedora software installation
https://github.com/fabaff/fedora-ansible
https://github.com/mrlesmithjr/ansible-kvm
https://github.com/kclymans/libvirt-install
https://github.com/robertdebock/ansible-role-libvirt
https://docs.google.com/document/d/1gHautHUc4vSRkzhZrwSONeBMVh9oXgq16EoF7wn0i0c/edit
https://github.com/openshift/installer/blob/4a97eccab5e432a1318d15ff064ede78dca05d4a/docs/dev/libvirt/README.md

### KVM check on Fedora

### libvirt check

### Ansible playbook for virtual machines creation
https://github.com/toshywoshy/ansible-role-vminstaller

### Ansible playbook for Openshift installation
https://www.youtube.com/watch?v=4mFMqNExRWk
https://github.com/luisarizmendi/ocp-libvirt-ipi-role

### Infrastructure as code application deployment

### Openshift monitoring

### Application deployment

### Application monitoring

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

Contributions are what make the open source community such an amazing place to be learn, inspire, and create.
Any contributions you make are **greatly appreciated**.

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
* [Fabian Affolter](https://github.com/fabaff/fedora-ansible) Ansible playbook for Fedora software installation
was inspire from is repository
* 


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
