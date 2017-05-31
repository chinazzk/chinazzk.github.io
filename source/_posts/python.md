---
title: Python学习札记
date: 2017-05-21 20:22:47
categories: 随笔札记
tags:
    -Python
mathjax: true
---
这里作为学习Python的一些个人笔记
<!-- more -->
Python代码块是通过一个冒号和缩进来分割代码块，用回车符来分割语句。
Python使用try...except块处理异常，使用raise语句抛出异常。所有东西区分大小写，所有模块都有内置的属性\_\_name\_\_。
import fractions 引入分数，列：x=fractions.Fraction(1, 3) 括弧中3前需有空格。
import math引入三角函数，例：math.sin(math.pi / 2)
列表可当做以零为基点的数组使用。非空列表的首个元素始终是a_list[0]。a_list[-n]==a_list[len(a_list)-n]
列表永远不会有”缝隙“，注意列表[]和元组()的区别 

我目前看的教材是讲Python2的，用的编译器是Python3，所以可能会遇到不少问题
1 在Python 3中 print后必须接() print是一个函数！而Python 2 中直接 print 注意这两者区别
在Windows中使用Anaconda :
打开CMD 默认是C:Users\hp>  输入e: 切换到e:\ProgramData\Anaconda3>
​	# -*- coding: utf-8 -*-  Python中用于中文转码
sublime text 3 文件重命名 Ctrl +shift +p 输入 rename 