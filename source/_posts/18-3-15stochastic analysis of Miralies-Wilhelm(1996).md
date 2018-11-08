---
title: stochastic analysis of Miralies-Wilhelm(1996)
date: 2018-3-15 22:49:53
categories: Research
tags: 
   - stochastic analysis
mathjax: true
---
对于Miralies-Wilhelm(1996)文章的推导梳理(unacomplished)
<!--  more -->
$$n\dfrac{\partial c}{\partial t} = \dfrac{\partial}{\partial x_i} \left[  E_{ij} \dfrac{\partial c}{\partial x_j} - cq_i\right]- n \mu c [1]$$

$i,j=1,2,3$

$$\dfrac{\partial q_i}{\partial x_i} = 0 $$

假设n为常数

$$\dfrac{\partial c}{\partial t} = \dfrac{\partial}{\partial x_i} \left[  D_{ij} \dfrac{\partial c}{\partial x_j} - cv_i\right]-  \mu c [2]$$

$$\dfrac{\partial (C + C')}{\partial t} = \dfrac{\partial}{\partial x_i} \left[  (D_{ij} + D_{ij}')\dfrac{\partial (C+C')}{\partial x_j} - (C+C')(v_i+v_i') \right]-  (\mu +\mu ')(C+C') [2]$$

mean equation

$$\dfrac{\partial C }{\partial t} = \dfrac{\partial}{\partial x_i} \left[  D_{ij} \dfrac{\partial C}{\partial x_j} + <D_{ij} '\dfrac{\partial C'}{\partial x_j}>- CV_i -<C'V_i'>\right]-  \mu C -<\mu'C'>[2]$$

perturbation equation

$$\dfrac{\partial C' }{\partial t} = \dfrac{\partial}{\partial x_i} \left[  D_{ij}' \dfrac{\partial C}{\partial x_j} + D_{ij} \dfrac{\partial C'}{\partial x_j}- C'V_i -CV_i'\right]-  \mu ' C -\mu C'[2]$$

For convenience the coordinate axis x1 is aligned in the direction of mean fluid flow so that V1=V and V2=V3=0。 The local dispersion tensor approximated in the form 

introduce a moving coordinate system

$$\xi _1 = x_1 - \int v_e dt'$$

$$\dfrac{\partial C' }{\partial t} - v_e \dfrac{\partial C'}{\partial \xi _1} +V \dfrac{\partial C'}{\partial \xi _1} + v_i' \dfrac{\partial C}{\partial \xi _i} = \\\dfrac{\partial}{\partial \xi_1} \left[  D_{11}' \dfrac{\partial C}{\partial \xi _1} + D_{11} \dfrac{\partial C'}{\partial \xi_1}\right] + \dfrac{\partial}{\partial \xi_2} \left[  D_{22}' \dfrac{\partial C}{\partial \xi_2} + D_{22} \dfrac{\partial C'}{\partial \xi_2}\right] + \dfrac{\partial}{\partial \xi_3} \left[  D_{33}' \dfrac{\partial C}{\partial \xi_3} + D_{33} \dfrac{\partial C'}{\partial \xi_3}\right]-  \mu ' C -\mu C'[2]$$



带入

$$\begin{aligned} C' &=  \int \exp (ik_j x_j)dZ_c \\&= \int \exp [i(k_1(\xi _1 +\int V_edt')+k_2\xi _2 +k_3 \xi _3)] dZ_c\end{aligned}$$

速度，局部弥散系数，降解常数等扰动同上形式一致,得到谱空间扰动方程

$$\dfrac{\partial}{\partial t} dZ_c + (iVk_1 + D_{ii}k_i^2  + \mu) dZ_c = - \dfrac{\partial C}{\partial \xi _i} dZ_{v_i} + \dfrac{\partial ^2C}{\partial \xi _i^2} dZ_{D_{ii}} - CdZ_{\mu}$$

引入

$$\dfrac{\partial }{\partial t} dZ_c = - \mu _e dZ_c$$

解得

