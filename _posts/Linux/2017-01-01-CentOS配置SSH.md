---
title: CentOS配置SSH
toc: false
permalink: /posts/linux/centos-config-ssh.html
categories: Linux
date: 2017-01-01
---

## 修改SSH端口号

1. 编辑 /etc/ssh/sshd_config；
2. 取消 #Port 前面的注释符号，将其指改为新的端口号
3. 重启 service sshd restart
4. 登陆 ssh -p *port* root@xx.xx.xx.xx

## 设置SSH不自动断开

1. 编辑 /etc/ssh/sshd_config；
2. 去掉 #TCPKeepAlive yes 前面的注释符号，保持其值为yes；
3. 去掉 #ClientAliveInterval 0 前面的注释符号，将其值改为60；
4. 去掉 #ClientAliveCountMax 3 前面的注释符号，保持其值为3；
5. 重启 service sshd restart

TCPKeepAlive参数表示要保持TCP连接。ClientAliveInterval参数指定了服务器端向客户端请求消息的时间间隔，默认是0，表示不发送，改为60表示每60秒发送一次，然后等待客户端响应。ClientAliveCountMax参数表示服务端发出请求后客户端可以没有响应的最大次数，当没有响应的次数达到ClientAliveCountMax就断开连接，这里使用默认值3即可。经过这样的配置，客户端在180秒内没有响应才会断开连接，正常情况下，客户端不会不响应，这样就能够保持长连接。
