---
title: mac与win10两台电脑同步更新静态网页博客
date: 2017-05-14 19:32:53
categories: web
tags:
    - hexo
    - github
    
---
目前有两台终端，分别是电脑A(Mac)电脑B(Win10)。已经在电脑A(Mac)下搭建好Hexo+GitHub的博客,现在要在电脑B(Win10)下搭建同样的环境进行同步更新。
<!-- more -->
***
### 1 电脑B(win10)下搭建hexo
参考帖子：[Windows下用Hexo+GithubPage搭建博客]([http://blog.csdn.net/wx_jin/article/details/51027783](http://blog.csdn.net/wx_jin/article/details/51027783))  
按照以上教程在window下安装1)e.js 2)Git 3) 配置SSH Key 4)安装Hexo  
### 2 A电脑备份博客内容及配置到GitHub  
2.1 配置.gitignore文件  
通过该文件添加不需要备份的文件夹或文件目录，在Mac下该文件默认是隐藏的，在终端中输入以下命令显示隐藏  
	
	defaults write com.apple.finder AppleShowAllFiles -bool true   
操作结束后，建议重新进行隐藏，命令如下  
	
	defaults write com.apple.finder AppleShowAllFiles -bool false  
进入博客目录文件夹下，找到此文件，用文本编辑器打开，在最后增加两行内容  

	/.deploy_git
	/public  

2.2 初始化仓库并同步到远程  
在博客根目录下，输入以下命令：  
	
	git init  //初始化本地仓库，建立.git文件夹(注意该文件夹默认为隐藏文件夹)
	git add . //添加备份的文件
	git commit -m "Blog Source Hexo"
	git branch hexo  //新建hexo分支	
	git checkout hexo  //切换到hexo分支上
	git remote add origin git@github.com:yourname/yourname.github.io.git  //将本地与Github项目对接
	git push origin hexo  //push到Github项目的hexo分支上  
	
### 3 B电脑中下拉远程仓库文件及更新博客  
将博客根目录下的初始文件夹及文件删除，然后右击博客根目录，打开git bush,在git bush中执行以下命令  
	
	git clone -b hexo  https://github.com/username/username.github.io  //将Github中hexo分支clone到本地
	cd  yourname.github.io  //切换到刚刚clone的文件夹内
	npm install    //注意，这里一定要切换到刚刚clone的文件夹内执行，安装必要的所需组件，不用再init  
到这里B电脑中的环境已经搭建完成。接下来是更新博客。  
	
	hexo new post "new blog name"   //新建一个.md文件，并编辑完成自己的博客内容
	git add source  //之后若不更改配置，每次只要更新sorcerer中的文件到Github中即可
	git commit -m "XX"
	git push origin hexo  //更新分支
	hexo g
	hexo d  
到这里，就完成了在B电脑中更新博客，再回到A电脑中更新博客，在博客根目录下，终端中输入以下命令  
	
	git pull origin hexo  //先pull完成本地与远端的融合
	hexo new post " new blog name"
	git add source
	git commit -m "XX"
	git push origin hexo
	hexo clean
	hexo g
	hexo d  
若推送失败可以尝试强制推送：```git push origin hexo -f```
推送之后打开https://github.com/chinazzk/chinazzk.github.io/  查看分支中文件夹及文件是否更新。  