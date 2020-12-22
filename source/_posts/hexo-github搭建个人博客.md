---
title: hexo+github搭建个人博客并部署到GitHub上
date: 2018-05-11 15:00:28
tags:
---

## 准备步骤

  1. 安装Node.js和配置好Node.js环境;
  2. 安装Git和配置好Git环境;
  3. Github账户注册和新建项目
   * 登陆github 点击这里新建一个仓储
   ![新建一个仓储](images/create-repository.png "Optional title")

   * 按下面步骤操作，项目必须要遵守格式：账户名.github.io,并且需要勾选Initialize this repository with a README
    ![操作步骤](images/create-steps.png "Optional title")

   * 然后这个仓储就创建好了，图中的地址一定要记好，将Hexo与Github page联系起来的时候会用到
    ![创建成功](images/create-finished.png "Optional title")

## 安装hexo

  1. 输入`npm install hexo -g`，开始安装Hexo;
  2. 输入`hexo -v`，检查hexo是否安装成功.

  [hexo常用命令笔记] (https://segmentfault.com/a/1190000002632530)

## 初始化 hexo

  1. 在自己认为合适的地方创个文件夹: 例如 blog，然后通过命令行 `cd blog` 进入到该文件夹里面;
  2. 输入`hexo init`，初始化该文件夹;
  3. 输入`npm install`，安装所需要的组件;
  4. 输入`hexo g`，首次体验Hexo;
  5. 输入`hexo s`，开启服务器，访问[该网址] (http://localhost:4000/blog.github.io/.) ，正式体验Hexo。

    问题：假如页面一直无法跳转，那么可能端口被占用了。
    此时我们ctrl+c停止服务器，接着输入`hexo server -p 端口号` 来改变端口号

## 在 _config.yml 进行基础配置

```
# Site
title: Hexo   // 博客名称
subtitle:
description:
keywords:
author: John Doe  // 作者
language:
timezone:
theme:   // 主题

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yoursite.com/blog.github.io
root: /blog.github.io/
permalink: :year/:month/:day/:title/
permalink_defaults:

```

其中可以在 [hexo Themes](https://hexo.io/themes/) 浏览更多主题

以安装 `BlueLake` 主题为例：
 1. 安装主题
 在 blog 文件夹下打开cmd，输入命令：`git clone https://github.com/iissnan/hexo-theme-nextthemes/next（next为主题名字）`，来获得更多主题
 ```
 git clone https://github.com/chaooo/hexo-theme-BlueLake.git themes/BlueLake
 ```

 2. 安装主题渲染器
 `BlueLake`是基于`jade`和`stylus`写的，所以需要安装`hexo-renderer-jade`和`hexo-renderer-stylus`来渲染
 ```
 npm install hexo-renderer-jade@0.3.0 --save
 npm install hexo-renderer-stylus --save
 ```
 3. 启用主题
 打开根`_config.yml`配置文件，找到`theme`字段，将其值改为`BlueLake`(先确认主题文件夹名称是否为BlueLake)

 4. 更新主题
 今后若主题添加了新功能正是您需要的，您可以直接git pull来更新主题。
  ```
   cd themes/BlueLake
   git pull
  ```

## 将Hexo与Github page联系起来

  1. 设置Git的user name和email（如果是第一次的话）
  2. 输入`cd ~/.ssh`，检查是否有.ssh的文件夹;
  3. 输入ls，列出.ssh文件下的内容;
  4. 如果没有.ssh文件夹，输入`ssh-keygen -t rsa -C '929762930@qq.com'`，连续三个回车，生成密钥，
    最后得到了两个文件：id_rsa和id_rsa.pub（默认存储路径是：C:\Users\Administrator\.ssh）。
  5. 使用`cd id_rsa.pub`命令进入id_rsa.pub目录,再使用`more id_rsa.pub`命令查看密钥的内容;
  6. 登录Github，点击头像下的settings，添加ssh: 新建一个new ssh key，将id_rsa.pub文件里的内容复制上去;
  7. 输入`ssh -T git@github.com`，测试添加ssh是否成功,如果看到Hi后面是你的github用户名，就说明成功了;
  8. 配置Deployment，在 blog 文件夹中，找到_config.yml文件，修改repo值（在末尾）

     ```
    # Deployment
    ## Docs: https://hexo.io/docs/deployment.html
    deploy:
      type: git
      //github上创建好的仓储地址：格式如下 https://账户:密码@你的仓储地址，也就是上面要你记住的地址
      repository: git@github.com:liumengjie2148/blog.github.io.git
      branch: master

     ```

## 新建一篇博客以及部署到github

  ### 新建博客

  新建一篇博客，在cmd执行命令：`hexo new post "博客名"`,这时候在文件夹_posts目录下将会看到已经创建的文件

  ### 部署到github

  1. 需要安装一个扩展：`npm install hexo-deployer-git --save`;
  2. 使用编辑器编好文章，那么就可以使用命令：`hexo d -g`，生成以及部署了
  [markdown语法]（http://www.markdown.cn/ ）
  3. 部署成功后访问你的地址：http://用户名.github.io, 那么将看到生成的文章
