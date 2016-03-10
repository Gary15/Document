#Use RackHD to Install CentOS On Your Server
###1. Unpacking the CentOS ISO file
1. Download the ISO file to `~/RackHD/example`

		$ wget http://mirrors.ustc.edu.cn/centos/7.2.1511/isos/x86_64/CentOS-7-x86_64-DVD-1511.iso
2. Unpack the ISO file in Vagrant

		$ vagrant ssh
		$ sudo mkdir /var/mirrors
		$ sudo python ~/src/on-http/data/templates/setup_iso.py /vagrant/CentOS-7-x86_64*.iso /var/mirrors --link=/home/vagrant/src
	**Note:** The VM has mounted shared folders:/vagrant => ~/RackHD/example

###2. Install a default workflow for CLS Server and a SKUs definition
Here is the example for CLS server. And how it work, please refer to http://rackhd.readthedocs.org/en/latest/how_it_works.html

1. Write a default workflow for CLS Server install CentOS by using IPMI OBM

		$ vi ~/RackHD/example/samples/CLS_install_centos.json

		{
		    "friendlyName": "CLS Server Install CentOS",
		    "injectableName": "Graph.CLS.InstallCentOS",
		    "tasks": [
		        {
		            "label": "create-ipmi-obm-settings",
		            "taskDefinition": {
		                "friendlyName": "Create IPMI OBM settings",
		                "injectableName": "Task.Obm.Ipmi.CreateSettings",
		                "implementsTask": "Task.Base.Obm.Ipmi.CreateSettings",
		                "options": {
		                    "impichanel": "lan",
		                    "user": "<user>",
		                    "password": "<passwd>"
		                },
		                "properties": {}
		            }
		        },
		        {
		            "label": "Install CentOS",
		            "taskDefinition": {
		                "friendlyName": "Install CentOS",
		                "injectableName": "Task.Os.Install.CentOS",
		                "implementsTask": "Task.Base.Os.Install",
		                "options": {
		                    "osType": "linux",
		                    "profile": "install-centos.ipxe",
		                    "hostname": "localhost",
		                    "comport": "ttyS0",
		                    "domain": "rackhd.demo.com",
		                    "completionUri": "renasar-ansible.pub",
		                    "version": "7.0",
		                    "repo": "{{api.server}}/Centos/{{options.version}}",
		                    "rootPassword": "root",
		                    "installDisk": "sda"
		                },
		                "properties": {
		                    "os": {
		                        "linux": {
		                            "distribution": "centos"
		                        }
		                    }
		                }
		            }
		        }
		    ]
		}
 		
		$ curl -H "Content-Type: application/json" -X PUT --data @samples/CLS_install_centos.json http://localhost:9090/api/1.1/workflows
2. To enable that workflow, we also need to include a SKU definition that includes the option of another workflow to run once the SKU has been identified. This takes advantage of the `Graph.SKU.Discovery` workflow, which will attempt to identify a SKU and run another workflow if specified.

		$ vi  ~/RackHD/example/samples/CLS_sku.json

		{
		    "name": "IPMI OBM settings for CLS nodes",
		    "discoveryGraphName": "Graph.CLS.InstallCentOS",
		    "discoveryGraphOptions": {},
		    "rules": [
		        {
		            "path": "dmi.System Information.Manufacturer",
		            "equals": "CLS"
		        }
		    ]
		}
		
		$ curl -H "Content-Type: application/json" -X POST --data @samples/CLS_sku.json http://localhost:9090/api/1.1/skus
3. View the SKU definitions:

	$ curl http://localhost:9090/api/1.1/skus | python -m json.tool

		[
			{
		        "createdAt": "2016-03-02T02:27:18.623Z",
		        "discoveryGraphName": "Graph.CLS.InstallCentOS",
		        "discoveryGraphOptions": {},
		        "id": "56d64f862dda5258076fd9ff",
		        "name": "IPMI OBM settings for CLS nodes",
		        "rules": [
		            {
		                "equals": "CLS",
		                "path": "dmi.System Information.Manufacturer"
		            }
		        ],
		        "updatedAt": "2016-03-02T02:27:18.623Z"
		    }
		]

###3.Startup your Machine through PXE 

**Note:** In the process of the installation, You may probably meet the issue that you can not get the "centos.ks" from HTTP server. https://github.com/RackHD/RackHD/issues/68. Or maybe not. It decide your code version. And the log file should be in /tmp/* . If there don't have it, please check this PR https://github.com/RackHD/RackHD/pull/66/files to make sure you have these code in your server. 
