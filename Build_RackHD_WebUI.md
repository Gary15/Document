#Build RackHD on-web-ui
###Build Running environment
1. Install Nodejs

	More information please refer to http://www.runoob.com/nodejs/nodejs-install-setup.html. Here is my step:

		$ wget https://nodejs.org/dist/v4.2.2/node-v4.2.2-linux-x64.tar.gz
		$ tar -xzvf node-v4.2.2-linux-x64.tar.gz
		$ mv node-v4.2.2-linux-x64/ /usr/local/node-v4/
		$ vi /etc/profile  #Add these lines in the bottom
			NODE_HOME=/usr/local/node-v4/bin
			export PATH=$NODE_HOME:$PATH
		$ source /etc/profile
	**Test**: You can input the `node -v` or `npm -v` to output the version to check if it works.
2. Change npmjs.org to npm.taobao.org to make it build faster

	More infomation please refer to http://npm.taobao.org/

		$ npm install -g cnpm --registry=https://registry.npm.taobao.org

3. Download the RackHD web-ui code from Github

		$ git clone https://github.com/RackHD/on-web-ui.git

###Build web ui

Build the web UI by type following command

	$ cd on-web-ui
	$ cnpm run uninstall
	$ cnpm install
	$ cnpm run install
	$ cnpm start


By default there will be two servers running. One on port 5000 which will be proxied by another on port 3000.

http://localhost:3000 is the MonoRail Web UI.


####Note: You also can download [latest build](https://github.com/RackHD/on-web-ui/archive/gh-pages.zip) offer by RackHD, then extra it to HTTP Server.You can refer to https://github.com/RackHD/on-web-ui.
 
	
