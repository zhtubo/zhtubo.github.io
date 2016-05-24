---
layout: post
title:  "centos安裝Mysql"
date:   2015-09-02
categories: database
---

###先行檢查有無安裝mysql###
~~~shell
yum list installed | grep mysql
~~~

###如果有可刪除也可直接安裝新版本###
~~~shell 
yum remove mysql*
~~~

###接下來添加mysql5.7的源###

###1:直接命令行的方式添加源###
###centos6.5###
~~~shell
yum install http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
~~~
###centos7###
~~~shell
yum install https://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
~~~
###激活5.7的源###
~~~shell
yum-config-manager --disable mysql56-community
yum-config-manager --enable mysql57-community-dmr
~~~
###如果系统里没有yum-config-manager命令，执行如下命令，进行安装。###
~~~shell
yum install yum-utils
~~~

###2:在/etc/yum.repo.d中添加mysql5.7的源，如下所示###
~~~shell
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
~~~


###yum安装MySQL5.7及相关的工具(按需選擇安裝###
~~~shell
yum install mysql mysql-devel mysql-server mysql-utilities
~~~
