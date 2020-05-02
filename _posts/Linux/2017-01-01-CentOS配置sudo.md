---
title: CentOS配置sudo
toc: false
permalink: /posts/linux/centos-config-sudo.html
categories: Linux
date: 2017-01-01
---

用 sudo 时提示 "xxx is not in the sudoers file. This incident will be reported."

1. 登陆root或超级用户模式（即使用命令 "su -"后的模式）
2. chmod u+w /etc/sudoers 为配置文件增加写权限。
3. 编辑 /etc/sudoers 文件，找到这一行："root ALL=(ALL) ALL"，在起下面添加"xxx ALL=(ALL) ALL"，xxx是你的用户名；
4. chmod u-w /etc/sudoers 撤销配置文件的写权限。
