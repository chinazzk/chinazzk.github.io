---
title: 独立域名与github静态博客的绑定
date: 2017-05-14 21:39:20
categories: web
tags:
       - hexo 
       - github
---
花了8块钱在[GoDaddy](https://sg.godaddy.com/)上买了个chinazzk.info的域名，期限一年。记录下独立域名与github静态博客的绑定流程。
<!-- more -->  
因为听说用国外的域名服务器可能会出现问题，所以域名服务器并没有用GoDady给的默认域名服务器，而是用腾讯的[DNSPOD](https://www.dnspod.cn/)进行解析。  
具体操作步骤见[官方帮助文档](https://support.dnspod.cn/Kb/showarticle/tsid/28/)  
.info的域名是顶级域名，在chinazzk.info域名下添加如下两条记录  

![解析设置](http://opy4d6q9k.bkt.clouddn.com/QQ0170514221420.png?imageView2/2/w/600)  
再在GoDaddy中通过DNS管理，添加DNSPOD的域名服务器  
	
	f1g1ns1.dnspod.net
	f1g1ns2.dnspod.net  
以上就完成了独立域名的绑定。  