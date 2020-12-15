---
title: Hexo博客在多台终端同步管理
date: 2020-12-15 09:08:06
tags: 
    - Hexo
    - Nodejs
    - Git
    - Markdown语法
categories:
    - 技术文档

---

## Hexo+Github博客在多台终端同步管理

最近这两天研究Hexo制作个人博客，发现自己放了好长时间的博客系统在一次环境系统升级过后崩溃了，然后决定从零开始。在配置过程中又遇到了这样的问题，在不同的电脑终端同步博客遇到了困难，不能实现要求，然后查找了很多博文和官网的文档，最终找到了解决的办法，在此记录下来以便能帮助有需要的后来人。此方法利用Git管理Hexo产生的文件，实现在不同的电脑上同步（Github）所需的文件，也可以正常的在不同的Hexo环境里进行相应操作，只要在做完之后同步到Github的仓库里即可。下面具体来说说：

## 搭建node.js环境

### Windows环境

官网下载并安装[Nodejs和npm](https://nodejs.org/en/)。

### Linux环境（本文以Manjaro为例）

    $sudo pacman -S nodejs npm

### 配置npm国内镜像源

    $npm config set registry https://registry.npm.taobao.org

## 安装Git

### Windows环境

官网下载并安装[Git](https://git-scm.com/download/win)。

### Linux环境（本文以Manjaro为例）

    sudo pacman -S git

## 安装Hexo（Hexo环境搭建）

这里不赘述直接给出安装方法（命令）所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo。

    $npm install -g hexo-cli

安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

    $hexo init <Blog-folder>
    $cd <Blog-folder>
    $npm install
    $npm install hexo-deployer-git --save

新建完成后，指定文件夹<Blog-folder>的目录如下：

    .
    ├── _config.yml
    ├── package.json
    ├── scaffolds
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes

之后就可以利用 **hexo** 命令进行一些操作，可以参看[Hexo官网文档](https://hexo.io/zh-cn/docs/)

## 思路

### Github 分支管理Hexo环境目录

方案主要是通过维护两条git分支来实现，第一个分支是 Gihub 上默认的 **master** 分支，用于存放发布的博文的静态页面；另外再新建一个分支，比如命名为 **Hexo** ，通过此分支存放主题、原始的博客文件等等，这些文件才是不同电脑需要同步的文件；当每次修改主题或者新增博文后，先将修改的主题文件（在themes文件夹下）或者新增博客（在source文件夹下），同步到远程的 **Hexo** 分支，然后在通过 *hexo g -d*命令发布博文，也就是将新增的博文的静态页面同步到 **master** 分支。这样通过不同的分支管理不同的文件，实现了多台电脑同步更新博文的功能。

### Hexo 目录结构

当执行 *hexo g -d* 后目录结构发生了变化，如下：

    .
    ├── .gitignore
    ├── .deploy_git
    |   ├── ...
    |   ├── ...
    |   └── ...
    ├── node_modules
    ├── public
    ├── package-lock.json
    ├── db.json
    ├── _config.yml
    ├── package.json
    ├── scaffolds
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes

其中 *.deploy_git*、*public* 两个文件夹下存放的是博客文章的静态页面，通过对比 Github 上博客仓库的**master**分支可以看到**master**分支下面的文件结构和 *.deploy_git* 文件夹下的文件是一致的，这个应该可以判断通过 *hexo g -d* 发布到 Github 的内容就是 *.deploy_git* 文件夹下面的文件。其余的文件夹和文件即是**Hexo**的主题文件、原始的博客文件等，刚在上文提到的**Hexo**分支，存的即时这些文件。

## 实现

### 创建分支（在 Github上操作）

首先在Github上面创建一个分支命名为Hexo，Hexo 是从master 拉取，所以分支的文件和 master 一样。

### 初始化分支（PC1上面操作）

在已经部署好Hexo环境的PC1上的安装Hexo的根目录下拉取 Hexo 分支，步骤如下：

    git clone 仓库地址

将仓库文件拉取到本地，生成的文件名为 username.github.io；进入username.github.io文件夹，除了 *.git* 文件夹以外，其他文件全部删除；

    git add .

将修改提交至暂存区；

    git commit -m “提交说明”

提交本次修改；

    git push origin Hexo

将本次修改推送到远端 Hexo；将刚才未删除的 *.git* 文件夹拷贝至Hexo根目录，删除 *username.github.io* 文件夹，此时本地的Hexo目录已与 Github上面的 Hexo 文件夹关联了，而且默认的   *.gitignore* 已经配置了忽略规则，所以已经可以将根目录下的文件同步上去，还是依次调用 *git add .* 、*git commit -m 'comment'*、 *git push origin Hexo* 三个命令将当前的文件推送至远程的 Hexo分支。

### 同步分支（PC2上面操作）

此时在PC2上，创建文件夹Blog，在Blog文件夹下面还行以下操作：

    git clone 仓库地址 BlogName
    git clone -b Hexo 仓库地址 hexo.repo

在clone下来的仓库文件夹中将*hexo.repo* 文件夹下的文件拷贝到 *BlogName* 目录中，然后安装和配置hexo环境，依次调用命令： *npm install hexo*、*npm install*、*npm install hexo-deployer-git*；（不需要*hexo init*）此时通过命令 *hexo g*、*hexo s*即可在本地开启同步下来的博客静态页面，并通过 *http://localhost:4000/* 访问。

### 发布博文（PC1或者PC2）

后续不管是PC1还是PC2，如果修改了主题或者新增了博文，按照以下步骤即可：

    git add .
    git commit -m “修改说明”
    git push origin hexo
    hexo g -d

## 结语

技术研究是在反复咀嚼的过程中提高的，伴随着咀嚼的过程时常会感到一丝丝的甜美无比的感觉，走在技术研究路上渣渣们，加油！！
