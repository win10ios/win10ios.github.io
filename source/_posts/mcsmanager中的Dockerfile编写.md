---
title: mcsmanager中的Dockerfile编写
date: 2025-08-14 23:57:39
tags: 
- web
categories: 
- web

---

基于 Debian 11，挂载 /data的一个示例

`FROM debian:11

ENV TZ=Asia/Shanghai

# 使用清华源加速 apt 更新

RUN sed -i 's|http://deb.debian.org|http://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list && \
    sed -i 's|http://security.debian.org|http://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list

# 安装必要工具并设置时区

RUN apt update && \
    DEBIAN_FRONTEND=noninteractive apt install -y \
    tzdata \
    libcurl4 \
    curl \
    wget \
    unzip \
    bash \
    sudo && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone

# 创建挂载点目录（MCSManager 实例根目录）

RUN mkdir -p /data

# 设置工作目录为 MCSManager 挂载目录

WORKDIR /data

# 启动时自动赋予权限并执行 start.sh

CMD bash -c '\
  chmod +x /data/start.sh && \
  find /data/bin -type f -exec chmod +x {} \; && \
  bash /data/start.sh'`

