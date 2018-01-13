---
title: 关于2D绕柱流的Lattice Boltzmann Method
date: 2018-1-12 22:49:53
categories: Research
tags: 
   - python
mathjax: true
---
2D flow around a cylinder  数值过程
<!--  more -->
**flow definition**
```python
# Total number of time iterations.
maxIter = 200000 
Re = 220.0  
# Lattice dimensions and populations. D2Q9
nx = 520
ny = 180
ly=ny-1.0 
q = 9 
# Coordinates of the cylinder.
cx = nx/4
cy=ny/2
r=ny/9  
# Velocity in lattice units. 
uLB= 0.04 
```

$u_{lattice}$

```python
nulb    = uLB*r/Re
```
$\nu=u_{lattice}r/Re$ 这里取圆柱半径为特征长度
```python
omega = 1.0 / (3.*nulb+0.5) 
```

$\omega=\frac{1}{3\nu+0.5}$

**Lattice Constants**
```python
c = array([(x,y) for x in [0,-1,1] for y in [0,-1,1]])
```

```python
[[ 0  0]    0
 [ 0 -1]    1
 [ 0  1]    2
 [-1  0]    3
 [-1 -1]    4
 [-1  1]    5
 [ 1  0]    6
 [ 1 -1]    7
 [ 1  1]]   8
```

c是一个二维数组$c_{i[x,y]}$ $c=\begin{bmatrix}c[1]=[0&0]\\c[2]=[0&-1]\\c[3]=[0&1]\\c[4]=[-1&0]\\c[5]=[-1&-1]\\c[6]=[-1&1]\\c[7]=[1&0]\\c[8]=[1&-1]\\c[9]=[1&1]\end{bmatrix}$

```python
# Lattice weights. ones()构造全1数组
t = 1./36. * ones(q) 
# 范数小于1.1 返回True 赋值1/9
t[asarray([norm(ci)<1.1 for ci in c])] = 1./9.         
t[0] = 4./9.  
```
```python
[ 0.44444444  0.11111111  0.11111111  0.11111111  0.02777778  0.02777778  0.11111111  0.02777778  0.02777778]
```
$w_i=\begin {bmatrix}w[0]=\frac{4}{9} \\w[1]=\frac{1}{9} \\w[2]=\frac{1}{9}\\ w[3]=\frac{1}{9}\\ w[4]=\frac{1}{36}\\ w[5]=\frac{1}{36}\\ w[6]=\frac{1}{9}\\ w[7]=\frac{1}{36}\\ w[8]=\frac{1}{36} \end{bmatrix}$
```python
## [(-c[i]).tolist() for i in range(q)] 为list  将-c转换为list获取指标
noslip = [c.tolist().index((-c[i]).tolist()) for i in range(q)] 
'''[0,2,1,6,8,7,3,5,4]'''
```
$-c=\begin{bmatrix}c[1]=[0&0]\\c[3]=[0&1]\\c[2]=[0&-1]\\c[7]=[1&0]\\c[9]=[1&1]\\c[8]=[1&-1]\\c[4]=[-1&0]\\c[6]=[-1&1]\\c[5]=[-1&-1]\end{bmatrix}​$
求-c的list中的元素在list c 中的指针
```python
# Unknown on right wall.[3 4 5]
i1 = arange(q)[asarray([ci[0]<0  for ci in c])]
# Vertical middle.[0 1 2]
i2 = arange(q)[asarray([ci[0]==0 for ci in c])] 
# Unknown on left wall.[6 7 8]
i3 = arange(q)[asarray([ci[0]>0  for ci in c])] 
```
![1](E:\blog.zzk\pictures\velocity.png)
**Function Definitions**
```python
# Helper function for density computation.
sumpop = lambda fin: sum(fin,axis=0) 
'''fin为入口参数 sum(fin,axis=0) 为函数体 axis=0 为fin把第一维度相加'''
```
$\sum \limits_{i=0}^8 f_{i,nx,ny}$
$fin=\begin{bmatrix}f_0\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\\f_1\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\\\vdots\\f_8\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\end{bmatrix}​$

