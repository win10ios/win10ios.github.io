---
title: 使用github actions部署hexo博客
date: 2025-08-09 21:03:13
tags: 
- web
categories: 
- web
---
# 参考文章
[如何优雅的使用Github Action服务来将Hexo部署到Github Pages][1]
[HEXO系列教程 | 使用GitHub部署静态博客HEXO | 小白向教程][2]
# GitHub
## 1.创建仓库
注册并登录一个GitHub账户【注册方法[2.2 准备 GitHub][3]】![github-1][4]![主界面][5]
点击头像，进入仓库![主界面-头像][6]![仓库空][7]
创建仓库【仓库名字为xx.github.io，并不是非要使用你的github的用户名作为xx，可以进行自定义，如我的为ioip.github.io】![新建仓库][8]![建好了][9]
## 2.获取token
点击头像- Settings-Developer Settings ![setting][10]
在个人设置中新增一个Personal access tokens。至少要包含repo权限，然后记住token。
这个token是给Github Action用的，Github Action会把Hexo编译部署到gh-pages分支。![token1][11]![token2][12]![token3][13]![token4][14]
## 3.将token填入仓库
随后在存放Hexo代码的仓库里把这个Token新增进去，名称为GH_TOKEN(随意，后面需要一致)。
【注意：先回到仓库再点击setting】![miyao1][15]![密钥2][16]![秒哟3][17]
## 4.获取ssh密钥【用于解决推送文件时GitHub国内的网络问题】
你需要在本地电脑上面安装：
nodejs（>16 版本，最新的应该有 20 版本）：[Node.js — Run JavaScript Everywhere][18]
 git（2.44.0）：[Git – Downloads][19]
在某一个盘里面创建一个文件夹，名字叫 hexo，然后右键选择 Open Git Bash Here
我们在里面输入`git config --global user.name "你的 GitHub 用户名"` `git config --global user.email "你的 GitHub 邮箱"`
我们在命令行窗口中输入`ssh-keygen -t rsa -C "你的 GitHub 邮箱"`
什么都不用管，一路回车就行。然后我们进入 C:\Users\ 用户名 \.ssh 目录（勾选显示 “隐藏的项目”）
我们用记事本打开 id_rsa.pub 并复制里面的内容。![keyssh1][20]
这个时候我们回到 GitHub，进入 Settings：![keyssh2][21]
选择左边栏的 SSH and GPG keys，点击 New SSH key：![keyssh3][22]
Title 随便取，然后把 id_rsa.pub 里面的内容到复制到 Key 中，点击 Add SSH key：![keyssh4][23]
保存完毕以后，我们可以在本地验证一下连接。依旧在 Git Bash Here 界面中输入`ssh -T git@github.com`
出现 “Are you sure……”，输入 yes 回车确认。若出现下图的提示即连接成功：![keyssh4][24]
切换到ssh用于解决推送文件时GitHub国内的网络问题
`$ git remote set-url origin git@github.com:luojunchong/PLAN.git`
# 本地配置HEXO
## 1. 初始化Hexo
安装脚手架，初始化hexo，进入之前新建的hexo文件夹，进入后安装依赖。`npm install -g hexo-cli
hexo init blog
cd blog
npm install
`
## 2. 初始化仓库
`git init
git remote add origin https://github.com/yourusername/your-repo.git
git add .
git commit -m "Initial commit"
git push -u origin main
`
## 3. 配置Github Action工作流
在.github文件夹下新增workflows文件夹，然后新增deploy.yml文件，内容如下。

里面有个node-version要和你本地的node一致。【使用node -v获取】

步骤大致意思就是使用ubuntu-latest作为基础环境，然后安装各种依赖，随后hexo generate生成博客网站静态文件夹，
把这个文件夹推送到同一仓库的gh-pages分支。`name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main  # 当推送到 main 分支时触发

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          submodules: false  # 禁用子模块检查

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
          git push origin HEAD:gh-pages -f`
## 3. 推送验证
把刚才更新的所有文件都推送一遍，github就会触发工作流，然后去网站看工作流运转的如何。
等一切运转完毕，就会发现仓库多出一个gh-pages分支。
`git add .
git commit -m "Initial commit 2"
git push -u origin main
`
## 4. 配置Github Pages
在仓库settings中配置page来源为gh-pages分支即可。等待网站部署完毕，就可以看了。网站链接可以在settings的GitHub Pages看到，也可以去action里看到。![img3][25]



  [1]: https://xiamu-ssr.github.io/Hexo/2024/06/19/2024-H1/2024-06-19-12-31-52/
  [2]: https://tech.yemengstar.com/hexo-tutorial-deploy-githubpages-beginner/
  [3]: https://tech.yemengstar.com/hexo-tutorial-deploy-githubpages-beginner/
  [4]: https://tp.999845.xyz/img/2025/08/c18cf2fc9a17c4f8914679a3afb8796a.PNG
  [5]: https://tp.999845.xyz/img/2025/08/2e7707f806fb27341d28205d89ebf774.PNG
  [6]: https://tp.999845.xyz/img/2025/08/7b9c25c6bb5644aa90d0d18d3e2434f3.PNG
  [7]: https://tp.999845.xyz/img/2025/08/e1857b9b48c9dbc5f1cdffb1e8052e5e.PNG
  [8]: https://tp.999845.xyz/img/2025/08/c2b0312dd143b87403b858d783874bcc.PNG
  [9]: https://tp.999845.xyz/img/2025/08/772d01a4d2879365464fd77059a0ae70.PNG
  [10]: https://tp.999845.xyz/img/2025/08/7bd95598afdac1fbd7040ef3c8a93d26.PNG
  [11]: https://tp.999845.xyz/img/2025/08/e71257f127f1157e231922b544b85b15.PNG
  [12]: https://tp.999845.xyz/img/2025/08/777d2a4b4f81d35240823e175031dcfc.PNG
  [13]: https://tp.999845.xyz/img/2025/08/a808a7117aacf10491bf7ab37216f765.PNG
  [14]: https://tp.999845.xyz/img/2025/08/bbf57daed6fcf1890239c8422c55f0ea.PNG
  [15]: https://tp.999845.xyz/img/2025/08/828b38eaf9f26feabfad45bf0ea01119.PNG
  [16]: https://tp.999845.xyz/img/2025/08/f2f8731b306ec2e411d118faea47d41f.PNG
  [17]: https://tp.999845.xyz/img/2025/08/e2650bdd0521a79f5fb31f595f5b2ff1.PNG
  [18]: https://nodejs.org/en
  [19]: https://github.com/git-for-windows/git/releases/download/v2.44.0.windows.1/Git-2.44.0-64-bit.exe
  [20]: https://tp.999845.xyz/img/2025/08/6184740d710eb99d406e930fbb42fca4.PNG
  [21]: https://tp.999845.xyz/img/2025/08/fb9988d962cc5bd25b49af768fbf6b7d.png
  [22]: https://tp.999845.xyz/img/2025/08/d5902aff75ad97ba933596b7685665ec.png
  [23]: https://tp.999845.xyz/img/2025/08/f14834376fc7cf047eb0df227eebd2c9.png
  [24]: https://tp.999845.xyz/img/2025/08/8074dc164fc7536ada5cb9c896ace329.png
  [25]: https://tp.999845.xyz/img/2025/08/1f31c8f82d323f63190230a0f7eadb04.PNG