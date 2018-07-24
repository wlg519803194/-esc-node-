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



## 安装mysql ##
	网址：https://blog.csdn.net/Erciyns/article/details/77919604

原
[阿里云] Linux CentOS 7.4 环境下安装 MySql5.7 完整流程以及常见问题的解决方法
2017年09月10日 02:03:27
阅读数：998
若要转载，请注明出处，毕竟每个字打出来都是需要时间和精力的。
【开始之前·注意事项】已不定期测试更新，最后测试：2018年3月11日02:24:32

PS：如有安装遇到问题，或有其他疑问，可以在评论区留言，我会尽量及时回复。

作者环境，阿里云ECS服务器。Linux操作系统：CentOS 7.4     安装数据库: MySql5.7

PS：在此之前的操作：1、重新初始化磁盘，2、更换系统盘；3、密码选择的秘钥对方式。

【安装所需工具】

因为是通过Windows进行远程安装，所以需要用到远程连接工具和方便文件传输的工具，这里推荐使用如下

1、  Putty       [必需]    ：  阿里云自带浏览器版远程连接但使用不便，推荐使用Putty，一款连接Linux操作系统的软件，体积小(一般1M大小以内)，使用简单，下载即用。

官网下载：  https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

英文[0.70]-32位下载：  https://the.earth.li/~sgtatham/putty/0.70/w32/putty-0.70-installer.msi    [需要安装使用]

英文[0.70]-64位下载：  https://the.earth.li/~sgtatham/putty/0.70/w64/putty-64bit-0.70-installer.msi

中文[0.70]-32位下载：  http://gd.mycodes.net/soft/putty-0.70cn.zip    [解压后打开putty.exe即可使用]

中文[0.70]-64位下载：  http://gd.mycodes.net/soft/putty-x64-0.70cn.zip

2、  WinSCP    [按需]  ：  https://winscp.net/eng/docs/lang:chs   PS：MySql我们用命令从官方下载，用Putty足已。

（若想自行上传可以安装WinSCP）

【开始安装】这里开始讲解安装流程/步骤，以及安装中遇到的问题

第一步： 安装前环境确认

1、打开Putty 连接 阿里ESC服务器  ：Linux  CentOS 7.4

主机名称输入你的服务器公网ip地址，如不知道，可以在控制台查看。

例如：60.211.103.121     端口号输入：22     连接类型：SSH

输入完毕后点击连接就可以了。

[问题-1]：如果连接之后发现连接不上，请查看阿里云安全组配置是否开放了22端口以及外网访问权限，如没有请在安全组配置添加即可，无问题则忽略。

如下图：



原因：你的服务器防火墙阻止了你的远程连接请求。

解决办法：设置安全组配置，开启22端口并允许外网访问。若0.0.0.0/0 改为你自己的IP，则只允许你自己访问，相对安全。

云服务器ECS>本实例安全组>配置规则>添加安全组策略

如图：



[问题-2]：配置完毕后再次进行Putty连接，若仍有问题，请关闭你的本机防火墙(家用电脑)，并重启阿里云服务器。

若仍不行，请留言给我，或百度寻找原因。

如果弹出安全警告，点击是即可。原因：你的服务器连接秘钥已更换或失效，需要更新，点击是则自动更新，无问题则忽略。

也可能受其他原因影响，所以可以尝试下面的解决方案，请看下面问题解释。

[问题-3]：再次连接出现如下错误，若没有则忽略

如图：






Fatal ： 严重到必须手动解决的错误。

Disconnected: No supported authentication methods available (server sent: publickey,gssapi-keyex,gssapi-with-mic)

原因：


是因为配置的时候选择了秘钥对认证，导致密码登录默认关闭了。开启即可。

解决办法：通过阿里云控制台[远程连接] 登录 Linux 系统。

进入之后直接输入用户名，密码 (Linux默认输入时看不见，只管输入即可)。

输入：root

输入：你的Linux登陆密码 （）

输入后回车键 (Enter) 登入系统

如果登录提示：授权验证失败。如果实例为非I/O优化实例，修改VNC密码后, 只有重启实例后才能生效。请修改远程连接密码后重启实例。

如图：登陆成功。



输入后进入命令模式

即：[root@主机名 ~]#

开始解决：

在命令模式下

	输入：vim /etc/ssh/sshd_config       [管理终端右上角可以复制命令输入]

输入后回车确认进入，如图：

按方向键下键，翻到最底下。



