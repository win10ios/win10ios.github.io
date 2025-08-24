---
title: 在CentOS 7.6上编译安装Python 3.8完整指南
date: 2025-08-24 18:47:32
tags: 
- web
categories: 
- web

---

# CentOS 7.6 源码编译安装Python 3.8教程

本文详细介绍在CentOS 7系统上编译安装Python 3.8的完整流程。
请提前执行centos7换源命令确保yum工具正常
`sed -i -r -e 's|^mirrorlist=|#mirrorlist=|g' -e 's|^#?baseurl=http://mirror.centos.org/centos|baseurl=https://vault.centos.org/centos|g' -e 's|^#?baseurl=http://mirror.centos.org/altarch|baseurl=https://vault.centos.org/altarch|g' -e 's|^#?baseurl=http://download.fedoraproject.org|baseurl=http://download.fedoraproject.org|g' /etc/yum.repos.d/CentOS-*.repo`
`yum clean all && yum makecache`
## 1.安装编译依赖

`yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make`

## 2.安装额外依赖

`yum install libffi-devel -y`

## 3.下载Python源码

`wget https://www.python.org/ftp/python/3.8.12/Python-3.8.12.tgz`

## 4. 解压源码包

`tar -zxvf Python-3.8.12.tgz`

## 5. 进入源码目录

`cd Python-3.8.12`

## 6. 配置编译选项

`./configure`

## 7. 编译并安装

`make&&make install`

## 8. 备份系统Python

`mv /usr/bin/python /usr/bin/python.bak`

## 9 创建软链接

`ln -s /usr/local/bin/python3 /usr/bin/python`

`ln -s /usr/local/bin/pip3 /usr/bin/pip`
