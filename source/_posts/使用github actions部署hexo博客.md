---
title: 使用 GitHub Actions 部署 Hexo 博客
date: 2025-08-09 21:03:13
tags: 
  - web
categories: 
  - web
---

# 📚 参考文章

- [如何优雅地使用 GitHub Action 服务将 Hexo 部署到 GitHub Pages](https://xiamu-ssr.github.io/Hexo/2024/06/19/2024-H1/2024-06-19-12-31-52/)
- [HEXO 系列教程 | 使用 GitHub 部署静态博客 HEXO | 小白向教程](https://tech.yemengstar.com/hexo-tutorial-deploy-githubpages-beginner/)

---

# 🐙 GitHub 操作流程

## 1️⃣ 创建仓库

注册并登录 GitHub 账号  
进入主界面 → 点击头像 → 进入仓库页面  
创建新仓库，命名为 `xx.github.io`（不强制使用用户名作为前缀）

![创建仓库流程图](https://tp.999845.xyz/img/2025/08/c18cf2fc9a17c4f8914679a3afb8796a.PNG)  
![主界面](https://tp.999845.xyz/img/2025/08/2e7707f806fb27341d28205d89ebf774.PNG)  
![头像入口](https://tp.999845.xyz/img/2025/08/7b9c25c6bb5644aa90d0d18d3e2434f3.PNG)  
![空仓库示意](https://tp.999845.xyz/img/2025/08/e1857b9b48c9dbc5f1cdffb1e8052e5e.PNG)  
![新建仓库](https://tp.999845.xyz/img/2025/08/c2b0312dd143b87403b858d783874bcc.PNG)  
![仓库创建成功](https://tp.999845.xyz/img/2025/08/772d01a4d2879365464fd77059a0ae70.PNG)

---

## 2️⃣ 获取 Token

进入：头像 → Settings → Developer Settings  
创建 Personal access token，至少勾选 `repo` 权限  
记住生成的 token，它将用于 GitHub Actions 自动部署

![Token 设置流程](https://tp.999845.xyz/img/2025/08/7bd95598afdac1fbd7040ef3c8a93d26.PNG)  
![Token 权限选择](https://tp.999845.xyz/img/2025/08/e71257f127f1157e231922b544b85b15.PNG)  
![生成界面](https://tp.999845.xyz/img/2025/08/777d2a4b4f81d35240823e175031dcfc.PNG)  
![复制保存](https://tp.999845.xyz/img/2025/08/a808a7117aacf10491bf7ab37216f765.PNG)  
![完成设置](https://tp.999845.xyz/img/2025/08/bbf57daed6fcf1890239c8422c55f0ea.PNG)

---

## 3️⃣ 添加 Token 到仓库

进入 Hexo 项目仓库 → Settings → Secrets  
新增名为 `GH_TOKEN` 的密钥（名称可自定义，但需与工作流一致）

![添加密钥步骤](https://tp.999845.xyz/img/2025/08/828b38eaf9f26feabfad45bf0ea01119.PNG)  
![密钥界面](https://tp.999845.xyz/img/2025/08/f2f8731b306ec2e411d118faea47d41f.PNG)  
![密钥添加成功](https://tp.999845.xyz/img/2025/08/e2650bdd0521a79f5fb31f595f5b2ff1.PNG)

---

## 4️⃣ 配置 SSH 密钥（解决国内网络问题）

### 安装必要工具：

- [Node.js（建议使用 v20）](https://nodejs.org/en)  
- [Git（建议使用 v2.44.0）](https://github.com/git-for-windows/git/releases/download/v2.44.0.windows.1/Git-2.44.0-64-bit.exe)

### 生成 SSH 密钥：

```bash
git config --global user.name "你的 GitHub 用户名"
git config --global user.email "你的 GitHub 邮箱"
ssh-keygen -t rsa -C "你的 GitHub 邮箱"
```

一路回车，进入 `C:\Users\你的用户名\.ssh`，复制 `id_rsa.pub` 内容  
添加到 GitHub → Settings → SSH and GPG keys → New SSH key

![生成密钥](https://tp.999845.xyz/img/2025/08/6184740d710eb99d406e930fbb42fca4.PNG)  
![进入设置](https://tp.999845.xyz/img/2025/08/fb9988d962cc5bd25b49af768fbf6b7d.png)  
![添加 SSH key](https://tp.999845.xyz/img/2025/08/d5902aff75ad97ba933596b7685665ec.png)  
![粘贴并保存](https://tp.999845.xyz/img/2025/08/f14834376fc7cf047eb0df227eebd2c9.png)  
![验证连接成功](https://tp.999845.xyz/img/2025/08/8074dc164fc7536ada5cb9c896ace329.png)

### 切换远程地址为 SSH：

```bash
git remote set-url origin git@github.com:yourusername/your-repo.git
```

---

# 🏗️ 本地配置 Hexo

## 1️⃣ 初始化 Hexo 项目

```bash
npm install -g hexo-cli
hexo init blog
cd blog
npm install
```

## 2️⃣ 初始化 Git 仓库

```bash
git init
git remote add origin https://github.com/yourusername/your-repo.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

# ⚙️ 配置 GitHub Actions 工作流

在项目根目录下创建 `.github/workflows/deploy.yml` 文件，内容如下：

```yaml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'

      - name: Install Dependencies
        run: npm install

      - name: Install Hexo Git Deployer
        run: |
          npm install hexo-deployer-git --save
          npm install hexo-cli -g

      - name: Clean and Generate Static Files
        run: |
          hexo clean
          hexo generate

      - name: Configure Git
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'

      - name: Deploy to GitHub Pages
        env:
          GH_TOKEN: ${{ secrets.GH_TOKEN }}
        run: |
          cd public/
          git init
          git add -A
          git commit -m "Create by workflows"
          git remote add origin https://${{ secrets.GH_TOKEN }}@github.com/yourusername/your-repo.git
          git push origin HEAD:gh-pages -f
```

---

## 3️⃣ 推送验证

```bash
git add .
git commit -m "Initial commit 2"
git push -u origin main
```

GitHub 会自动触发工作流，生成 `gh-pages` 分支。

---

## 4️⃣ 配置 GitHub Pages

进入仓库 → Settings → Pages  
选择 `gh-pages` 分支作为发布源  
部署完成后即可访问你的博客网站

![GitHub Pages 设置](https://tp.999845.xyz/img/2025/08/1f31c8f82d323f63190230a0f7eadb04.PNG)
