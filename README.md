# Linux-
jdk+tomcat+nginx+mysql

### 2019/7/24 Linux环境部署

`jdk+tomcat+nginx+mysql安装`
百度网盘：链接：https://pan.baidu.com/s/1DXaGFBZDYK25_WRgXj2pIQ 
提取码：v5tu 


### Linux 的磁盘挂载

什么叫挂载呢，我的理解是，如果你给你自己的`Linux`服务添加额外的磁盘容量后，你需要让`linux`系统去识别你添加的磁盘容量，就需要挂载操作。

我们可以通过

`fdisk -l`

来查询当前的磁盘有哪些没有被挂载的磁盘容量。这里用一张别人的图，因为我已经操作完成后，已经没有被挂载的磁盘可以显示。

![1563960218792](https://github.com/PopCandier/Linux-/blob/master/img/1563960218792.png)

一般显示在最下面的部分是`还未挂载`的磁盘。

然后我们还可以使用

`df -h`

来查看当前的磁盘使用情况，以后也可以看到我们挂载后的样子

![1563960610039](https://github.com/PopCandier/Linux-/blob/master/img/1563960610039.png)

图中`/dev/vdb2`是我挂载成功的部分。

让我们回到第一张图，未挂载的磁盘会有一个类似名字一样的路径。例如第一张图未挂载的名字叫做。`/dev/vdb`

挂载之前，我们需要`建立分区`，命令行使用

`fdisk /dev/vdb`

后面加上你需要分区的未挂载磁盘。接着你会看到一系列的命令。

![1563961008385](https://github.com/PopCandier/Linux-/blob/master/img/1563961008385.png)

敲入`m`可以获取帮助，然后我们键入`n`表示新建一个分区，接着键入`p`表示建立分区，然后输入分区的编号`1`，代表这个分区的序号，其实类似就是一个名字而已。

最后会让你设置扇区，其实就是磁盘的容量。先输入的是开始，然后是结束位置，两个数相减就是磁盘容量，如果你不愿意输入，可以直接`回车`，他会有默认值。

![1563961269831](https://github.com/PopCandier/Linux-/blob/master/img/1563961269831.png)

最后输入`w`，回车保存，新分区的建立和容量设置就完成了。

接着`初始化分区`

为什么要格式化呢？这时因为每种操作系统所设置的文件属性/权限并不相同，为了存放这些文件所需的数据，因此需要将分区进行格式化，已成为操作系统能够利用的文件系统格式。由此我们也能知道，每种操作系统的文件系统格式并不相同。比如Windows系统的文件系统格式是FAT/FAT16/NTFS，而CentOS 就是Ext2/Ext3/Ext3。接下来进行格式化操作，

格式化命令：格式分区，并指定分区系统`/dev/vdb` ,我这里是格式化成 ext4``

`mkfs.ext4  /dev/vdb`

![1563961502247](https://github.com/PopCandier/Linux-/blob/master/img/1563961502247.png)

等待挂载完成

`挂载`

这一步，我们需要将我们`格式化好的磁盘`和`linux`关联起来

我们首先现在linux上创建一个路径`/data`，然后将我们创建好分区关联起来。

`mkdir /data`

挂载

`mount /dev/vdb /data`

然后你可以通过 

`df -h`

来查看已经使用的情况。

![1563963417799](https://github.com/PopCandier/Linux-/blob/master/img/1563963417799.png)

但是这个挂载只是这次启动会有，为了下次启动也能够挂载，我们需要配置一下

`vim /etc/fstab`

![1563963518742](https://github.com/PopCandier/Linux-/blob/master/img/1563963518742.png)

键入我们之前建立好编号的磁盘 `/dev/vdb` 放入配置文件中。保存后，reboot 重启 linux 服务器

磁盘挂载完毕。

### JDK的安装



我们所有的安装都放在`/usr/xx`

回到JDK安装的环节，这里我们使用1.7版本，请在官网下载相应`linux`包。

我们可以使用 `Xftp`将下载好的`tar`放到一个目录下，输入。

`tar -zxvf  jdk-8u131-linux-x64.tar.gz`

解压完毕后，我们通过将安装包放到 `/usr`目录下

`mkdir /usr/java`

直接将已经解压好的jdk包移动到这个`/usr/java`下

`mv /自己的解压路径/jdk-8u131-linux-x64.tar.gz    /usr/java`

接着我们`配置环境变量`，输入

`vim /etc/profile`

![1563964096315](https://github.com/PopCandier/Linux-/blob/master/img/1563964096315.png)

```xml-dtd
 export JAVA_HOME=/usr/java/jdk1.8.0_131
 export JRE_HOME=${JAVA_HOME}/jre
 export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
 export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
 export PATH=$PATH:${JAVA_PATH}
```

保存退出。

接着你可以输入 `javac` 或者 `java -version` 来查看是否安装成功。

![1563964234062](https://github.com/PopCandier/Linux-/blob/master/img/1563964234062.png)

`JDK`环境配置完毕。

### Tomcat 的安装

同样，<http://tomcat.apache.org/>下载环境。

解压tomcat

`tar -zxvf /tomcat.tar.gz`

然后你可以选择到/conf/server.xml修改配置

启动tomcat 服务器，需要cd 到他的bin目录下

`sh startup.sh`

关闭服务器

`sh shutdown` 

### Nginx 安装

<https://www.runoob.com/linux/nginx-install-setup.html>

关于用`nginx`做请求的分发给tomcat服务器，由于一开始当前linux服务器只开启了81端口，我们需要用81分发给tomcat的8080端口。

所以，向nginx.conf输入以下配置。

```properties
  upstream tomcat_server{
	server 127.0.0.1:8080;
	# tomcat 的申明。
  }
 
  #limit_zone crawler $binary_remote_addr 10m;
 #下面是server虚拟主机的配置
 
 server
  {
    listen 81;#监听端口
    server_name localhost;#域名
    index index.html index.htm index.php;
    #root /usr/local/webserver/nginx/html;#站点目录
	root /usr/tomcat/apache-tomcat-7.0.78/webapps/ROOT/;
	# root 要加上这个，不然反问无法获取css与js样式。
	location / {
	# 指出tomcat的路径
		proxy_pass http://tomcat_server;
		proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
	}
```



### Mysql 安装

一些话先说在前面

* 安装zip的解压和压缩的工具
  * yum install zip
  * yun install unzip
  * 解压缩的时候， zip -r /需要压缩的文件   unzip /需要解压的文件
* 如何导入sql文件入数据库
  * 首先将sql文件放到linux服务器上，在登陆客户端后
  * 使用命令 source /你的sql文件.sql 完成导入。

具体安装步骤，先去目标网盘下载相关文件

我们下载完毕候，需要创建一个文件

我们规定，`soft`用来安放文件，`/usr/local/mysql`来安放mysql的主要要文件，`/data/mysql`用来安放相关数据文件。

```
mkdir soft
```

cd 到 soft 目录下，执行解压

```
tar -zxvf mysql-5.5.62-linux-glibc2.12-x86_64.tar.gz
```

解压完毕后，我们将解压好的文件移动到我们约定好的`/usr/local/mysql`下

```
mv mysql-5.5.62-linux-glibc2.12-x86_64 /usr/local/mysql
```

![1574084953238](./img/1574084953238.png)

接下来创建mysql用户组合用户并修改权限。

让mysql运行的时候使用一个独立的账号，如果mysql被黑了那么开始拿到的权限就是那个创建的账号而不是默认的root
大概意思是，我们分配给mysql服务一个单独的账号，我们在编译安装的时候创建一个mysql组和一个mysql用户，并把datadir和安装目录属主改为mysql，在MySQL启动的时候，单进程mysqld，该进程的属主就是mysql，这样就保证了mysql服务的独立性，即便mysql服务被黑掉，得到了mysql用户权限，也不会影响整个系统的安全

```
groupadd mysql
useradd -r -g mysql mysql
```

我们创建分组并赋予他们权限

```
mkdir -p /data/mysql
chown mysql:mysql -R /data/mysql
```

接下来，我们就要配置一下mysql的配置文件，`my.cnf`，将下面内容写入到配置文件中

```properties
[mysqld]
port=3306
user=mysql
basedir=/usr/local/mysql
datadir=/data/mysql
socket=/tmp/mysql.sock
tmpdir=/tmp
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

[mysqld_safe]
log-error=/data/mysql/mysql.err
pid-file=/data/mysql/mysql.pid

# character conif

character_set_server=utf8mb4

```

然后保存退出

接下来就是初始化数据库了，我们回到`/usr/local/mysql`目录下。找到`scripts`文件夹，并到该目录下

```
./mysql_install_db --verbose --user=mysql --defaults-file=/etc/my.cnf --datadir=/data/mysql --basedir=/usr/local/mysql
```

![1574085838354](./img/1574085838354.png)

成功后，为了可以方便启动mysql，我们将mysql的服务放到init.d目录下

```
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

启动

![1574085992547](./img1574085992547.png)

![1574086076616](./img1574086076616.png)

设置用户名密码，默认root是没有密码的，所以我们可以直接进入，cd到/usr/local/mysql/bin  目录下，可以输入下面执行，并且不输入密码，直接回车就可以进入mysql

![1574086171433](./img1574086171433.png)

开始设置用户名密码。首先我们需要像这样进入mysql，然后输入下面命名

```
SET PASSWORD = PASSWORD('123456');
FLUSH PRIVILEGES; 
```

![1574086309470](./img1574086309470.png)

我们先把密码，设置成123456，然后刷新一下，`exit`后。就发现用户名密码已经更改

> 关于navicat 无法连接到linux的mysql数据的问题

首先，确保你的linux防火墙已经关闭

```
systemctl stop firewalld
```

然后在linux上登陆你的mysql数据库，输入下面的命令

```
mysql> grant all privileges on *.* to root@"%" identified by "123456"; 
```

![1574086613571](./img/1574086613571.png)

由于安装的mysql数据库没有对外开放的，所以我们在此要给与权限去开放；密码自己设置能够记住的。这表示是给本地ip赋予了所有的权限，包括远程访问权限，%百分号表示允许任ip访问数据库。

然后再输入：

```
flush privileges;
```

这相当于是重新加载一下mysql权限，这一步必须有。最后就是退出数据库

还有一个问题就是绑定了本地ip的问题，在之前的my.cnf中，如果存在类似

```properties
bind 127.0.0.1
```

的配置，请**注释**掉他，或者改成

```properties
# bind 127.0.0.1
bind 0.0.0.0
```

这样就可以连接成功了。



#### Window 版本下安装linux

```
链接：https://pan.baidu.com/s/12f206tbJzaZmXua55ClFhA 
提取码：tqke 
```

官网太慢了，这里有百度链接

默认解压出来的时候，长这样。

![1574169156244](./img/1574169156244.png)

需要创建一个`data`，另一个是`my.ini`文件。

`my.ini`的内容是这样的

```ini
[mysqld]
##skip-grant-tables=1
port = 3306
basedir=F:\mysql\mysql-5.7.20-winx64
datadir=F:\mysql\mysql-5.7.20-winx64\data
# max connection-number
max_connections=200
character-set-server=utf8
default-storage-engine=INNODB
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
[mysql]
default-character-set=utf8
```

注意新建`my.ini`的编码格式为 `ANSI`

然后配置环境变量

![1574169426630](./img/1574169426630.png)

写入 `path`

```
%MYSQL_HOME%\bin
```

保存后，使用**管理员权限**进入cmd，然后运行

```
mysqld -install
```

进行安装。

然后启动mysql服务

```
net start mysql
```

> 服务启动后停止，某些服务未有....

* 找到mysql安装路劲的data文件夹，将data进行备份，记住一定要备份！
  （data文件夹路径可以在mysql安装目录的my.ini文件中搜索datadir找到）将data文件夹删除

* **用管理员身份打开cmd**，进入到mysql的bin目录下，输入命令初始化数据库文件（也就是data）

* 在命令行中输入：`mysqld --initialize-insecure --user=mysql`

* 之后等待一段一段时间就是从新初始化数据库，mysql数据库也就可以从新启动了

* 最后利用备份的打他文件进行数据恢复，把原先库的data里的数据库名文件夹以及ibdata1文件替换就成。

cmd输入services.msc后查看mysql服务是否启动正常。

```
UPDATE mysql.user SET authentication_string = PASSWORD('123456'), password_expired = 'N' WHERE User = 'root' AND Host = 'localhost';

flush privileges;
```

安装完毕