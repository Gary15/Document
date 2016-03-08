#Ubuntu14.04 Install RackHD
This document is describing How to build the RackHD server and How to use the RackHD service to deploy physical machine.
##Build Environment 

###1. Install VirtualBox
Please reference to official document to install latest VirtualBox version [https://www.virtualbox.org/wiki/Linux_Downloads](https://www.virtualbox.org/wiki/Linux_Downloads "Oracle VM VirtualBox Download Page")

Here is my step:

- Add the following line to /etc/apt/sources.list:

		$ deb http://download.virtualbox.org/virtualbox/debian trusty contrib
- Download the key file and add it

		$ wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
- Download the deb package to install  

		$ wget http://download.virtualbox.org/virtualbox/5.0.14/virtualbox-5.0_5.0.14-105127~Ubuntu~trusty_amd64.deb
- Install the following package to get ready install VirtualBox

		$ sudo apt-get install -y dkms fakeroot libfakeroot libsdl-ttf2.0-0 libsdl1.2debian
- Install VirtualBox

		$ sudo dpkg -i virtualbox-5.0_5.0.14-105127~Ubuntu~trusty_amd64.deb

###2. Install Vagrant, Ansible, git 
If you want know more knowledge about Vagrant and Ansible please refer to these official doc page:[https://www.vagrantup.com/docs/](https://www.vagrantup.com/docs/ "Vagrant Doc"), [http://docs.ansible.com/ansible/](http://docs.ansible.com/ansible/ "Ansible Doc")

Here is my step:

- Download the Vagrant package ([https://releases.hashicorp.com/vagrant/](https://releases.hashicorp.com/vagrant/ "Vagrant Download Page") ) and install it

		$ wget https://releases.hashicorp.com/vagrant/1.7.4/vagrant_1.7.4_x86_64.deb
		$ sudo dpkg -i vagrant_1.7.4_x86_64.deb
	**NOTE**: Do not use Vagrant 1.8.0, as the private network settings don't appear
to be working correctly. Bug filed upstream with Vagrant at 
https://github.com/mitchellh/vagrant/issues/6730.
- Install Ansible by using pip install

		$ sudo apt-get -y install python-pip
		$ sudo pip install ansible
- Install git 

		$ sudo apt-get -y install git
- Get RackHD open source code from Github
	
		$ git clone https://github.com/RackHD/RackHD

##Start RackHD service

- Change into the directory `example`
	
		$ cd ~/RackHD/example
- Bring up the VirtualBox

		$ vagrant up dev
	**NOTE**: This step will download the vagrant box from https://atlas.hashicorp.com and it costs too much time and this method can not support the "Breakpoint Continuingly". You could copy the link choose another way to download the vagrant box to localhost. And then import it by change `target.vm.box` from `"rackhd/rackhd"` to `"<Absolute Path of the box on you host>"` define in `Vagrantfile`.
- Login to the VM, Start the service

		$ vagrant ssh
		$ sudo nf start
- Update the second NIC from closed-net to bridged, so that the physical server can get the service

	**Note**: When you deployed the physical machine may lead to this issue [https://github.com/RackHD/RackHD/issues/93](https://github.com/RackHD/RackHD/issues/93 "issue").

##Test
Once you've started the services, the RackHD API will be available on your local machine through port 9090. For example, you should be able to view the RackHD API documentation that's set up with the service at http://localhost:9090/docs.

You can also interact with the APIs using curl from the command line of your local machine.

To view the list of nodes that has been discovered:

		$ curl http://localhost:9090/api/1.1/nodes | python -m json.tool
View the list of catalogs logged into RackHD:

		$ curl http://localhost:9090/api/1.1/catalogs | python -m json.tool
(both of these should result in empty lists in a brand new installation)
