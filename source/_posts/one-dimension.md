---
title: Point Source Impulse Analytical Solution
date: 2017-05-19 16:53:55
categories: Research
tags:
   - math
mathjax: true
---
对简单的一维点源瞬时释放的结果推导过程记录如下
<!-- more -->
Governing equation
$$\frac{\partial c}{\partial t}=D\frac{ {\partial}^2c}{\partial x^2}-V\frac{\partial c}{\partial x}-\lambda c \tag{1}$$  
inital condition
$$c(x,0)=0\tag{2}$$  
boundary condition
$$c(0,t)=C_M \delta (t)=\frac{M}{An} \delta (t)\tag{3}$$ 
$$\frac{\partial c(\infty,t)}{\partial x}=0\tag{4}$$ 
where $c$ is the concentration,$D$ is the dipersion coefficient, V is the seepage velocity,$\lambda$ is the first order degradation rate,$C_M$ is the instantaneous source concentration, $M$ is the mass of solute introduced instantaneously,$A$ is the unit volume,$n$ is the porosity. 
do laplace transformation with equation 1-3
$$c(x,s)=\mathcal{L}[c(x,t)]$$
$$sc(x,s)-c|{t=0} =D\frac{ {\partial}^2c(x,s)}{\partial x^2}-V\frac{\partial (x,s)}{\partial x}-\lambda c(x,s)\\D\frac{ {\partial}^2c(x,s)}{\partial x^2}-V\frac{\partial (x,s)}{\partial x}-(\lambda +s)c(x,s)=0\tag{5}$$  
$$c(0,s)=C_M$$
$$\frac{\partial c(\infty,s)}{\partial x}=0$$
the solution of  equation 5 is 
$$c=c_1exp(k_1)+c_2exp(k_2)\tag{6}$$  
$k_1$ and $k_2$ got from the characteristic equation as fllowing
$$k^2-\frac{V}{D}k-\frac{\lambda +s}{D}=0$$
the solution are
$$k_1=\frac{\sqrt{\frac{v^2}{D^2}+4\frac{\lambda +s}{D}}+\frac{V}{D}}{2}$$
$$k_2=\frac{-\sqrt{\frac{v^2}{D^2}+4\frac{\lambda +s}{D}}+\frac{V}{D}}{2}$$
substitude the boundary condition into 6
$$c_1 +c_2=C_M$$
$$c_1k_1e^{k_1x}+c_2k_2e^{k_2x}=0$$
then $c_1=0$ and $c_2=C_M$
$$c(x,s) =C_Mexp(\frac{-\sqrt{\frac{v^2}{D^2}+4\frac{\lambda +s}{D}}+\frac{V}{D}}{2}x)\\  =C_Mexp(\frac{V}{2D}x)       exp(-\sqrt{\frac{v^2}{4D^2}+\frac{\lambda +s}{D}}x)\\C_Mexp(\frac{V}{2D}x)       exp(-x\sqrt{\frac{1}{D}}\sqrt{ s-(-\frac{v^2}{4D}-\lambda})$$

the inverse laplace  transformation  of $c(x,s)$ is 

according to $F(s-a)$~$e^{at}f(t)$  and $e^{-\sqrt {\frac{s}{\kappa}}x}$~$\frac{x}{2\sqrt{\pi \kappa t^3} }e^{-x^2/4\kappa t}$

$a=-\frac{v^2}{4D}-\lambda$ and $\kappa =D$

$$c(x,t)=C_Mexp(\frac{V}{2D}x)exp[ (-\frac{v^2}{4D}-\lambda)t]\frac{x}{2\sqrt{\pi D t^3} }exp(-\frac{x^2}{4D t})$$

[1] : Abramowitz, Milton, and Irene A. Stegun. *Handbook of mathematical functions: with formulas, graphs, and mathematical tables*. Vol. 55. Courier Corporation, 1964.

[2] : Carslaw, Horatio Scott, and John Conrad Jaeger. "Conduction of heat in solids." Oxford: Clarendon Press, 1959, 2nd ed. (1959).



这篇博客用markdown写完后，因为有公式，用hexo 无法渲染，百度之后，才知道是因为markdown对一些特殊字符的处理不友好，容易出现异常。找到了对应的解决方法，将地址贴在这里
[在Hexo中渲染MathJax数学公式1](http://www.jianshu.com/p/7ab21c7f0674)
[在Hexo中渲染MathJax数学公式2](https://segmentfault.com/a/1190000007261752)
我使用的方法是链接2中的**修改Hexo渲染源码**大致过程如下
在博客根文件夹下找到：nodes_modules/marked/lib/marked.js 文件
- 去掉\与{}的额外转义
- 将em标签对应的符号中，去掉 _ 因为markdown中有 * 可以表示斜体。
  找到代码:
  escape: /^\\([\\`*{}\[\]()# +\-.!_>])/,
  改为：
  escape: /^\\([`*\[\]()# +\-.!_>])/,
  招待代码：
  em: /^\b_((?:[^_]|__)+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  改为：
  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  启动hexo s -p 5000 调试
  这里需要注意的是：
- 在博客的markdown文件中，公式的输入{与{ ，}与}之间一定要留有空格。
- 所使用的主题一定要支持mathjax，且在配置文件中mathjax开关是打开的。
- markdown 文件中文章开头的Front-matter里要打开mathjax开关[mathjax: true]。
  用链接1中的方法，修改渲染引擎，hexo-renderer-pandoc ，在hexo g时出错，hexo-renderer-kramed ，在hexo g 与 hexo d 无错误，但是，打开网页显示404！
