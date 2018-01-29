---
title: 关于2D绕柱流的Lattice Boltzmann Method
date: 2018-1-14 22:49:53
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

$u\_{lattice}$

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

c是一个二维数组$c\_{i[x,y]}$ $c=\begin{bmatrix}c[1]=[0&0]\\c[2]=[0&-1]\\c[3]=[0&1]\\c[4]=[-1&0]\\c[5]=[-1&-1]\\c[6]=[-1&1]\\c[7]=[1&0]\\c[8]=[1&-1]\\c[9]=[1&1]\end{bmatrix}$

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
![1](http://opy4d6q9k.bkt.clouddn.com/velocity.png)

**Function Definitions**
```python
# Helper function for density computation.
sumpop = lambda fin: sum(fin,axis=0) 
'''fin为入口参数 sum(fin,axis=0) 为函数体 axis=0 为fin把第一维度相加'''
```
$\sum \limits_{i=0}^8 f\_{i,nx,ny}$
$fin=\begin{bmatrix}f_0\begin{bmatrix}\ast &n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast \\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\\f_1\begin{bmatrix}\ast&n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\\ \vdots\\f_8\begin{bmatrix}\ast&n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\end{bmatrix}$

```python
# Equilibrium distribution function.
def equilibrium(rho,u): 
cu   = 3.0 * dot(c,u.transpose(1,0,2))
```
$\frac{c_k\cdot \vec{u}}{c_s^2}$
$cu[9,nx,ny]=\begin{bmatrix}c[1]=[0&0]\\c[2]=[0&-1]\\c[3]=[0&1]\\c[4]=[-1&0]\\c[5]=[-1&-1]\\c[6]=[-1&1]\\c[7]=[1&0]\\c[8]=[1&-1]\\c[9]=[1&1]\end{bmatrix}{\begin{bmatrix}u_i\begin{bmatrix}\ast&n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}u_j\begin{bmatrix}\ast&n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\end{bmatrix}}^\intercal$

$\vec{u}=\frac{1}{\rho}\sum \limits_{k=0}^8 f_k c_k$
```python
usqr = 3./2.*(u[0]**2+u[1]**2)      	
```

$usqr[nx,ny]$

$\frac{1}{2} \frac{  \vec{u} ^2 }{c_s^2}$ (注意大括号的使用)

```python
# 初始化数组
feq = zeros((q,nx,ny)) 
for i in range(q): feq[i,:,:] = rho*t[i]*(1.+cu[i]+0.5*cu[i]**2-usqr)
return feq
```

$f_k^{eq}= \rho w_k \left[ 1+\frac{c_k \cdot \vec{u}}{c_s^2}+\frac{1}{2} (\frac{c_k\cdot \vec{u}}{c_s^2})^2-\frac{1}{2}\frac{\vec{u}^2}{c_s^2}\right]$

**Setup: cylindrical obstacle and velocity inlet with perturbation**
```python
# 无流动区域
obstacle = fromfunction(lambda x,y: (x-cx)**2+(y-cy)**2<r**2, (nx,ny))
```
${(x-cx)}^2+{(y-cy)}^2 < r^2$ (注意大括号的使用)
```python
# d = 0 1 表示流速仅x方向
vel = fromfunction(lambda d,x,y: (1-d)*uLB*(1.0+1e-4*sin(y/ly*2*pi)),(2,nx,ny))  
```
$u_x=u\_{lattice}(1+0.0001\ast sin(2\pi\frac{y}{ny-1}))$

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

$f\_{3,nx}=f\_{3,nx-1}\\f\_{4,nx}=f\_{4,nx-1}\\f\_{5,nx}=f\_{5,nx-1}$

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
${\begin{bmatrix}c[1]=[0&0]\\c[2]=[0&-1]\\c[3]=[0&1]\\c[4]=[-1&0]\\c[5]=[-1&-1]\\c[6]=[-1&1]\\c[7]=[1&0]\\c[8]=[1&-1]\\c[9]=[1&1]\end{bmatrix}}^\intercal {\begin{bmatrix}f_0\begin{bmatrix}\ast &n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast \\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\\f_1\begin{bmatrix}\ast&n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\\ \vdots\\f_8\begin{bmatrix}\ast&n_y&\cdots &\ast&\ast\\n_x&\ast&\cdots &\ast&\ast\\ \vdots & \vdots&\ddots &\vdots&\vdots\\ \ast&\ast&\cdots&\ast&\ast\\ \end{bmatrix}\end{bmatrix}}^\intercal​$

$\vec{u}=\frac{1}{\rho}\sum \limits_{k=0}^8 f_k c_k$

```python
# Left wall: compute density from known populations.BC with known velocity
u[:,0,:] =vel[:,0,:] 
rho[0,:] = 1./(1.-u[0,0,:]) * (sumpop(fin[i2,0,:])+2.*sumpop(fin[i1,0,:]))
```

$\rho \_w=\frac{1}{1-u_w}[f_0+f_1+f_2+2(f_3+f_4+f_5)]$

```python
# Left wall: Zou/He boundary condition.
feq = equilibrium(rho,u) # 通过式计算feq
fin[i3,0,:] = feq[i3,0,:]  # Equiulibrium Scheme
```
$f_i(x_b,t)=f_i^{eq}(\rho_w,u_w)$

```python
# Collision step.
fout = fin - omega * (fin - feq)  
for i in range(q): fout[i,obstacle] = fin[noslip[i],obstacle]    #  障碍物处不计算
```

$f_k(x,y,t+\Delta t)=f_k(x,y,t)-\omega [f_k^{eq}(x,y,t)-f_k(x,y,t)]$

```python
# Streaming step.
for i in range(q): 
    #  先按行移动，再按列移动
    fin[i,:,:] = roll(roll(fout[i,:,:],c[i,0],axis=0),c[i,1],axis=1)  
```

$f_k(x+\Delta x,y+\Delta y,t+\Delta t)=f_k(x,y,t+\Delta t)$

```python
# Visualization 输出图像
if (time%100==0): 
    plt.clf() #清理当前figure
    # imshow() 将numpy array 绘制成image
    plt.imshow(sqrt(u[0]**2+u[1]**2).transpose(),cmap=cm.Reds)
    # str().zfill(width) 使用零填充字符串width 是字符串最终的宽度
    plt.savefig("vel."+str(time/100).zfill(4)+".png")
```