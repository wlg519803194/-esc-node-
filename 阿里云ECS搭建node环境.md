## 使用二进制文件安装 ##
该部署过程使用的安装包是已编译好的二进制文件，解压之后，在bin文件夹中就已存在node和npm，无需手工编译。

#### 安装步骤： ####

wget命令下载Node.js安装包。该安装包是编译好的文件，解压之后，在bin文件夹中就已存在node和npm，无需重复编译。

	wget https://nodejs.org/dist/v6.9.5/node-v6.9.5-linux-x64.tar.xz
解压文件。

	tar xvf node-v6.9.5-linux-x64.tar.xz
创建软链接，使node和npm命令全局有效。通过创建软链接的方法，使得在任意目录下都可以直接使用node和npm命令：

	ln -s /root/node-v6.9.5-linux-x64/bin/node /usr/local/bin/node
	ln -s /root/node-v6.9.5-linux-x64/bin/npm /usr/local/bin/npm
查看node、npm版本。

	node -v
	npm -v
至此，Node.js环境已安装完毕。软件默认安装在/root/node-v6.9.5-linux-x64/目录下。如果需要将该软件安装到其他目录（如：/opt/node/）下，请进行如下操作：

	mkdir -p /opt/node/
	mv /root/node-v6.9.5-linux-x64/* /opt/node/
	rm -f /usr/local/bin/node
	rm -f /usr/local/bin/npm
	ln -s /opt/node/bin/node /usr/local/bin/node
	ln -s /opt/node/bin/npm /usr/local/bin/npm
使用NVM安装多版本
NVM（Node version manager）是Node.js的版本管理软件，使用户可以轻松在Node.js各个版本间进行切换。适用于长期做 node 开发的人员或有快速更新node版本、快速切换node版本这一需求的用户。

安装步骤：

直接使用git将源码克隆到本地的~/.nvm目录下，并检查最新版本。

	yum install git
	git clone https://github.com/cnpm/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
激活NVM。

	echo ". ~/.nvm/nvm.sh" >> /etc/profile
	source /etc/profile
列出Node.js的所有版本。

	nvm list-remote
安装多个Node.js版本。

	nvm install v6.9.5
	nvm install v7.4.0
#### 运行 nvm ls 查看已安装Node.js版本，当前使用的版本为v6.9.5。返回结果如下所示。 ####

	[root@iZXXXXZ .nvm]# nvm ls
	      v6.9.5
	->       v7.4.0
	      system
	stable -> 7.4 (-> v7.4.0) (default)
	unstable -> 6.9 (-> v6.9.5) (default)
	运行 nvm use v7.4.0 切换Node.js版本至v7.4.0。返回结果如下所示。
	
	[root@iZXXXXZ .nvm]# nvm use v7.4.0
	Now using node v7.4.0
NVM的更多操作请参考帮助文档：

	nvm help
步骤3：部署测试项目
新建项目文件example.js。
	cd ~
	touch example.js
使用vim编辑器打开项目文件example.js。

	yum install vim
	vim example.js
输入 i，进入编辑模式，将以下项目文件内容粘贴到文件中。使用Esc按钮，退出编辑模式，输入:wq，回车，保存文件内容并退出。
项目文件内容：

	const http = require('http');
	const hostname = '0.0.0.0';
	const port = 3000;
	const server = http.createServer((req, res) => {
	res.statusCode = 200;
	res.setHeader('Content-Type', 'text/plain');
	res.end('Hello World\n');
	});
	server.listen(port, hostname, () => {
	console.log(`Server running at http://${hostname}:${port}/`);
	});
注意：
项目文件内容中的3000为端口号，可以自行定义。

运行项目。

	node ~/example.js
注意：
可以使用命令 node ~/example.js & 将项目置于后台运行。

使用命令查看项目端口是否存在。

	netstat -tpln
登录ECS管理控制台，并在安全组中 添加安全组规则 放行端口（如本示例中为TCP 3000端口）。

（可选）如果您的实例中开启了防火墙，必须添加端口的入站规则（如本示例中为TCP 3000端口）。

在本地机器的浏览器中输入 http://实例公网IP地址:端口号 访问项目。
	