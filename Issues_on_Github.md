#Issue Status
##Closed
###Issue List
####1. [Boot from ipxe failed on Dell R610](https://github.com/RackHD/RackHD/issues/52 "https://github.com/RackHD/RackHD/issues/52")
- Issue description

	After deploy the RackHD server by following link [https://github.com/RackHD/RackHD/blob/master/example/README.md](https://github.com/RackHD/RackHD/blob/master/example/README.md). And it can demo successfully on VirtualBox.
	
	Then I update the VM the network Adapter to Brideged Adapter.Connect it to Dell Server(R610).It gets stuck in "random: nonblocking pool is initialized".[Stuck Picture](https://photos.google.com/share/AF1QipO5GBWTvqcPrMLcEXbspenOxShAZLSKFksXrGiA25etHxcfowUAV12A2jyApbVwpw/photo/AF1QipOJIdrjz0WI2V6hslbRHUnp2celUe0-ZIM7jQc8?key=X25UbFR6bVRvUVhhMEJQM0w0SUdMQXRIMmJBaVNn).
- Root Cause

	The microkernel kernel do not support the hardware platform.
- Resolved 

	RackHD member provides the Older microkernel files in a zip file at https://www.dropbox.com/s/ph372nd29fw320s/old_microkernel.zip?dl=0 that you can download.Replace the files in the VM at /home/vagrant/src/on-http/static/http/common/ with these files

	Or rebulid the microkernel kernel then replace them, Please see the Issue reference.
- Issue reference

	[microkernel build process doesn't include req HW drivers when made in VMs](https://github.com/RackHD/RackHD/issues/57 "https://github.com/RackHD/RackHD/issues/57")
	
	[Add linux-image-extras package to initrd build environment](https://github.com/RackHD/on-imagebuilder/issues/16 "https://github.com/RackHD/on-imagebuilder/issues/16")

###2. [Microkernel build process doesn't include req HW drivers when made in VMs](https://github.com/RackHD/RackHD/issues/57 "https://github.com/RackHD/RackHD/issues/57")
- Issue description
	
	The microkernels created using cloud resources (in a VM, or through TravisCI) don't include all the underlying drivers for the kernel such that booting some physical machines fails - the discovery kernel hangs at boot up.

- Root Cause

	Apparently since Ubuntu 14.04 the main bulk of kernel drivers have been kept in the linux-image-extras-<version> package, so that the main linux-image-<version> package could be slimmed down for virtual machine images. 

- Resolved

	We need to include this package in our initrd build environment so that we actually have all the required HW drivers to boot physical machines! Some of the primary ones that have been missing are usb-hid keyboard support (makes TTY1 hang, which explains partly what we've been seeing) and network device driver support.

####3. [Can not get the "centos.ks" file](https://github.com/RackHD/RackHD/issues/68 "https://github.com/RackHD/RackHD/issues/68")
- Issue description

	After fixed the discovery OS hang on, the discovery tasks can be done and the IPMI-OBM-Service can be worked. Also installing coreOS would be right.
	
	Then trying to deliver a task to install CentOS.The vmlinuz and initrd of CentOS can be transfered successfully.But the automatic scipt ".ks" file can not be transfered, occurs HTTP GET 500 error.[error picture](https://photos.google.com/share/AF1QipO5GBWTvqcPrMLcEXbspenOxShAZLSKFksXrGiA25etHxcfowUAV12A2jyApbVwpw/photo/AF1QipN_XkFAnufLfadUj-BB114fsFgD-e5K7hXuc28C?key=X25UbFR6bVRvUVhhMEJQM0w0SUdMQXRIMmJBaVNn). So the client go to Dracut Emergency Shell. [Shell Picture](https://photos.google.com/share/AF1QipO5GBWTvqcPrMLcEXbspenOxShAZLSKFksXrGiA25etHxcfowUAV12A2jyApbVwpw/photo/AF1QipNYGqkxFwIYrXMy78L95EUutmmJO8vNKjJmmQPy?key=X25UbFR6bVRvUVhhMEJQM0w0SUdMQXRIMmJBaVNn)
- Root Cause

	The on-taskgraph service and on-http service don't match, The RackHD developer has changed the name of "driveId" in kickstart file to "installDisk". So there encountered the "centos-ks" rendering error.

	The taskgraph log shows "installDisk" (https://github.com/Gary15/WorkFlow/blob/master/taskgraph.log#L167), but http log shows it tried to lookup driveId.	
- Resolved
	
	The solution is to sync up all RackHD services, either all to latest or a specified but same branch (like 1.0.x branch is relative stable for CentOS/RHEL/ESXi installation and the 1.1.x branch is also more stable than master).



##Open
###Issue list
####1. [The discovery kernel hangs at boot up on new hardware platform](https://github.com/RackHD/RackHD/issues/93 "https://github.com/RackHD/RackHD/issues/93")
- Issue description

	After deployed the RackHD server, fixed the discovery OS hang on and fixed ".ks" file rendering error. Then we can use RackHD server to finish discovery task , send IPMI command and install OS on standard physical server like Dell R610, Inspur NF5270M3.

	But when trying to use the server to discover the server which has some the latest hardware like the CPU(Skylake) and Ethernet of Intel, and it get stuck again [Stuck Photo](https://photos.google.com/share/AF1QipNBbvTYirOy1aOgiD42H-yk0HGdvoc3qSodvkhF-GNlU3O7BFjwMoQW_Y3GchDu4g/photo/AF1QipPH7DctXHWTiTBbJ93xDvd7PaLgpnnbFGrke1xJ?key=Q2JOUXk4Nk9vSTVIRHoyVU9GSGlWeXY1NXFkUW1B)
- Root Cause investigation
	
	We suspected that the microkenrel do not support the latest CPU platform(Skylake) of Intel.That the Linux Kernel version need be updated to 4.3 can support the Skylake, and we are using the Ubuntu kernel version is 3.16.0-25-generic. 
	
	So we are trying to build the microkenrel by using the latest Ubuntu kernel version.