可以看到：PasswordAuthentication  no 密码认证登录被关闭：no (开启为：yes)

我们打开它为：yes

[问题]：如果不懂vi命令看下面讲解，懂则改为yes保存退出即可(保存退出后，需要重启Linux服务器)

继续解决：

按键盘i键：i(进入- - INSERT - -   插入模式)

如图：



PS：通过控制往下左右将光标移动至  no 后面 ，删除  no  ,改为  yes

改为yes之后，不要进行其他操作，继续进行下面步骤。

1、按键盘左上角ESC键：ESC

- - INSERT - -  白字会消失

2、按住shift+;     输入     ：(即：输入一个英文冒号，进入命令模式)

3、冒号后面继续输入     wq       (：wq      保存并退出)

4、回车确认

如图：



完成以上步骤，恭喜你修改成功，接下来重启服务器让配置生效。

（如果不想修改，但已经进入插入模式，按Ese进入命令模式，输入  q!   不保存强制退出）

输入：reboot (命令模式下，即 # 后光标闪烁，reboot  重启Linux服务器   PS：你也可以通过阿里云重启)

2、如果遇到问题，经过上述步骤操作后，Putty可以直接连接，那么进入第二步。如果仍有问题就问度娘吧。

仍有问题，请截图保存。并百度了解原因，度娘是万能的。或者直接留言。

PS:安装了十几次 总结出来的，大致的问题都在这里，或许有些其他问题我遗忘了可能没有列出来请留言。

如果没有问题，可以正常Putty连接，恭喜！我们进入第二步，安装MySql5.7

第二步： 开始安装MySql5.7（注意，如果修改了上述配置，请务必重启Linux系统，命令模式：reboot 或 控制台重启）

1、Putty登陆Linux，自动进入命令模式。

2、CentOS7.x默认安装mariadb数据库(据说7.x版本，其他版本未知)不想删除跳过即可。

专治强迫症，卸载mariadb，并删除之。

命令模式下

	输入： rpm -qa|grep mariadb

	显示：mariadb-libs-5.5.56-2.el7.x86_64

	输入： yum remove mariadb-libs.x86_64
一长串过程 最后一行显示：  Complete!

如果第二个命令提示Is this ok [y/N]: 是否确认，输入：y回车即可

3、下载MySql5.7

	MySql官网：https://dev.mysql.com/downloads/repo/yum/

方法一：官网自行下载并上传至Linux服务器，不推荐。

方法二：Linux命令直接下载，推荐！

这里我们用方法二

命令模式下

        输入：wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

如图：





4、安装MySql

	方法一：  yum localinstall mysql57-community-release-el7-11.noarch.rpm

安装过程有其他问题用方法二

	方法二：    rpm --import /etc/pki/rpm-gpg/RPM*

                  rpm -ivh mysql57-community-release-el7-11.noarch.rpm

                  yum install -y mysql-server

（根据网速快慢，耗时不同，一般2-10分钟以内）

>注意：安装中可能需要确认，会提示输入Y/N之类。直接输入： y回车即可，可能会提示2-3次

[不推荐yum -y 方式自动确认，初次安装需要了解基本的过程]。

	>若安装失败请度娘或，yum --nogpgcheck install 软件包名称 再试。

5、启动MySql

	输入：systemctl start mysqld   或  service mysqld start 

PS：检查启动状态

	输入：service mysqld status（如果有绿色字体提示：active (running) 表示已经启动OK了)

6、设置开机启动

	输入：systemctl enable mysqld 

	输入：systemctl daemon-reload


7、修改MySqlroot用户的密码。

（不修改登陆会报错，这里一定要求改，并且密码不要太简单，最好是  Acnju@154asda  这种）

PS：修改密码可以百度参考各版本区别。
PS：先查看MySql57临时密码(安装后默认有一个)

	输入：grep 'temporary password' /var/log/mysqld.log;

如图：红圈：后面就是初始密码，用这个登陆即可。



#命令模式下，登陆MySql命令模式   mysql>  

	输入：mysql -uroot -p

	输入：                  （提前复制好，右键粘贴即可）



修改root密码

	输入：set password for 'root'@'localhost' =password('123456');



由于密码太简单会出现其他问题，所以修改密码限制

	输入： set global validate_password_policy=0;

	输入： set global validate_password_length=1;

再次修改密码

	输入：set password for 'root'@'localhost' =password('123456');

8、授权远程登录     (123456为密码)

	输入：grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;

	输入：flush privileges;

搞定收工，登录看看吧！
	