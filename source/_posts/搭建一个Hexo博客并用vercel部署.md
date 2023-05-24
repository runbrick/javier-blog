---
title: 搭建一个Hexo博客并用vercel部署
tags: 
  - java
  - 技术
  - 学习笔记
category: java
date: 2023-05-03 21:55:56
---

**Hexo** 是一款基于**Node.js**的静态博客生成器，具有简单易用、快速高效、丰富主题、插件支持等特点，适合用于个人博客、技术文档、个人网站等场景。而且基于 **markdown** 编写起来非常顺畅，个人非常喜欢这个博客。这次下定决心搭建一个 **Hexo** 博客来实现我日常分享的一个小博客网站。

### **前置准备**

1. 一台 **windows** 笔记本电脑，当然 **mac** 电脑也是没问题的，即使 Linux 电脑也是可以的。
2. **Git**工具:  [Git (git-scm.com)](https://git-scm.com/)
3. **Node**+ **Npm**工具：[Node.js (nodejs.org)](https://nodejs.org/en) 
4. 准备好 **Hexo** 博客程序： [https://hexo.io/](https://hexo.io/)
5. 创建一个 **Git** 仓库
6. 注册一个 **vercel** 账号(如果有自己的服务器或者不适用 vercel 可以不用管这一步)：[https://vercel.com/](https://vercel.com/)
7. 准备一个域名(如果不需要独立域名可以略过这一步)
8. hexo 本地图片部署

### **正式开始**

###### **安装 Hexo 工具**

上面的 **GIT**和 **Node**在百度或者谷歌上已经有很多相关专业的教程，这里就不详细讲解这块的知识了。一般来说到官网上下载完毕直接下一步下一步即可。我们先来全局安装一个 **Hexo** 工具

```bash
npm install hexo-cli -g
```

###### **创建 hexo 本地博客程序**

安装完成之后我们就可以使用 **hexo** 的工具来创建博客了。在电脑上创建一个目录并进入该目录，我起名叫做 **javier-blog**，刚才我们安装过 **Git** 工具这时候就派上用场了。在该目录内右键找到 **Git Bash Here** 点击打开一个 **Git**的命令行窗口或者你可以使用其他的命令行工具。执行以下命令

```bash
hexo init 
# 下面是在执行上面的命令之后的结果
INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
INFO  Install dependencies
INFO  Start blogging with Hexo!
```

###### **启动 hexo 本地服务**

安装完成 **hexo**程序直接执行 `hexo s` 命令就可以打开 **hexo** 的本地服务。在命令行上会显示启动后的服务地址。

```bash
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.
```

![image](images/aPtEJvCMY6RhBe0rC1t39vkeJIn6D2QHBAmIp_kJYp4.png)

以上就正式搭建完成 **hexo** 的博客程序了。

###### **修改博客的基本信息**

修改 博客的标题，副标题，描述，语言时区等信息

![image](images/-2ypI5XGgYYyJxPI9HfQaRiB5qc3wmiuZxPUp1iJ2Lg.png)

修改博客地址

![image](images/7CmJWWbIptdmCA1TQowBT-vu4PBMiPStDn6SOCgjoko.png)

###### **创建 github 仓库**

打开 [https://github.com/](https://github.com/) 创建一个仓库用来存储我们的部署程序,创建后是这个样子的。

![image](images/vN_ncAhfigfP-OmyjElWRqUxAMsotxRElbIHkQpXviI.png)

![image](images/OeDvMHdwNTjhvTDvzMNbupaka8xJWzRlBYYg9sCg_UQ.png)

###### **同步本地 Hexo 部署页面到 github 中**

初始化 git 项目

```yaml
git init
git remote add origin https://github.com/runbrick/javier-blog.git
```

打开本地 **hexo **项目目录并执行下面的命令,用来安装 **git** 的部署组件

```yaml
npm install hexo-deployer-git --save
```

安装之后在项目目录中找到 `_config.yml`  修改 **deploy** 参数

```yaml
deploy:
  type: 'git'
  repo: 'https://github.com/runbrick/javier-blog.git'
  branch: 'master'
```

执行 `hexo g`  然后执行 `hexo d` 将静态页面部署到 **github** 上，是这个样子的。

![image](images/IOcwTUiphjTqmWR1El8GuUpiJZt88ZpACMDzbcDsuEk.png)

###### **将项目部署到 vercel 上**

如果不需要部署到 vercel 上此步可以略过

在 **dashboard **上 选择 **Create a New Project**

![image](images/zIxiVAK7k2dEKGUaEnBGcLWBv-vEoA_k47CzTuigNu4.png)

在关联的 **github **账户中，找到刚才的博客仓库，选择 **import**

![image](images/be-D4QBnCIM5QDawjZPyoxD1F1zN3txTvD0LMGVGCvY.png)

进入部署页面选择 deploy 

![image](images/ewUmRiGaVPP19iKJoC757vHZlwv-RL3IxS2O7JBpnHc.png)

生成之后点击 **Continue to Dashboard**

![image](images/YOtJqtwnuGTFq3n7EbO3jR-QtFqgdOlc3R8zs1Lo-gw.png)

点击 Visit 就可以看到刚刚生成的博客程序了

![image](images/5Wpbt3KbDMubkciUSF5jVX2coEVXKLte-q2RFWs8P3c.png)

###### **将项目资源文件放入同一个仓库中**

在命令行创建一个新的分支并切换过去

```yaml
git checkout -b source
```

提交代码

```yaml
git add .
git commit -m "first commit"
git push origin source
```

###### **常用命令**

```yaml
hexo new "name"       # 新建文章
hexo new page "name"  # 新建页面
hexo g                # 生成页面
hexo d                # 部署
hexo g -d             # 生成页面并部署
hexo s                # 本地预览
hexo clean            # 清除缓存和已生成的静态文件
hexo help             # 帮助
```
