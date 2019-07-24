# Linux-
jdk+tomcat+nginx+mysql

### 2019/7/24 Linux环境部署

`jdk+tomcat+nginx+mysql安装`



### Linux 的磁盘挂载

什么叫挂载呢，我的理解是，如果你给你自己的`Linux`服务添加额外的磁盘容量后，你需要让`linux`系统去识别你添加的磁盘容量，就需要挂载操作。

我们可以通过

`fdisk -l`

来查询当前的磁盘有哪些没有被挂载的磁盘容量。这里用一张别人的图，因为我已经操作完成后，已经没有被挂载的磁盘可以显示。

![1563960218792](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563960218792.png)

一般显示在最下面的部分是`还未挂载`的磁盘。

然后我们还可以使用

`df -h`

来查看当前的磁盘使用情况，以后也可以看到我们挂载后的样子

![1563960610039](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563960610039.png)

图中`/dev/vdb2`是我挂载成功的部分。

让我们回到第一张图，未挂载的磁盘会有一个类似名字一样的路径。例如第一张图未挂载的名字叫做。`/dev/vdb`

挂载之前，我们需要`建立分区`，命令行使用

`fdisk /dev/vdb`

后面加上你需要分区的未挂载磁盘。接着你会看到一系列的命令。

![1563961008385](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563961008385.png)

敲入`m`可以获取帮助，然后我们键入`n`表示新建一个分区，接着键入`p`表示建立分区，然后输入分区的编号`1`，代表这个分区的序号，其实类似就是一个名字而已。

最后会让你设置扇区，其实就是磁盘的容量。先输入的是开始，然后是结束位置，两个数相减就是磁盘容量，如果你不愿意输入，可以直接`回车`，他会有默认值。

![1563961269831](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563961269831.png)

最后输入`w`，回车保存，新分区的建立和容量设置就完成了。

接着`初始化分区`

为什么要格式化呢？这时因为每种操作系统所设置的文件属性/权限并不相同，为了存放这些文件所需的数据，因此需要将分区进行格式化，已成为操作系统能够利用的文件系统格式。由此我们也能知道，每种操作系统的文件系统格式并不相同。比如Windows系统的文件系统格式是FAT/FAT16/NTFS，而CentOS 就是Ext2/Ext3/Ext3。接下来进行格式化操作，

格式化命令：格式分区，并指定分区系统`/dev/vdb` ,我这里是格式化成 ext4``

`mkfs.ext4  /dev/vdb`

![1563961502247](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563961502247.png)

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

![1563963417799](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563963417799.png)

但是这个挂载只是这次启动会有，为了下次启动也能够挂载，我们需要配置一下

`vim /etc/fstab`

![1563963518742](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563963518742.png)

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

![1563964096315](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563964096315.png)

```xml-dtd
 export JAVA_HOME=/usr/java/jdk1.8.0_131
 export JRE_HOME=${JAVA_HOME}/jre
 export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
 export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
 export PATH=$PATH:${JAVA_PATH}
```

保存退出。

接着你可以输入 `javac` 或者 `java -version` 来查看是否安装成功。

![1563964234062](C:\Users\范凌轩\AppData\Roaming\Typora\typora-user-images\1563964234062.png)

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

<https://www.cnblogs.com/duanrantao/p/8988116.html>

具体安装步骤。








