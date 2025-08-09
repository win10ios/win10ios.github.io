---
title: butterfly主题在hexo中的配置
date: 2025-08-09 21:03:13
tags: 
- web
categories: 
- web
---
## 1. 安装Butterfly主题

### 方法一：Git克隆（推荐）

bash

cd themes

git clone https://github.com/jerryc127/hexo-theme-butterfly.git

--depth=1

### 方法二：手动下载
1. 从[Butterfly Releases](https://github.com/jerryc127/hexo-theme-butterfly/releases)下载最新版
2. 解压到`themes/butterfly`目录

## 2. 去除子模块绑定

### 步骤说明

bash
解除子模块关联

git rm --cached themes/butterfly

rm -rf themes/butterfly/.git
清理残留配置（可选）

git config -f .gitmodules --remove-section submodule.themes/butterfly

rm -rf .git/modules/themes/butterfly

### 文件结构验证
确保处理后目录包含：

themes/butterfly/

├── _config.yml # 主配置文件（27KB）

├── layout/ # 模板文件

├── source/ # 静态资源

├── scripts/ # 脚本文件

└── languages/ # 多语言文件

## 3. 推送到GitHub仓库

### 配置Hexo
修改`_config.yml`：

yaml

theme: butterfly

### 提交变更

bash

git add themes/butterfly

git commit -m "feat: 添加butterfly主题并解除子模块"

git push origin main

## 4. 主题更新方法

### 手动更新
1. 备份`_config.yml`和自定义文件
2. 覆盖`themes/butterfly`目录
3. 恢复配置文件

### 自动化建议

yaml
.github/workflows/update-theme.yml

jobs:

update:

steps:

    uses: actions/checkout@v4

    run: |

    rm -rf themes/butterfly

    git clone https://github.com/jerryc127/hexo-theme-butterfly

    themes/butterfly

    cp custom_config.yml themes/butterfly/_config.yml

## 注意事项
1. 操作前备份`_config.yml`（27KB配置文件）
2. 不要提交`themes/butterfly/.github/`目录
3. 使用`.gitignore`过滤临时文件：

themes/butterfly/.github/

themes/butterfly/node_modules/
