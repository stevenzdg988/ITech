---
title: Anaconda+VSCode配置Python开发学习环境
date: 2020-12-15 21:36:58
tags:
    - Anaconda
    - Python
    - VSCode
    - Manjaro
categories:
    - 技术文档
---

# 前言

当前，流行语很多，其中“大数据”、“人工智能”、“信息化”、“教育信息化”备受关注，而其核心的工具大多都指向了 **Python**者们程序设计语言，不论是生产环境的开发，还是关乎国计民生的 **教育**，都在向它倾斜。近几年来，教育主管部门更是发布了重磅的政策性的指导意见，可见从事教育的一线工作者掌握这门语言是多么的迫在眉睫，它不仅能提高自身的工作效率，更能在其职业的高度上认识信息社会发展的契机和理论认识，进而引领学习者开阔视野，能够适应当今社会发展，能够对社会发展激发出更加活跃的创造力。本文从一个最最基础的角度，聊一聊 **Python** 学习、开发环境的配置，但更多的偏重于搭建 **Python** 学习环境。

# Python

**Python**，关于其的信息铺天盖地，培训机构、学习网站充斥了互联网各个角落。**Python** 中文名是蟒蛇。**Python**的创始人为荷兰人吉多·范·罗苏姆（Guido van Rossum）。1989年圣诞节期间，在阿姆斯特丹，为了打发圣诞节的无趣，决心开发一个新的脚本解释程序，作为ABC 语言的一种继承。之所以选中 **Python**（大蟒蛇的意思）作为该编程语言的名字，是取自英国20世纪70年代首播的电视喜剧《蒙提.派森的飞行马戏团》（Monty Python's Flying Circus）。

**Python** 是一种解释型脚本语言，可以应用于以下领域 Web 和 网络开发、 科学计算和统计、 人工智能、 桌面界面开发、 软件开发、 网络爬虫、 黑客最喜欢的语言。

**Python** 的主要运用领域有:

云计算：云计算最热的语言，典型的应用OpenStack

WEB开发：许多优秀的WEB框架，许多大型网站是Python开发、YouTube、Dropbox、Douban……典型的Web框架包括Django

科学计算和人工智能：典型的图书馆 NumPy、SciPy、Matplotlib、Enided 图书馆、熊猫

系统操作和维护：操作和维护人员的基本语言

金融：定量交易、金融分析，在金融工程领域，Python不仅使用最多，而且使用最多，其重要性逐年增加。

图形 GUI：PyQT，WXPython，TkInter

**Python** 在一些公司的运用有:

谷歌：谷歌应用程序引擎，代码。Google.com、 Google 爬虫、Google 广告和其他项目正在广泛使用 **Python**。

CIA：美国中情局网站是用 **Python** 开发的。

NASA：美国航天局广泛使用 **Python** 进行数据分析和计算。

YouTube：世界上最大的视频网站 YouTube 是用 **Python** 开发的。

Dropbox：美国最大的在线云存储网站，全部用 **Python** 实现，每天处理 10 亿的文件上传和下载。

Instagram：美国最大的照片共享社交网站，每天有 3000 多万张照片被共享，所有这些都是用 **Python** 开发的。

Facebook：大量的基本库是通过 **Python** 实现的

Red Hat/Centos：世界上最流行的 Linux 发行版中的 Yum 包管理工具是用 **Python** 开发的

Douban：几乎所有公司的业务都是通过 **Python** 开发的。

知乎：中国最大的 Q＆A 社区，通过 **Python** 开发（国外 Quora）

除此之外，还有搜狐、金山、腾讯、盛大、网易、百度、阿里、淘宝、土豆、新浪、果壳等公司正在使用 **Python** 来完成各种任务。

信息化时代的应用真是太多太多了，不仅 **Python** 能做到，其他的程序语言也可以做到，只是为什么选择它？我们从 **Python**之禅中可以获得一些答案吧：
    Beautiful is better than ugly.
    优美胜于丑陋（Python以编写优美的代码为目标）
    Explicit is better than implicit.
    明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似）
    Simple is better than complex.
    简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现）
    Complex is better than complicated.
    复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
    Flat is better than nested.
    扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套）
    Sparse is better than dense.
    间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题）
    Readability counts.
    可读性很重要（优美的代码是可读的）
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    即便假借特例的实用性之名，也不可违背这些规则（这些规则至高无上）
    Errors should never pass silently.
    Unless explicitly silenced.
    不要包容所有错误，除非你确定需要这样做（精准地捕获异常，不写except:pass风格的代码）
    In the face of ambiguity, refuse the temptation to guess.
    当存在多种可能，不要尝试去猜测
    There should be one-- and preferably only one --obvious way to do it.
    而是尽量找一种，最好是唯一一种明显的解决方案（如果不确定，就用穷举法）
    Although that way may not be obvious at first unless you're Dutch.
    虽然这并不容易，因为你不是 Python 之父（这里的Dutch是指Guido）
    Now is better than never.
    Although never is often better than *right* now.
    做也许好过不做，但不假思索就动手还不如不做（动手之前要细思量）
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    如果你无法向人描述你的方案，那肯定不是一个好方案；反之亦然（方案测评标准）
    Namespaces are one honking great idea -- let's do more of those!
    命名空间是一种绝妙的理念，我们应当多加利用（倡导与号召）

