# Linux

### 目录结构

pwd：当前目录

~ 代表用户目录

/ 代表系统根目录



### 常用命令

提示信息：--help

列出文件列表：ls 或 ll

目录切换：cd

返回上一级：cd ..

返回上一次所在目录：cd -

创建文件夹：mkdir

删除文件夹：rmdir

创建多级目录：mkdir -p aaa/bbb

查看文件内容：cat、more、less、head、tail

动态查看文件：tail -f xxx.log

复制文件：cp

剪切文件：mv

删除文件：rm

删除目录和文件：rm -rf

自杀：rm -rf /*

打包文件：tar -cvf xxx.tar tomcat7/

压缩文件：tar -zcvf xxx.tar tomcat7/

解压文件：tar -zxvf xxx.tar

解压到指定目录：tar -zxvf xxx.tar -C ./aaa

查找文件：find / -name xxx.tar

文件内容搜索：grep Address ./logs/catalina.log --color -A1 -B1（-A1显示后一行 -B1前一行）

创建文件：touch

清屏幕：clear 或 ctrl+L

重定向（覆盖）：ifconfig > aaa.txt

重定向（追加）：ifconfig  >> aaa.txt

管道：| （将一个命令的输出作为另一个命令的输入）

查看进程：ps -ef | grep tomcat

强制删除进程：kill -9 进程ID



### 权限

##### 权限表示

​	drwxr-xr-x   可分为   d rwx r-x r-x

​	1.文件类型

​		① - 文件

​		② d 文件夹

​		③ l 连接（快捷方式）

​	2.当前用户权限

​		① r 读		4

​		② w 写	   2

​		③ x 执行	1

​	3.当前组内权限

​	4.其他组权限



##### 修改权限

​	方式1：chmod u=rwx,g=r,o=r aa.txt

​	方式2：chmod 755 aa.txt



### 常见网络管理操作

##### 主机名配置

​	查看主机名：hostname

​	临时修改主机名：hostname KuTatsuko

​	永久修改：vim /etc/sysconfig/network（修改 hostname）



##### IP 地址配置

​	重启网络服务：service network restart

​	临时修改 IP：ifconfig eth0 192.168.59.129

​	永久修改 IP：vim /etc/sysconfig/network-scripts/ifcfg-eth0

​		修改成：

​			ONBOOT=yes						#启动时启用

​			BOOTPROTO=static			  #静态地址

​			IPADDR=192.168.59.129	  #IP 地址

​			NETMASK=255.255.255.0	 #子网掩码

​			NETWORK=192.168.59.0	   #网络地址

​			BROADCAST=192.168.0.255  #广播地址

​			NBOOT=yes							  #系统启动时是否设置此网络接口

域名映射修改：vim /etc/hosts

关闭防火墙：service iptables stop



### 软件安装

#####  常见安装方式

​	1.二进制包：已针对平台编译打包发布，只需解压修改配置

​	2.RPM 包：已按 redhat 包管理工具规范打包发布，需要相应 RPM 包，不能自动安装依赖

​	3.Yum 在线安装：已按 RPM 打包，能自动安装依赖

​	4.源码编译安装：以源码方式发布，需要相应开发工具编译打包部署

​	5.环境变量配置文件：vim /etc/profile



##### lrzsz

​	安装：yum install lrzsz

​	上传：rz

​	下载：sz

 

##### MySQL 安装

[MySQL 8.0安装以及初始化错误解决方法_Pioneer4的博客-CSDN博客](https://blog.csdn.net/weixin_40780777/article/details/100553505)

[mysql报错22_Cent OS7强制安装Mysql8.0.22的错误解决办法_weixin_39557373的博客-CSDN博客](https://blog.csdn.net/weixin_39557373/article/details/113252355)



##### Redis 安装

​	1.安装gcc-c++：yum install gcc-c++

​	2.下载 Redis：wget https://download.redis.io/releases/redis-6.2.5.tar.gz

​	3.解压：tar -xzvf redis-6.2.5.tar.gz

​	4.编译：切换到安装目录，执行 make

​	5.安装：make PREFIX=/usr/local/software/redis-6.2.5 install

​	6.复制配置文件：cp ~/下载/redis-6.2.5/redis.conf /usr/local/software/redis-6.2.5/bin/、

​	6.运行：./redis-server redis.conf



### 项目部署

1.导入数据库

2.修改数据库连接语句：url=jdbc:mysql:///travel?characterEncoding=utf-8

3.修改 pom.xml

```xml
<finalName>travel</finalName>
<!--jdk编译插件-->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.0</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <encoding>utf-8</encoding>
    </configuration>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.3.1</version>
</plugin>
```

4.清理缓存并打包：maven 的 clean 和 package

5.复制到 Tomcat 的 webapps 目录

6.可查看 Tomcat 运行日志：Tomcat 的 logs 目录 tail -f catalina.out

7.访问网站：http://192.168.59.129:8080/travel/



### EditPlus 远程编辑文件

File -> FTP -> FTP Upload -> Settings -> Add -> 输入配置 -> Advanced Options -> Encryption -> sftp

注：乱码则 Advanced Options 勾选 Use UTF-8 file names



# Nginx

概念：一款高性能 http 服务器/方向代理服务器及电子邮件（IMAP/POP3）代理服务器，能支持 5 万并发链接，可对 Tomcat 进行负载均衡



### 应用场景

​	1、http 服务器。Nginx 是一个 http 服务可以独立提供 http 服务。可以做网页静态服务器。

​	2、虚拟主机。可以实现在一台服务器虚拟出多个网站。例如个人网站使用的虚拟主机。

​	3、反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用 nginx 做反向代理。并且多台服务器可以平均分担负载，不会因为某台服务器负载高宕机而某台服务器闲置的情况。  



### 安装

##### 环境准备

​	1.一般需要重新准备一台服务器进行安装（单独跑 Nginx）

​	2.安装 gcc 环境：yum install gcc-c++

​	3.安装第三方开发包：

​		PCRE：yum install -y pcre pcre-devel

​		zlib：yum install -y zlib zlib-devel

​		OpenSSL：yum install -y openssl openssl-devel



##### 安装 Nginx

​	1.下载：wget http://nginx.org/download/nginx-1.20.1.tar.gz

​	2.解压：tar -zxvf nginx-1.20.1.tar.gz

​	3.进入 ngnix 目录，使用 configure 命令构建 makeFile 文件

```tex
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
```

​	4.执行 make 编译和安装 make PREFIX=/usr/local/software/nginx-1.20.1 install



##### 启动与访问

​	注：启动前，上边将临时文件目录指定为 /var/temp/nginx/client， 需要在 /var 下创建此目录

​	1.mkdir /var/temp/nginx/client -p

​	2.进入 Nginx 下的 sbin 目录：cd /usr/local/nginx/sbin

​	3.启动：./nginx

​	4.启动后查看进程：ps aux|grep nginx

​	5.访问（默认端口号 80）：http://192.168.59.129/

​	6.关闭：./nginx -s stop（强行终止）或 ./nginx -s quit（正常退出）

​	7.重启：./nginx -s reload（用于刷新配置文件）



### 静态网站部署

​	1.将/资料/静态页面/index目录下的所有内容上传到服务器的/usr/local/nginx/ 下

​	2.修改 nginx/conf/nginx.conf 配置文件（root index）

```
server {
	listen       80;	#默认端口
	server_name  localhost;	#域名或IP

	location / {
		root   index;	#默认访问资源的目录
		index  index.html index.htm;	#默认访问资源名称
	}

	error_page   500 502 503 504  /50x.html;	#错误页面
	location = /50x.html {
		root   html;
	}
}
```

​	3.重启服务器：./nginx -s reload



### 配置虚拟主机

##### 端口绑定

​	可配置多个 server

    server {
        listen       80;	#默认端口
        server_name  localhost;	#域名或IP
    
        location / {
            root   index;	#默认访问资源的目录
            index  index.html index.htm;	#默认访问资源名称
        }
    
        error_page   500 502 503 504  /50x.html;	#错误页面
        location = /50x.html {
            root   html;
        }
    }
    
    server {
        listen       81;	#默认端口
        server_name  localhost;	#域名或IP
    
        location / {
            root   regist;	#默认访问资源的目录
            index  regist.html;	#默认访问资源名称
        }
    
        error_page   500 502 503 504  /50x.html;	#错误页面
        location = /50x.html {
            root   html;
        }
    }



##### 域名绑定

​	1.本地测试可以修改 hosts 文件（C:\Windows\System32\drivers\etc\hosts）

	192.168.59.129 www.ktsk.com
	192.168.59.129 regist.ktsk.com

​	

​	2.修改 nginx.xml

    server {
        listen       80;	#默认端口
        server_name  www.ktsk.com;	#域名或IP
    
        location / {
            root   index;	#默认访问资源的目录
            index  index.html index.htm;	#默认访问资源名称
        }
    
        error_page   500 502 503 504  /50x.html;	#错误页面
        location = /50x.html {
            root   html;
        }
    }
    
    server {
        listen       80;	#默认端口
        server_name  regist.ktsk.com;	#域名或IP
    
        location / {
            root   regist;	#默认访问资源的目录
            index  regist.html;	#默认访问资源名称
        }
    
        error_page   500 502 503 504  /50x.html;	#错误页面
        location = /50x.html {
            root   html;
        }
    }



### 反向代理与负载均衡

##### 概念

​	1.正向代理：如教室，学生机需要访问外网，则老师部署代理服务器，学生机配置代理服务器地址访问代理服务器，代理服务器访问外网资源，最后返回到学生机（代理客户端）

​	2.反向代理：用户直接访问 Tomcat 服务器会出现负载过高问题，则让所有用户请求访问代理服务器，代理服务器分发请求到多个 Tomcat 服务器上。或在 Tomcat 和代理服务器之间加上防火墙或将 Tomcat 改成内网或局域网，让外网只能访问代理服务器，增加安全性（代理服务端）



##### 反向代理配置

​	配置 upstream tomcat-travel 和 server 下 location 的 proxy_pass http://tomcat-travel;

    upstream tomcat-travel {
    	server 192.168.59.129:8080;
    }
    
    server {
        listen       80;	#默认端口
        server_name  www.ktsk.com;	#域名或IP
    
        location / {
        	proxy_pass http://tomcat-travel;
            index  index.html index.htm;	#默认访问资源名称
        }
    
        error_page   500 502 503 504  /50x.html;	#错误页面
        location = /50x.html {
            root   html;
        }
    }



##### 负载均衡配置

​	1.修改多个 Tomcat 的 conf/server.xml 中的端口号

​	2.分别启动多个 Tomcat

​	3.修改 nginx.conf（weight 为访问权重，默认为 1）

    upstream tomcat-travel {
    	server 192.168.59.129:8080 weight=2;
    	server 192.168.59.129:8081;
    	server 192.168.59.129:8082;
    }

