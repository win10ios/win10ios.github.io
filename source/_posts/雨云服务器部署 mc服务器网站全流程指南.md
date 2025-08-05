---
title: 雨云服务器部署 mc服务器网站全流程指南
date: 2025-05-02 18:03:19
tags: 
- mc
categories: 
- mc教程
---
## 一、雨云服务器购买与初始化
### 1. 购买服务器
1. 访问 [雨云官网](https://www.rainyun.com/)  
2. 选择「云服务器」→「立即购买」  
3. **推荐配置**：
   - 地域：香港/东京（低延迟）
   - 镜像：Ubuntu 22.04 LTS
   - 规格：1核1G（基础网站足够）
   - 存储：30GB SSD（系统盘+网站数据）

### 2. 获取服务器信息
- **IP地址**：控制台→云服务器→实例详情查看  
- **SSH密码**：通过站内信或控制台「重置密码」获取

---

## 二、安装宝塔面板
### 1. SSH连接服务器（以FinalShell为例）
1. 新建SSH连接：  
   - 主机：`你的服务器IP`  
   - 用户：`root`  
   - 密码：`雨云提供的密码`  

2. 执行安装命令：
```bash
# 国际版（推荐英文界面用户）
wget -O install.sh http://www.aapanel.com/script/install-ubuntu_6.0_en.sh && sudo bash install.sh aapanel

# 国内版（中文界面）
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh
```

### 2. 完成安装提示
- 安装成功后显示：  
  `Bt-Panel: http://服务器IP:8888/随机安全入口`  
  `username: admin`  
  `password: 随机密码`

---

## 三、配置 PHP 7.4 环境
### 1. 登录宝塔面板
1. 浏览器访问 `http://服务器IP:8888/安全入口`  
2. 同意用户协议 → 绑定宝塔账号（需注册）

### 2. 一键安装环境
1. 选择「LNMP」架构：  
   - Nginx 1.22  
   - **PHP 7.4**（重点选择！）  
   - MySQL 5.7  
   - phpMyAdmin 5.0  

2. 点击「一键安装」等待完成（约10-20分钟）

---

## 四、部署 PHP 网站源码
### 1. 上传源码到服务器[点我下载源码][1]
**方法一：宝塔文件管理器**  
1. 左侧菜单「文件」→ 进入 `/www/wwwroot`  
2. 新建文件夹 `yourdomain.com` → 上传压缩包  
3. 右键解压 → 删除原始压缩包  

**方法二：SSH命令上传**  
```bash
cd /www/wwwroot
wget https://cloud.ioip.com.mp/down.php/0a520459dce21f201b46c777b74873a9.zip
unzip 我的世界桃花源官网源码.zip
```

### 2. 创建网站
1. 宝塔面板 →「网站」→「添加站点」  
2. 关键配置：  
   - 域名：填写你的域名（或暂用IP访问）  
   - 根目录：`/www/wwwroot/yourdomain.com`  
   - PHP版本：**必须选择 7.4**  
   - 创建FTP/数据库：建议勾选  

---

## 五、PHP 7.4 专项配置
### 1. 安装必要扩展
1. 宝塔 →「软件商店」→ 已安装的 PHP 7.4 →「设置」  
2. 安装扩展：  
   - **必装**：`fileinfo` `exif` `mysqli`  
   - **推荐**：`opcache`（性能加速） `redis`（缓存支持）

### 2. 修改 PHP 配置（解决常见报错）
1. 点击「配置文件」修改以下参数：
```ini
upload_max_filesize = 50M
post_max_size = 60M
max_execution_time = 300
memory_limit = 256M
```

---

## 六、权限与安全设置
### 1. 文件权限修正
```bash
chown -R www:www /www/wwwroot/yourdomain.com
find /www/wwwroot/yourdomain.com -type d -exec chmod 755 {} \;
find /www/wwwroot/yourdomain.com -type f -exec chmod 644 {} \;
```

### 2. 防火墙配置
- 雨云默认全端口开放，无需额外设置  
- 建议宝塔「安全」页面开启「禁Ping」和「SSH防爆破」

---

## 七、验证部署结果
1. 访问 `http://你的域名` 或 `http://服务器IP`  
2. 常见问题排查：  
   - **502错误**：检查 PHP 7.4 是否正常运行  
   - **数据库连接失败**：确认数据库账号密码正确  
   - **文件权限问题**：SSH执行 `chmod -R 755 storage/`（框架类网站）

![部署成功示意图](https://tp.999845.xyz/img/2025/06/ 98e9d5b45508abfd98afd8b699e3bae3.jpg)  
*PHP 7.4 网站正常运行效果*


  [1]: https://tp.999845.xyz/img/2025/06/ 0a520459dce21f201b46c777b74873a9.zip