$$dZ_c =  dZ_{\hat c} \exp [- \int \mu _e dt']$$

$$\dfrac{\partial}{\partial t} dZ_{\hat c} + (iVk_1 + D_{ii}k_i^2  + \mu - \mu _e) dZ_{\hat c} = [- \dfrac{\partial C}{\partial \xi _i} dZ_{v_i} + \dfrac{\partial ^2C}{\partial \xi _i^2} dZ_{D_{ii}} - CdZ_{\mu} ]\exp [ \int \mu _e dt'] $$

The solution is 

$$dZ_{\hat c} = \int \exp [-\beta (t-\tau)] [- \dfrac{\partial C}{\partial \xi _i} dZ_{v_i} + \dfrac{\partial ^2C}{\partial \xi _i^2} dZ_{D_{ii}} - CdZ_{\mu} ] \exp [ \int \mu _e dt']$$

$$\beta = iVk_1 + D_{ii}k_i^2  + \mu - \mu _e$$

最终得到

$$dZ_c = \dfrac{1-e^{-\beta t}}{\beta}[- \dfrac{\partial C}{\partial \xi _i} dZ_{v_i} + \dfrac{\partial ^2C}{\partial \xi _i^2} dZ_{D_{ii}} - CdZ_{\mu} ]$$

cross correlations are 

$$\begin {aligned}<D_{ii}'C'> &= \int E[dZ_{D_i}dZ_c ^*] \\ &= \int \dfrac{1-e^{- \beta t}}{\beta} [- \dfrac{\partial C}{\partial \xi _i} E(dZ_{D_i}dZ_{v_i} )+ \dfrac{\partial ^2C}{\partial \xi _i^2} E(dZ_{D_{ii}} dZ_{D_{ii}}) - CE(dZ_{D_{ii}}dZ_\mu)]\end{aligned}$$

$$<V_i'C'> =  \int \dfrac{1-e^{- \beta t}}{\beta} [- \dfrac{\partial C}{\partial \xi _i} E(dZ_{v_i}dZ_{v_i} )+ \dfrac{\partial ^2C}{\partial \xi _i^2} E(dZ_{v_{i}} dZ_{D_{ii}}) - CE(dZ_{v_{i}}dZ_\mu)]$$

$$<\mu'C'> =  \int \dfrac{1-e^{- \beta t}}{\beta} [- \dfrac{\partial C}{\partial \xi _i} E(dZ_{\mu}dZ_{v_i} )+ \dfrac{\partial ^2C}{\partial \xi _i^2} E(dZ_{\mu} dZ_{D_{ii}}) - CE(dZ_{\mu}dZ_\mu)]$$

求解

$$I(t) = \int \dfrac{1-e^{-\beta t}}{\beta} S_{ff}d\textbf k$$

$$I(t) = \int \dfrac{1-\exp[-( iVk_1 + D_{ii}k_i^2  + \mu - \mu _e)t]}{iVk_1 + D_{ii}k_i^2  + \mu - \mu _e} S_{ff}d\textbf k$$

$$I(t) = \int \dfrac{(1-\exp[-( iVk_1 + D_{ii}k_i^2  + \mu - \mu _e)t])(-iVk_1 + D_{ii}k_i^2  + \mu - \mu _e)}{V^2k_1^2 + (D_{ii}k_i^2  + \mu - \mu _e)^2} S_{ff}d\textbf k$$

$u_i=\lambda _i k_i$

$$I(t) = \int \dfrac{(1-\exp[-( iVu_1/\lambda _1 + D_{ii}u_i^2/\lambda _i^2  + \mu - \mu _e)t])(-iVu_1/\lambda _1 + D_{ii}u_i^2/\lambda _i^2  + \mu - \mu _e)}{V^2u_1^2/\lambda _1^2 + (D_{ii}u_i^2/\lambda _i^2  + \mu - \mu _e)^2} S_{ff}d\textbf k$$

simplicity 1

只考t趋向于无穷，稳定状态，只考虑横向局部弥散。

simplicity 2

考虑t趋向于零，

$$S_{ff}=\dfrac{\sigma _f^2 \lambda _1 \lambda _2 \lambda _3}{\pi ^2 (1+\lambda _1^2 k_1^2 + \lambda _2^2 k_2^2 + \lambda _3^2 k_3^2)}$$

