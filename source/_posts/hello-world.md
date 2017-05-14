---
title: 搭建hexo＋github静态网页博客的五分钟教程
date: 2017-05-9 11:22:18
categories: web

tags: 
     - hexo 
     - github
 
---
  熬了个夜，才搞好…… funny thing anyway。两个教程，一个说十小时，一个说五分钟。把大概过程梳理一下，做个备案。
  <!-- more --> 
***
先贴上参考帖子的链接：

* [5分钟搭建免费个人博客](http://www.jianshu.com/p/4eaddcbe4d12)

* [10个小时搭建独立博客](http://www.chinaz.com/web/2016/0105/491998.shtml)

1 创建Github域名和空间
> 1.1 首先你需要注册一个Github账号，注意username，这会影响到你的域名，域名：username.github.io  
> 1.2 创建一个仓库(repository) 来存储我们的网站，点击首页New repository按钮创建仓库, Respository name 中的username.github.io 的username 一定与前面的Owner 一致，记住你的username下面会用到。  
  
  2 安装配置Hexo  
  2.1 安装HomeBrew  
  终端命令可在[Brew](https://brew.sh/index_zh-cn.html)官网首页查看  
   
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubuser  
    content.com/Homebrew/install/master/install)"
2.2 安装Git
                      
    brew install git  //安装git
2.3 安装Nodejs  
先安装nvm
     
    brew install nvm
配置shell

    export NVM_DIR=~/.nvm
    source $(brew --prefix nvm)/nvm.sh
设置别名

    nvm ls
    #v4.8.3
    nvm use 4.8.3
    #Now using node v4.8.3
    nvm alias default 4.8.3
    #default -> 4.8.3 (-> v4.8.3)
安装node

    brew install node
2.4 安装Hexo

    sudo npm install hexo-cli -g
或者
    
    npm install hexo -g  #-g表示全局安装, npm默认为当前项目安装
    hexo version
3.本地配置  
3.1 创建博客文件夹  
将下面的 username 替换成你自己的username，执行成功后，会创建出一个名为 username.github.io 的文件夹。

    hexo init username.github.io  
基础配置：打开文件username.github.io/_config.yml 修改如下几个关键值

    # URL
    ## If your site is put in a subdirectory, set url as   
    'http://  yoursite.com/child' and root as '/child/'
    url: https://github.com/username/username.github.io
    root: /
[其他设置](https://hexo.io/zh-cn/docs/configuration.html)  
主题配置略，[参考1](http://theme-next.iissnan.com/getting-started.html#theme-settings) [参考2](https://hexo.io/themes/)
主题配置才是比较有乐趣的^_^  
3.2 本地测试
    
    hexo s
测试服务启动，你可以在浏览器中输入 https://localhost:4000 访问。  
可用以下命令修改端口：

    hexo s -p 5000
4 配置使用Github  
4.1 配置SHH keys  
检查本机的ssh密钥
    
    cd ~/.ssh 
生成新的SSH Key

    ssh-keygen -t rsa -C "邮件地址@youremail.com"
    Generating public/private rsa key pair.
    Enter file in which to save the key 
    (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
系统提示输入加密密码

    Enter passphrase (empty for no passphrase):<输入加密串>
	Enter same passphrase again:<再次输入加密串>
在回车中会提示你输入一个密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。

> 添加 SSH Key 到 GitHub  
在本机设置SSH Key之后，需要添加到GitHub上，以完成SSH链接的设置。  
1、打开本地C:\Documents and Settings\Administrator.ssh\id_rsa.pub文件。此文件里面内容为刚才生成人密钥。如果看不到这个文件，你需要设置显示隐藏文件。准确的复制这个文件的内容，才能保证设置的成功。  
2、登陆github系统。点击右上角的 Account Settings—>SSH Public keys —> add another public keys  
3、把你本地生成的密钥复制到里面(key文本框中)， 点击 add key 就ok了  
测试  
可以输入下面的命令，看看设置是否成功，git@github.com的部分不要修改：  

    ssh-T git@github.com
     
>如果是下面的反馈：

    The authenticity of host 'github.com (207.97.227.239)' can't be   
    established.RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:  
    1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?
>不要紧张，输入yes就好，然后会看到：

    Hi aierui! You've successfully authenticated, but GitHub does not   
    provide shell access.

>设置用户信息  
现在你已经可以通过 SSH 链接到 GitHub 了，还有一些个人信息需要完善的。  
Git 会根据用户的名字和邮箱来记录提交。GitHub 也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的，名字必须是你的真名，而不是GitHub的昵称。

    $ git config --global user.name "aierui"//用户名
    $ git config --global user.email "imland@outlook.com"//填写自己的邮箱

>将当前的改动暂存在本地仓库

    $ git init
    $ git add .

>将暂存的改动提交到本地仓库，并写入本次提交的注释是”first post“

    $ git commit -m "first post"

>将远程仓库在本地添加一个引用：origin

    $ git remote add origin https://github.com/username/projectName.git

>向origin推送gh-pages分支，该命令将会将本地分支gh-pages推送到github的远程仓库，并在远程仓库创建一个同名的分支。该命令后会提示输入用户名和密码。

    $ git push origin gh-pages

>在GitHub上将gh-pages merge 到msater上  
添加部署代码  
在站点的-config.yml文件新增字段  
Deployment 站点部署到github要配置这里, 非常重要

    deploy:
    type: git 部署类型若有问题，其他类型自行google之
    repository: https://github.com/Aierui/aierui.github.io.git
    branch: master
    plugins: -hexo-generator-feed

>merge后就可以部署上去了，在Git命令黑窗口里输入  

    $ hexo clean 
    $ hexo g #生成静态网页
    $ hexo d #开始部署  
在浏览器地址栏输入：https://username.github.io/ 即可查看自己的个人博客。





