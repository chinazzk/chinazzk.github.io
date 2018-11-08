---
title: Reading Dentz(2012)
date: 2018-3-15 22:49:53
categories: Research
tags: 
   - concentration statistics
mathjax: true
---
对Dentz(2012)文章的阅读理解
<!--  more -->
<u>Concentration statistics for transport in heterogeneous media due to stochastic fluctuations of the center of mass velocity</u>

Entropy function : $$H(t)=-\int d \mathrm{x}c(\mathrm{x},t)\ln[c(\mathrm{x},t)](3)$$

**Information entropy** is defined as the [average](https://en.wikipedia.org/wiki/Expected_value) amount of [information](https://en.wikipedia.org/wiki/Information) produced by a [stochastic](https://en.wikipedia.org/wiki/Stochastic) source of data. in information theoretic terms, the information entropy of a system is the amount of "missing" information needed to determine a microstate, given the macrostate.

质心速度center of mass velocity  $$v_i(t)=\frac{d}{dt}\int d \mathrm{x}x_i g(\mathrm{x},t)(1)$$

tracer distribution $g(\mathrm{x},t=0)=\delta (\mathrm{x})$ 其中$\delta (\mathrm{x})$ 是狄拉克函数(广义函数，总伴随着积分一起出现)

百科，在除了零以外的点函数值都等于零，而其在整个定义域上的积分等于1

effective dispersion tensor$$D_{ij}^e(t)=\frac{1}{2}\frac{d}{dt} \left [ \int d \mathrm{x} x_i x_jg(\mathrm{x},t)-\int d \mathrm{x} x_i g(\mathrm{x},t)\int d \mathrm{x}x_jg(\mathrm{x},t) \right ] (2)$$

normalization constraint $$\int d \mathrm{x}c(\mathrm{x},t)=1(A1)$$

Consider a distribution $c(\mathrm{x},t)$  approximation to actual tracer distribution $g(\mathrm{x},t)$

to maximizes (3) subject to constraints (1) (2) and (A1)

(1)and(2) integrated over time

**center of mass** position  $$m_i(t)=\int d \mathrm{x}x_i c(\mathrm{x},t)(A2)$$

plume width $$\begin{equation} \begin{split}k_{ij}^e(t)&=\int d \mathrm{x}[x_i-m_i(t)][x_j-m_j(t)] c(\mathrm{x},t)(A3)\\&=\int d \mathrm{x}[x_i x_j-x_i m_j(t)-x_j m_i(t)+m_i(t)m_j(t)] c(\mathrm{x},t)\\&=\int d\mathrm{x}x_i x_jc(\mathrm{x},t)-\int d\mathrm{x}x_ic(\mathrm{x},t)m_j(t)-\int d\mathrm{x}x_jc(\mathrm{x},t)m_i(t)+\\ &\int d\mathrm{x}m_i(t)m_j(t)c(\mathrm{x},t)\\&=\int d\mathrm{x}x_i x_jc(\mathrm{x},t)-2\int d\mathrm{x}x_ic(\mathrm{x},t)\int d \mathrm{x}x_j c(\mathrm{x},t)+m_i(t)m_j(t)\\&=\int d\mathrm{x}x_i x_jc(\mathrm{x},t)-\int d\mathrm{x}x_ic(\mathrm{x},t)\int d \mathrm{x}x_j c(\mathrm{x},t)\end{split} \end{equation}$$ 

构造约束条件下新的目标函数(拉格朗日乘数法求极值)

$$\begin{split}H_0\{c(\mathrm{x},t)\}=-\int d\mathrm{x} c(\mathrm{x},t) ln[c(\mathrm{x},t)]+\lambda \int d\mathrm{x} c(\mathrm{x},t) \\+ \lambda _{ij} \int d \mathrm{x}[x_i-m_i(t)][x_j-m_j(t)] c(\mathrm{x},t)\end{split}(A4)$$

$$\begin{split}\dfrac{\partial H_0\{c(\mathrm{x},t)\}}{\partial c(\mathrm{x},t)}=0\\-\int d\mathrm{x}(ln[c(\mathrm{x},t)]+1)+\lambda \int d\mathrm{x}+\lambda _{ij}\int d \mathrm{x}[x_i-m_i(t)][x_j-m_j(t)]=0\\-1-ln[c(\mathrm{x},t)]+\lambda +\lambda _{ij}[x_i-m_i(t)][x_j-m_j(t)]=0\end{split}(A5)$$

which implies

$$c(\mathrm{x},t)=\exp (1-\lambda )\exp \{\lambda _{ij}[x_i-m_i(t)][x_j-m_j(t)]\}(A6)$$

约束条件1

$$\begin{split}\dfrac{\partial H_0\{c(\mathrm{x},t)\}}{\partial \lambda}=0\\ \int d\mathrm{x }x_i c(\mathrm{x},t)-m_i(t)=0\end{split}(A8)$$

约束条件2

$$\begin{split}\dfrac{\partial H_0\{c(\mathrm{x},t)\}}{\partial \lambda _{ij}}=0\\ \int d \mathrm{x}[x_i-m_i(t)][x_j-m_j(t)] c(\mathrm{x},t)-k_{ij}^e(t)=0\end{split}(A7)$$

联立A6-A8解得$\lambda​$ 和 $\lambda _{ij}​$

$$c(\mathrm{x},t)=\dfrac{\exp \{[\mathrm{x}-\mathrm{m}(t)][2\mathrm{k}^e(t)]^{-1}[\mathrm{x}-\mathrm{m}(t)]\}}{\sqrt {(2\pi)^d \det [\mathrm{k}^e(t)]}}$$





calculus $c(\mathrm{x},t)$ maximize :

$$H(t)=-\int d \mathrm{x}c(\mathrm{x},t)\ln[c(\mathrm{x},t)](1)$$

subject to:

$$\int d \mathrm{x}c(\mathrm{x},t)=1(2)$$ 等于 $$\int c(\mathrm{x},t) d v =1$$ ？

$$\int d \mathrm{x}x_i c(\mathrm{x},t)=m_i(t)(3)$$

$$ \begin{split}\int d \mathrm{x}x_ix_j c(\mathrm{x},t)=m_i(t)m_j(t)+k_{ij}^e(t)\end{split}(4)$$

Let c be the function that is the true solution and let $\eta(\mathrm{x},t)$ be any function which simply satisfies the homogeneous form of the constraints:

$$\int d \mathrm{x} \eta (\mathrm{x},t)=0(5)$$

$$\int d \mathrm{x}x_i \eta (\mathrm{x},t)=0(6)$$

$$ \begin{split}\int d \mathrm{x}x_ix_j \eta (\mathrm{x},t)=0\end{split}(7)$$

Then, for any scalar $\varepsilon$ , the function $c(\mathrm{x},t)+\varepsilon \eta (\mathrm{x},t)$ automatically satisfies the constraints. The integral

$$I(\varepsilon)=-\int d \mathrm{x}(c(\mathrm{x},t)+\varepsilon \eta (\mathrm{x},t))\ln[c(\mathrm{x},t)+\varepsilon \eta (\mathrm{x},t)](8)$$

is a function of $\varepsilon$ that must take its maximum value when $\varepsilon =0$ ; otherwise, the solution is not a unique maximum locally. Thus $dI/d \varepsilon =0$ at $\varepsilon =0$ .

$$-\int d \mathrm{x}\{\eta (\mathrm{x},t)\ln c(\mathrm{x},t)+ \eta (\mathrm{x},t)\}=0(9)$$

or, after simplification,

$$-\int d \mathrm{x}\eta (\mathrm{x},t)\ln c(\mathrm{x},t)=0(10)$$

Because $\eta$ is arbitrary other than that it satisfies equations (5)-(7), the coefficient $\ln c(\mathrm{x},t)$ , must be selected so that the integral is indeed 0. The only terms allowed are the ones that will automatically make the integral 0 by virtue of the constraints (5)-(7)  :

$$\ln c(\mathrm{x},t) =C_0 + C_1x_i +C_2 x_ix_j(11)$$

From

$$ c(\mathrm{x},t) = \exp \{C_0 + C_1x_i +C_2 x_ix_j\}(12)$$

and the three constraints (2)-(4) we find that the only solution is 

$$\int d \mathrm{x}\exp \{C_0 + C_1x_i +C_2 x_ix_j\}=1(2)$$

$$\int d \mathrm{x}x_i \exp \{C_0 + C_1x_i +C_2 x_ix_j\}=m_i(t)(3)$$

$$ \begin{split}\int d \mathrm{x}x_ix_j \exp \{C_0 + C_1x_i +C_2 x_ix_j\}=m_i(t)m_j(t)+k_{ij}^e(t)\end{split}(4)$$

求解$C_0$ $C_1$ $C_2$ 参考 **The concept of the dilution index** by Peter K. Kitanidis

感觉对二维和三维情形进行求解复杂，文献中也没有直接给出求解过程。 

augmenting增加，扩张