```python
# Equilibrium distribution function.
def equilibrium(rho,u): 
cu   = 3.0 * dot(c,u.transpose(1,0,2))
```
$\frac{c_k\cdot \vec{u}}{c_s^2}$
$cu[9,nx,ny]=\begin{bmatrix}c[1]=[0&0]\\c[2]=[0&-1]\\c[3]=[0&1]\\c[4]=[-1&0]\\c[5]=[-1&-1]\\c[6]=[-1&1]\\c[7]=[1&0]\\c[8]=[1&-1]\\c[9]=[1&1]\end{bmatrix}{\begin{bmatrix}u_i\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}u_j\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\end{bmatrix}}^\intercal​$

$\vec{u}=\frac{1}{\rho}\sum \limits_{k=0}^8 f_k c_k$
```python
usqr = 3./2.*(u[0]**2+u[1]**2)      	
```

$usqr[nx,ny]$

$\frac{1}{2}\frac{{\vec{u}}^2}{c_s^2}$

```python
# 初始化数组
feq = zeros((q,nx,ny)) 
for i in range(q): feq[i,:,:] = rho*t[i]*(1.+cu[i]+0.5*cu[i]**2-usqr)
return feq
```

$f_k^{eq}= \rho w_k\left[1+\frac{c_k \cdot \vec{u}}{c_s^2}+\frac{1}{2}{(\frac{c_k\cdot \vec{u}}{c_s^2})}^2-\frac{1}{2}\frac{{\vec{u}}^2}{c_s^2}\right]$

**Setup: cylindrical obstacle and velocity inlet with perturbation**
```python
# 无流动区域
obstacle = fromfunction(lambda x,y: (x-cx)**2+(y-cy)**2<r**2, (nx,ny))
```
$(x-cx)^2+(y-cy)^2<r^2$
```python
# d = 0 1 表示流速仅x方向
vel = fromfunction(lambda d,x,y: (1-d)*uLB*(1.0+1e-4*sin(y/ly*2*pi)),(2,nx,ny))  
```
$u_x=u_{lattice}(1+0.0001*sin(2\pi\frac{y}{ny-1}))$

$vel[2,nx,ny]$

```python
#初始化？
feq = equilibrium(1.0,vel)
fin = feq.copy()
```

**Main time loop**

```python
for time in range(maxIter):
  # Right wall: outflow condition.[3 4 5] 出口边界条件open boundary condition
  fin[i1,-1,:] = fin[i1,-2,:]
```

$f_{3,nx}=f_{3,nx-1}\\f_{4,nx}=f_{4,nx-1}\\f_{5,nx}=f_{5,nx-1}$

```python
# Calculate macroscopic density and velocity.
rho = sumpop(fin)           
```

$\rho=\sum \limits_{i=0}^8f_i$

```python
#  该行重要
u = dot(c.transpose(), fin.transpose((1,0,2)))/rho
```
$c[2,9]fin[520,9,190]$
${\begin{bmatrix}c[1]=[0&0]\\c[2]=[0&-1]\\c[3]=[0&1]\\c[4]=[-1&0]\\c[5]=[-1&-1]\\c[6]=[-1&1]\\c[7]=[1&0]\\c[8]=[1&-1]\\c[9]=[1&1]\end{bmatrix}}^\intercal {\begin{bmatrix}f_0\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\\f_1\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\\\vdots\\f_8\begin{bmatrix}*&n_y&\cdots &*&*\\n_x&*&\cdots &*&*\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ *&*&\cdots&*&*\\ \end{bmatrix}\end{bmatrix}}^\intercal$

$\vec{u}=\frac{1}{\rho}\sum \limits_{k=0}^8 f_k c_k​$

```python
# Left wall: compute density from known populations.BC with known velocity
u[:,0,:] =vel[:,0,:] 
rho[0,:] = 1./(1.-u[0,0,:]) * (sumpop(fin[i2,0,:])+2.*sumpop(fin[i1,0,:]))
```

$\rho _w=\frac{1}{1-u_w}[f_0+f_1+f_2+2(f_3+f_4+f_5)]$

```python
# Left wall: Zou/He boundary condition.
feq = equilibrium(rho,u) # 通过式计算feq
fin[i3,0,:] = fin[i1,0,:] + feq[i3,0,:] - fin[i1,0,:] # 存疑？
```

