---
title: Method of moments 
data: 2019-1-16 22:00:00
categories: Research
tags:
   - RW3D
   - Paricle tracking
   - Lagrangian method
mathjax: true
---
pick out from "Salamon, P., Fernàndez-Garcia, D., & Gómez-Hernández, J. J. (2006). **A review and numerical assessment of the random walk particle tracking method**. *Journal of contaminant hydrology*, *87*(3-4), 277-305." 本文是自己学习了解粒子追踪算法中如何计算弥散度的记录
<!-- more -->
**Method of moments**
method of moments developed by Aris(1956)^1^ is used to evaluate numerical simulations.
the spatial moments serve as a simple, physical meaningful description of overall plume behavior. The zeroth moment represents the total solute mass contained in the plume, the first moment measures the location of the center of mass of the plume and the second moment corresponds to the spreading of the plume around the center of mass. the general formula to calculate the spatial moments of a plume is given as
$$
M_p(t) = 
\int \int \int _{-\infty} ^{\infty} n x^p c(x,y,z,t) dx dy dz
\\
M_p \quad \text{moment of p-th order}
\quad n \quad \text{the prorosity}
\quad c \quad \text{the concentration at location xyz and time t}
$$
the center of mass as a function of time 
$$
X_G(t) = \dfrac{M_1}{M_0}
$$
the second spatial moment in x direction
$$
S_{xx}(t) = \dfrac{M_2}{M_0}-
\left( 
\dfrac{M_1}{M_0} 
\right) ^2
$$
The apparent average velocity $U(t)$ of the plume center of mass
$$
U(t) = \dfrac{\Delta X_G}{\Delta t}
= \dfrac{X_G(t)-X_G(0)}{t}
$$
The apparent longitudinal macro-dispersion $D_L(t)$
$$
D_L(t) = \dfrac{1}{2} \dfrac{\Delta S_{xx}}{\Delta t}
= \dfrac{1}{2} \dfrac{ S_{xx}(t)-S_{xx}(0)}{t}
$$
The apparent longitudinal macro-dispersivity $A_L(t)$
$$
A_L(t) = \dfrac{D_L(t)}{U(t)}
\dfrac{1}{2} \dfrac{S_{xx}(t)-S_{xx}(0)}{X_G(t)-X_G(0)}
$$
Apparent parameters are viewed as equivalent values in homogeneous porous media, that , when used with the classic advection-dispersion equation, lead to the same spatial moments of the plume as observed in the simulations for heterogeneous porous media.




[^1]: R. Aris **On the dispersion of a solute in a fluid flowing through a tube** Proceedings of the Royal Society of London. Series A, 235 (1956), pp. 67-78.

**Temporal moments (breakthrough curves)**

"Fernàndez-Garcia, D., Illangasekare, T. H., & Rajaram, H. (2005). Differences in the scale-dependence of dispersivity estimated from temporal and spatial moments in chemically and physically heterogeneous porous media. *Advances in water resources*, *28*(7), 745-759."

Temporal moments associated with observed BTCs at several predefined control planes were computed using a Lagrangian framework.

n-th absolute temporal moment can be calculated as the expected value of the arrival time of a particle at the control plane to the n-th power
$$
\mu _n ^{'} (x_1)=
\dfrac{1}{m_{tot}} 
\int _0 ^{\infty} t^n Q C_f(x_1,t) dt
\approx \dfrac{1}{m_{tot}}
\sum _{k=1} ^{NP_a} m_p^{(k)} (t_p^{(k)}(x_1))^{n}
\\
m_{tot}= \sum _{k=1} ^{NP_a} m_p ^{(k)}
$$
Q is the total water flux passing through the control plane. x_1 is the mean flow direction coordinate.C_f is the flux concentration of solute passing through a given surface. t_p^{(k)} is the first arrival passage time of the k-th particle, m_tot is the total mass injected, and NP_a is the total number of particles arrived at the x_1 control plane. $ \dfrac{QC_f(x_1,t)}{m_{tot}} $ is the rate of solute mass passing through the x_1 control plane at time t normalized by the total mass injected, which is the probability density function for the mass flux.

The n-th central temporal moment is calculated using the relationship between central and absolute temporal moments
$$
\begin{align}
M_{T,n(x_1)} &= \dfrac{1}{m_{tot}} 
\int 
\left( t-\mu_1 ^{'}(x_1)
\right)^n
QC_f(x_1,t) dt
\\
&=\sum _{r=0}^{n} 
\left(
\begin{matrix}
n\\r 
\end{matrix}
\right)
\mu _{n-r}^{'}(x_1) (-\mu _1^{'}(x_1))^r
\end{align}
$$
dispersivities from temporal moments were calculated as
$$
A_{11}(\xi _p) = \dfrac{\xi _p} {2} 
\dfrac{M_{T,2}(\xi _p)} { (\mu_1^{'}(\xi _p))^2}
$$
"Fernàndez‐Garcia, D., & Gómez‐Hernández, J. J. (2007). Impact of upscaling on solute transport: Travel times, scale dependence of dispersivity, and propagation of uncertainty. *Water Resources Research*, *43*(2)."

T_a is the mean arrival time for a given realization $\sigma _t ^2$ is the variance of travel times for a given realization

$$
T_a(x) \approx \dfrac{1}{M_a} 
\sum _{k=1} ^{NP_a} m_p^{(k)} t_p^{(k)}(x)
\\
\sigma _t ^2(x) \approx 
\dfrac{1}{M_a} \sum _{k=1} ^{NP_a} m_p^{(k)} (t_p^{(k)}(x))^2 
- (T_a(x))^2
$$

equivalent dispersivities
$$
A_L^b(x_c) = \dfrac{L}{2} 
\dfrac{\sigma _t ^2(x)}{T_a^2}-a_L
$$
x_c denotes the centroid location of the grid block. L is the size of the block in the mean flow direction. First passage times of particles exiting the grid block are used to estimate the mass flux breakthrough curve.