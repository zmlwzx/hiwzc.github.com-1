---
title: CentOS安装MySQL
toc: false
permalink: /posts/linux/centos-install-mysql.html
categories: Linux
date: 2017-01-01
---

1. 下载Yum源：<http://dev.mysql.com/downloads/repo/yum/>
2. 安装Yum源：rpm -ivh mysql80-community-release-el7-1.noarch.rpm
3. 查看默认安装版本：`yum repolist all | grep mysql`
4. 修改安装版本：如果要安装非默认版本，则编辑 /etc/yum.repos.d/mysql-community.repo，将要安装的版本的enable改为1，将其他版本改为0
5. 安装：yum install mysql-community-server
6. 启动：service mysqld start