学习**Python** [官网](https://www.python.org) 是一个能找到不错文档的地方，同样也能找到搭建 **Python** 环境的核心[安装文件](https://www.python.org/downloads/),但是仅仅依靠其远不能达到要求，这个时候就需要加载一些第三方的应用（模块），其中之一就是 **Anaconda** 。

# Anaconda

**Anaconda**，水蚺(南美洲蟒蛇)，和蟒蛇是同类吧，就是可以便捷获取包且对包能够进行管理，同时对环境可以统一管理的发行版本。Anaconda包含了 **conda、Python** 在内的超过180个科学包及其依赖项。昂，原来 **Anaconda** 是一个 **Python** 的大管家啊！

## Windows系统下的安装操作

由于某种原因， **Anaconda** 的官网我们无法访问，文档也需要从其他第三方的网站去查询，幸好我们国内有网站提供了其镜像的下载，经常用的速度比较快的[清华镜像站下载](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)，此镜像站提供了适用多平台的安装包，感觉还是下载最新版本吧，如果你用的是Windows平台，那么就在这里下载 *Anaconda3-5.3.1-Windows-x86.exe* 或者 *Anaconda3-5.3.1-Windows-x86_64.exe* 吧，区别是前一个适合32位操作系统平台，后一个适合64位操作系统平台，至今感觉后一种比较普遍了。若是在Linux平台安装，在后面会详细介绍。

那就双击下载下来的安装文件进行安装吧，安装非常简单，一直下一步（next）默认安装就可以了，只是这个软件包含的应用众多，体量比较大，所用时间比较长，等等就好啦。给一张安装界面吧：
    ![Anaconda安装](https://img-blog.csdnimg.cn/20201216101019919.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
安装完成就可以在系统开始菜单里找到 **Anaconda** 的应用程序了，如图：
    ![Anaconda程序菜单](https://img-blog.csdnimg.cn/20201216101631215.png#pic_center)

嗯，我们找到 **Anaconda Promot** 利用右键单击选择 ```以管理员身份运行```,
    ![conda promot启动](https://img-blog.csdnimg.cn/20201216102530638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
你就会看到 **CMD** 窗口
    ![conda操作界面](https://img-blog.csdnimg.cn/20201216102623819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
接下来键入 ```conda --help```，显示 **Anaconda** 管理 **Python** 应用模块的命令提示信息。
    ![conda帮助](https://img-blog.csdnimg.cn/20201216103244327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
常用的同步更新软件包（update）、安装软件包（install）。不管是更新还是安装，都需要到软件官网去获取软件信息，由于某种原因这些操作都是非常困难的，因此需要对软件默认的配置修改成国内的快速镜像地址，这样才能高效。
在当前用户的根目录下创建 ```.condarc``` 文件，因为在 Windows系统下无法创建以"."开头的文件，因此需要利用命令行进项操作，让其生成 ```condarc``` 文件，然后利用文本编辑软件打开，编辑如下内容后保存退出。
    ![.condarc文件创建](https://img-blog.csdnimg.cn/20201216110459956.png#pic_center)
    ![.condarc文件位置](https://img-blog.csdnimg.cn/20201216111056674.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
    ![.condarc文件配置](https://img-blog.csdnimg.cn/20201216110509888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
再利用 ```conda``` 命令获取软件的过程中可能会用到 ```pip``` 进行一些下载，因此也需要将 ```pip``` 配置成国内的快速镜像地址，具体做法是，在当前用户根目录下创建 ```pip``` 文件夹，并且在此文件夹内创建 ```pip.ini``` 文件，利用文本编辑器编辑如下图内容到此文件后保存退出。
    ![pip配置文件位置](https://img-blog.csdnimg.cn/20201216111319848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)
    ![pip配置文件创建](https://img-blog.csdnimg.cn/20201216110653435.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70#pic_center)




# VSCode  