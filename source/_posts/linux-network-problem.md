---
title: Linux Network Problem
date: 2019-08-03 10:45:19
categories:
- Software Development
- Operating System
- Linux
tags:
- Linux
---
Linux系统出现网络问题的解决方法记录。
<!--more-->

### Ubuntu  
可以对网络服务进行重启。
+ 停止： `sudo service network-manager stop`
+ 备份（可选）：`sudo cp /var/lib/NetworkManager/NetworkManger.state /var/lib/NetworkManager/NetworkManager.state.bak`
+ 删除当前状态：`sudo rm /var/lib/NetworkManager/NetworkManger.state`
+ 重启：`suao service network-manager start`


### CentOs

