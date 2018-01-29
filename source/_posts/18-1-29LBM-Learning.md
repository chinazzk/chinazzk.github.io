---
title: lbm for learn
date: 2018-1-29 22:49:53
categories: Research
tags: 
   - python
mathjax: true
---
Learn a LBM 按运行顺序解读
<!--  more -->
```python
import sys        # 系统操作
import json       # JavaScript Object Notation 数据交换格式
import logging    # 日志模块
import h5py       # 存储数据
import numpy      # 数值计算包
import matplotlib
import matplotlib.pyplot as plt
from matplotlib import cm
from scipy.ndimage.filters import gaussian_filter
主
```



```python
# 如果模块是被直接运行的，则代码块被运行，如果模块是被导入的，则以下代码块不被运行
# __name__是当前模块名，当模块被直接运行时，模块名为__main__
if __name__ == '__main__':
	print("============================")
	print("========== LBM 2D ==========")
	print("============================")
	# 获取logger对象 （日志系统，并不理解）
	log = logging.getLogger(__name__)       

	# Domain 赋初值
	domain = Domain()
主
```
- 调用`Domain()`  完成1各种变量的定义赋None；2读取网格文件（部分变量赋值）；3写一网格文件；4定义无流动区域；5初始化宏观流速；6初始化密度；7计算宏观流速；8计算密度；9打印区域相关信息；10创建输出HD5文件。

```python
class Domain(object):    
    """ Domain definition """
    # init  method to initialization
    # 赋初值，相当于定义变量
    def __init__(self):
        # boolean, dimensions (npx, npy)
        self.solid = None 
        # dimensions: (2, npx, npy)
        self.velocity = None  
        self.init_velocity = None
        # dimensions: (npx, npy)
        self.density = None  
        self.characteristic_dimension_lu = None
        self.characteristic_dimension = None
        self.tau = None
        
        # Distribution function at equilibrium
        self.feq = None
        # Distribution function at it=n
        self.f_n = None
        # Distribution function tmp
        self.f_tmp = None  
```

```python
    # "mesh.dat" 是初值
    # read_mesh method to read mesh
    def read_mesh(self, filename="mesh.dat"):      
        """ Read the mesh """
        # 打开文件，进入读取状态
        mesh_file = open(filename, 'r')
        # 一次性读取整个文件，自动将文件内容分析成一个行的列表 see example1
        mesh_lines = mesh_file.readlines()
```
**example **
```python
>>> import os
>>> os.getcwd() #获取当前工作目录
'E:\\Program Files\\Sublime Text 3'
>>> filename='mesh_for_lbmtest.txt' 
```
在当前工作目录下新建文本如下
![1](http://opy4d6q9k.bkt.clouddn.com/1111.png)
```python
>>> mesh_lines = mesh_file.readlines()
>>> print(mesh_lines)
['0000000000\n', '0000000000\n', '0011000000\n', '0011000000\n', '0000000000\n', '0000000000\n']
```
*end of example*

```python
		# 列表转换为矩阵
		np_mesh = numpy.array(mesh_lines)
```
**example**
```python
>>> print(np_mesh)
['0000000000\n' '0000000000\n' '0011000000\n' '0011000000\n' '0000000000\n'
 '0000000000\n']
```
*end of example*

```python
        # 数有多少行
        n_line = 0
        for line in mesh_lines:
            n_line = n_line + 1
```
**example**
```python
>>> print(n_line)
6
```
*end of example*

```python
        # 数一行有多少个字符
        n_char = 0
        for point in mesh_lines[0]:
            n_char = n_char + 1
            # Remove newline
        n_char = n_char - 1
```
**example**
```python
>>> print(n_char)
10
```
*end of example*

```python
		# 构造矩阵，全部赋值False
        mymesh = numpy.zeros((n_char, n_line),dtype=numpy.bool)
```
**example**
```python
>>> print(mymesh)
[[False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]]
```
*end of example*

```python
        i_y = 0
        for line in mesh_lines:
           i_x = 0
           # 取最后一个之外的所有值
           line = line[:-1]
           for char in line:
               # 将字符先转换为整数值再转换为逻辑值赋给mymesh
               mymesh[i_x, i_y] = bool(int(char))
               # 行数累加
               i_x = i_x + 1
           # 列数累加
           i_y = i_y + 1
```
**example**
```python
>>> print(i_x,i_y)
10 6
>>> print(mymesh)
[[False False False False False False]
 [False False False False False False]
 [False False  True  True False False]
 [False False  True  True False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]]
```
*end of example*

```python
        # 两个任意大小的矩阵的克罗内克积Kronecker product
        my_scaled_mesh=numpy.kron(mymesh,numpy.ones((self.scale, self.scale)))
        # 赋矩阵行数列数
        [self.np_x, self.np_y]=numpy.shape(my_scaled_mesh)
        # 矩阵初始化，赋 False
        self.solid=numpy.zeros((self.np_x,self.np_y),dtype=numpy.bool)
        # 矩阵初始化，赋False
        self.fluid=numpy.zeros((self.np_x,self.np_y),dtype=numpy.bool)
```
**example**
```python
>>> domain_scale=1
>>> my_scaled_mesh=numpy.kron(mymesh,numpy.ones((domain_scale, domain_scale)))
>>> [domain_np_x, domain_np_y]=numpy.shape(my_scaled_mesh)
>>> domain_solid=numpy.zeros((domain_np_x,domain_np_y),dtype=numpy.bool)
>>> domain_fluid=numpy.zeros((domain_np_x,domain_np_y),dtype=numpy.bool)
>>> print(numpy.ones((domain_scale, domain_scale)))
[[ 1.]]
>>> print(my_scaled_mesh)
[[ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  1.  1.  0.  0.]
 [ 0.  0.  1.  1.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]]
>>> print([domain_np_x, domain_np_y])
[10, 6]
>>> print(domain_solid)
[[False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]]
>>> print(domain_fluid)
[[False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]]
```
*end of example*

```python
        # 列
        for i_y in range(0, domain.np_y):
            # 行
            for i_x in range(0, domain.np_x):
                # 初始化固边界
                self.solid[i_x, i_y]=bool(my_scaled_mesh[i_x, i_y])
                # 初始化 流动区域
                self.fluid[i_x, i_y]= not self.solid[i_x, i_y]
        # 关闭网格文件
        mesh_file.close()
```
**example**
```python
>>> for i_y in range(0,domain_np_y):
... 	for i_x in range(0,domain_np_x):
... 		domain_solid[i_x,i_y]=bool(my_scaled_mesh[i_x,i_y])
... 		domain_fluid[i_x,i_y]=not domain_solid[i_x,i_y]
... 
>>> print(domain_solid)
[[False False False False False False]
 [False False False False False False]
 [False False  True  True False False]
 [False False  True  True False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]
 [False False False False False False]]
>>> print(domain_fluid)
[[ True  True  True  True  True  True]
 [ True  True  True  True  True  True]
 [ True  True False False  True  True]
 [ True  True False False  True  True]
 [ True  True  True  True  True  True]
 [ True  True  True  True  True  True]
 [ True  True  True  True  True  True]
 [ True  True  True  True  True  True]
 [ True  True  True  True  True  True]
 [ True  True  True  True  True  True]]
```
*end of example*

```python
        self.l_x = self.np_x * self.lu_x
        self.l_y = self.np_y * self.lu_x
```
**example**
In the inputfile "lu_x": 0.0001

*end of example*

```python
    def write_mesh(self, filename="mesh.dat"):
        """ Write the mesh """
        # 以只读模式打开文件 filename
        mesh_file = open(filename, 'w')
        # 对于计算域中的点循环
        for i_y in range(0, domain.np_y):
            for i_x in range(0, domain.np_x):
                # 对固体边界 赋值1
                if self.solid[i_x, i_y]:
                    mesh_file.write("1")
                else:
                    mesh_file.write("0")
            # 换行
            mesh_file.write("\n")
        mesh_file.close()
```
**example**
```python
>>> filename='mesh.dat'
>>> mesh_file = open(filename, 'w')
>>> for i_y in range(0, domain_np_y):
... 	for i_x in range(0, domain_np_x):
... 		if domain_solid[i_x, i_y]:
... 			mesh_file.write("1")
... 		else:
... 			mesh_file.write("0")
... 	mesh_file.write("\n")
... 
#略
>>> mesh_file.close()
```
生成如下文件
![2](http://opy4d6q9k.bkt.clouddn.com/1112.png)

*end of example*

```python
    def define_solid(self):
        """ Define an array of boolean where False is fluid and True is solid """

        # Cylinder coordinates
        cylinder_x = int(self.np_x / 3)
        cylinder_y = int(self.np_y / 2)
        cylinder_radius = int(self.np_y / 9)
        # 特征流速
        self.characteristic_dimension_lu=cylinder_radius * 2.
        self.characteristic_dimension=cylinder_radius* 2. * self.lu_x

        # Define a domain.np_x, domain.np_y array of boolean
        # False where there is no obstacle and True where there is one
        # lambda快速定义函数，圆内无流动
        self.solid=numpy.fromfunction(lambda x,y:(x-cylinder_x)**2+
             (y-cylinder_y)**2<cylinder_radius**2,(self.np_x,self.np_y))
        # Wall bottom & top
        # 赋 top?
        self.solid[:, 0] = True
        # 赋 bottom
        self.solid[:, -1] = True
```
**example**该方法实际上不需要
```python
>>> domain_np_y=9
>>> cylinder_x = int(domain_np_x / 3)
>>> cylinder_y = int(domain_np_y / 2)
>>> cylinder_radius = int(domain_np_y / 9)
>>> domain_characteristic_dimension_lu=cylinder_radius * 2.
>>> domain_lu_x=0.0001
>>> domain_characteristic_dimension=cylinder_radius* 2. * domain_lu_x
>>> domain_solid = numpy.fromfunction(lambda x,y:(x-cylinder_x)**2+(y-cylinder_y)**2<cylinder_radius**2,(domain_np_x,domain_np_y))
>>> print(domain_solid)
[[False False False False False False False False False]
 [False False False False False False False False False]
 [False False False False False False False False False]
 [False False False False  True False False False False]
 [False False False False False False False False False]
 [False False False False False False False False False]
 [False False False False False False False False False]
 [False False False False False False False False False]
 [False False False False False False False False False]
 [False False False False False False False False False]]
>>> domain_solid[:, 0] = True
>>> print(domain_solid)
[[ True False False False False False False False False]
 [ True False False False False False False False False]
 [ True False False False False False False False False]
 [ True False False False  True False False False False]
 [ True False False False False False False False False]
 [ True False False False False False False False False]
 [ True False False False False False False False False]
 [ True False False False False False False False False]
 [ True False False False False False False False False]
 [ True False False False False False False False False]]
>>> domain_solid[:, -1] = True
>>> print(domain_solid)
[[ True False False False False False False False  True]
 [ True False False False False False False False  True]
 [ True False False False False False False False  True]
 [ True False False False  True False False False  True]
 [ True False False False False False False False  True]
 [ True False False False False False False False  True]
 [ True False False False False False False False  True]
 [ True False False False False False False False  True]
 [ True False False False False False False False  True]
 [ True False False False False False False False  True]]
```
*end of example*

```python
    def define_solid_volvo(self):
        """ Define an array of boolean where False is fluid and True is solid """

        # Triangle coordinates
        triangle_x = 0.780           # Pointe de gauche [m]
        triangle_y = self.np_y / 2.0  # Centre
        triangle_side = 0.004        # [m]
        # 特征长度
        self.characteristic_dimension = triangle_side
        # Define a domain.np_x, domain.np_y array of boolean
        # False where there is no obstacle and True where there is one
        # 赋空值
        self.solid = None
```
上面的方法函数实际上没用
```python
    def initialize_velocity(self):
        """ Define the velocity on the domain"""
        # 初始化流速矩阵
        self.init_velocity = numpy.zeros((2, self.np_x, self.np_y))
    #self.init_velocity[0, :, :]=self.velocity_lu*(1-self.solid[:, :])
        self.velocity = self.init_velocity
```

```python

    def initialize_density(self):
        """ Define the density on the domain
        """
        # 初始化密度
        self.init_density = numpy.zeros((self.np_x, self.np_y))
        # 赋值80
        self.init_density[:, :] = 80
        for i_x in range(domain.np_x):
            for i_y in range(domain.np_y):
                # Gaussian
                dist = numpy.sqrt((i_x-50)**2+(i_y-20)**2)
                self.init_density[i_x, i_y] += 420 * numpy.exp(-
                (dist*dist)/(2*10.0**2))
                #dist = numpy.sqrt((i_x-60)**2+(i_y-70)**2)
                #self.init_density[i_x, i_y] += 10.0 * numpy.exp(-
                #(dist*dist)/(2*6.0**2))
                # Hard
                #if i_x>20 and i_x<30 and i_y>20 and i_y<30:
                #    self.init_density[i_x, i_y] += 10.0
        self.density = self.init_density
```
**example**
```python
>>> domain_init_density=numpy.zeros((domain_np_x,domain_np_y))
>>> for i_x in range(domain_np_x):
... 	for i_y in range(domain_np_y):
... 		dist=numpy.sqrt((i_x-50)**2+(i_y-20)**2)
... 		domain_init_density[i_x,i_y]+=420*numpy.exp(-(dist*dist)/(2*10.0**2))
... 
>>> domain_density=domain_init_density
>>> print(domain_density)
[[ 0.00021183  0.00025743  0.00030975  0.00036899  0.00043518  0.00050814]
 [ 0.0003475   0.00042232  0.00050814  0.00060532  0.00071392  0.00083361]
 [ 0.0005644   0.00068592  0.00082532  0.00098315  0.00115952  0.00135393]
 [ 0.00090757  0.00110297  0.00132712  0.00158092  0.00186453  0.00217714]
 [ 0.00144486  0.00175595  0.00211279  0.00251685  0.00296836  0.00346603]
 [ 0.00227734  0.00276768  0.00333013  0.003967    0.00467865  0.00546307]
 [ 0.00355377  0.00431894  0.00519663  0.00619046  0.00730099  0.00852506]
 [ 0.00549045  0.0066726   0.0080286   0.00956404  0.01127976  0.0131709 ]
 [ 0.00839814  0.01020635  0.01228047  0.01462906  0.01725341  0.02014609]
 [ 0.01271789  0.0154562   0.01859718  0.02215382  0.02612806  0.03050864]]
```
*end of example*

```python
    def compute_velocity(self, lattice, f_in, explicit=False):
        """ Compute the macroscopic velocity which is an average of
            the microscopic velocities weighted by the directional
            densities (see p34 eq15 in [Lattice Boltzmann Modeling] 
            by Sukop)
        """
        # 注意 f_in 为参变量 在方法函数内未有定义
        # 已赋False
        if explicit:
            # 初始化
            tmp = numpy.zeros((2, domain.np_x, domain.np_y))
            for i_x in range(domain.np_x):
                for i_y in range(domain.np_y):
                    # 流速分量 eq15
                    for i_dir in range(lattice.dim_q):
                        tmp[:, i_x, i_y] += f_in[i_dir, i_x, i_y] * 
                        lattice.directions[i_dir, :]
            # 除rou
            tmp[:, :, :] = tmp[:, :, :] / self.density[:, :]
            self.velocity = tmp
        else:
            # 矩阵点乘 计算eq15
            self.velocity=(numpy.dot(lattice.directions.transpose(),
            f_in.transpose((1,0,2)))/self.density)
        return self.velocity
```
**example**

$$\vec u=\frac{1}{\rho}\sum\limits_ {a=0} ^8f_a\vec e_a(15)$$
```python
>>> domain_np_y=6
>>> domain_np_x=10
>>> import numpy
>>> domain_init_velocity=numpy.zeros((2,domain_np_x,domain_np_y))
>>> print(domain_init_velocity)
[[[ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]]

 [[ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]
  [ 0.  0.  0.  0.  0.  0.]]]
```
*end of example*


```python
    def compute_density(self, lattice, f_in, explicit=False):
        """ Calculate macroscopic densities and velocities from the lattice units.
            The macroscopic density is the sum of direction-specific fluid densities.
            (see p33 eq14)
        """

        if explicit:
            tmp = numpy.zeros((domain.np_x, domain.np_y))
            for i_dir in range(lattice.dim_q):
                tmp[:, :] += f_in[i_dir, :, :]
            self.density = tmp
        else:
            self.density = numpy.sum(f_in, axis=0) # 将维度一相加
        return self.density
```

**example**

$$\rho=\sum \limits _ {a=0}^8f_a$$

```python
>>> tmp=numpy.zeros((domain_np_x,domain_np_y))
>>> print(tmp)
[[ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]
 [ 0.  0.  0.  0.  0.  0.]]
```

*end of example*

```python
    def print_infos(self):
        """ Print infos about the domain """
        print(" == Infos about the domain ==")
        print("Dim x, y [m]     : ", self.l_x, self.l_y)
        # 由输入文件输入
        print("dx       [m]     : ", self.lu_x)
        print("Nnodes x, y, x*y : ", self.np_x, self.np_y, self.np_x * self.np_y)
        # 特征长度 根据计算区域算得
        print("L        [m]     : ", self.characteristic_dimension)
        print(" ==                        ==")
```

```python
    def write_hdf(self, iteration):
        """ write the solution """
        # 文件名 格式
        filename = 'output/data.' + str(iteration / 100).zfill(4) + '.dat'
        # 创建 HDF5文件
        h5file = h5py.File(filename, "w")
        # h5file.create
        h5file.close()
```

*end of class domain*

```python
	lattice = Lattice()
主
```

- 调用 Lattice() 1定义lattice节点信息，流速数，权重，边界节点设置等；2打印相关信息

```python
class Lattice(object):         # 定义类 3

    """ Lattice definition D2Q9"""

    def __init__(self):
        self.dim_q = 9
        # 1 lattice unit per time step or 1 lu/ts
        self.speed = 1. # 1. # in lu/ts
        # The numbering of the directions in the lattice unit is like this: (see p32)
        # 6 2 5
        # 3 0 1
        # 7 4 8
        # The direction vectors in the lattice unit are relative to the center. 
        # In 2D this leads to:
        # (-1, 1)     ( 0, 1)     ( 1, 1)
        # (-1, 0)     ( 0, 0)     ( 1, 0)
        # (-1,-1)     ( 0,-1)     ( 1,-1)
        # So with the numbering the direction array is:
        # ( (0,0) (1,0) (0,1) (-1,0) (0,-1) (1,1) (-1,1) (-1,-1) (1,-1) )
        self.directions = numpy.zeros((self.dim_q, 2), dtype=numpy.int)
        self.directions[0] = [0, 0]
        self.directions[1] = [1, 0]
        self.directions[2] = [0, 1]
        self.directions[3] = [-1, 0]
        self.directions[4] = [0, -1]
        self.directions[5] = [1, 1]
        self.directions[6] = [-1, 1]
        self.directions[7] = [-1, -1]
        self.directions[8] = [1, -1]
    
        # weights for each direction for the equilibrium function
        # see p35 after eq17
        # ( 1/36)     ( 1/9 )     ( 1/36)
        # ( 1/9 )     ( 4/9 )     ( 1/9 )
        # ( 1/36)     ( 1/9 )     ( 1/36)
        self.weights = numpy.zeros(self.dim_q)
        self.weights[0] = 4. / 9.
        self.weights[1] = 1. / 9.
        self.weights[2] = 1. / 9.
        self.weights[3] = 1. / 9.
        self.weights[4] = 1. / 9.
        self.weights[5] = 1. / 36.
        self.weights[6] = 1. / 36.
        self.weights[7] = 1. / 36.
        self.weights[8] = 1. / 36.
    
        # Bounceback boundary (see Figure 28 in p44)
        #         6 2 5         8 4 7
        # Lattice 3 0 1 becomes 1 0 3
        #         7 4 8         5 2 6
        # 初始化
        self.bnd_noslip = numpy.zeros((self.dim_q), dtype=numpy.int)
        # bounceback 
        self.bnd_noslip = [ 0, 3, 4, 1, 2, 7, 8, 5, 6]
    
        # Bounceback along y and slip along x
        #         6 2 5         7 4 8
        # Lattice 3 0 1 becomes 3 0 1
        #         7 4 8         6 2 5
        self.bnd_slip_x = numpy.zeros((self.dim_q), dtype=numpy.int)
        self.bnd_slip_x = [ 0, 1, 4, 3, 2, 8, 7, 6, 5]
    
        # Bounceback along x and slip along y
        #         6 2 5         5 2 6
        # Lattice 3 0 1 becomes 1 0 3
        #         7 4 8         8 4 7
        self.bnd_slip_y = numpy.zeros((self.dim_q), dtype=numpy.int)
        self.bnd_slip_y = [ 0, 3, 2, 1, 4, 6, 5, 8, 7]
    
        # For convenience of treating the boundary conditions, keep in a table the 
        # indicies of the dimensions relative to the left, middle and right column 
        # (axis y) and also the down, middle and up line (axis x)
        self.bnd_i1 = numpy.zeros(3, dtype=int)  # First column (left bnd)
        self.bnd_i1[0] = 3
        self.bnd_i1[1] = 6
        self.bnd_i1[2] = 7
        self.bnd_i2 = numpy.zeros(3, dtype=int)  # Second column (middle vertical)
        self.bnd_i2[0] = 0
        self.bnd_i2[1] = 2
        self.bnd_i2[2] = 4
        self.bnd_i3 = numpy.zeros(3, dtype=int)  # Third column (right bnd)
        self.bnd_i3[0] = 1
        self.bnd_i3[1] = 5
        self.bnd_i3[2] = 8
        self.bnd_j1 = numpy.zeros(3, dtype=int)  # First line (down bnd)
        self.bnd_j1[0] = 4
        self.bnd_j1[1] = 7
        self.bnd_j1[2] = 8
        self.bnd_j2 = numpy.zeros(3, dtype=int)  # Second line (middle horizontal)
        self.bnd_j2[0] = 0
        self.bnd_j2[1] = 1
        self.bnd_j2[2] = 3
        self.bnd_j3 = numpy.zeros(3, dtype=int)  # Third line (up bnd)
        self.bnd_j3[0] = 2
        self.bnd_j3[1] = 5
        self.bnd_j3[2] = 6
        # 打印lattice节点信息
    def print_infos(self):
        """ Print infos """
        print("Dim Q = ", self.dim_q)
        print("directions = ", self.directions)
        print("Weights = ", self.weights)
        print("BND no slip:", self.bnd_noslip)
        print("Direction & weights & noslip")
        for index in range(self.dim_q):
            print(self.directions[index],"|",self.weights[index],"|",
                  self.bnd_noslip[index])
```

*end of class lattice*

关于边界节点还需要进一步的阅读相关内容理解，待补充

```python
    # Inputfile
    # ‘python lbm_solver.py [inputfile]’ 当脚本调用时
    if len(sys.argv) > 1:
        inputfile = Inputfile(sys.argv[1])
    # 直接运行
    else:
        inputfile = Inputfile("inputfile") # 产生 inputfile.dico
主
```

- 这里调用类`Inputfile()` 1读取输入文件中的内容，2进行赋值 `inputfile.dico`

```python
# 定义 输入文件    类1
class Inputfile(object):
    # doc-string for class
    ''' inputfile infos'''
    # 定义 方法1 __init__方法的第一个参数永远是self，表示创建的实例本身
    # The __init__() method, called a constructor, 
    # is called automatically when the object is created. 
    # We provide a body that sets two instance variables of a Inputfile object. 
    # It sets the filename of object filename, and it then call the read_inputfile.
    def __init__(self, filename):
        # 赋 实例参数
        self.filename = filename
        self.read_inputfile()
     # 定义 方法2 即封装数据的函数
     # The read_inputfile() method, called a manipulator, open file  
     # 读取数据到 the dico. 并对相关变量赋值？
    def read_inputfile(self):
        # doc-string for method
        """ read inputfile """
        in_file = open(self.filename, 'r')
        # 字典 解码json 数据
        self.dico = json.load(in_file)       
        in_file.close()
        # Postproc  后处理 这里的直接赋值与inputfile存在重复？
        self.postproc_dump_niter = 50
        self.postproc_info_niter = 10
        self.postproc_vel_mag = "True"
        self.postproc_density = "True"
        self.postproc_vel_ux = "True"
```

**example**

读取如下格式的输入文件

![2](http://opy4d6q9k.bkt.clouddn.com/1113.png)

```python
>>> import json
>>> filename='inputfile'
>>> in_file=open(filename,'r')
>>> dico=json.load(in_file)
>>> in_file.close()
>>> print(dico)
{'tau': 0.6, 'characteristic_dimension': 0.01, 'postproc_vel_mag': True, 'max_iter': 10000, 'viscosity': 1e-06, 'mesh': 'steps.dat', 'lu_x': 0.0001, 'bnd_up': 'periodic', 'bnd_bottom': 'periodic', 'bnd_right': 'periodic', 'bnd_left': 'periodic', 'reynolds': 100, 'scale': 1, 'gravity': True, 'postproc_dump_niter': 10, 'postproc_vel_ux': False, 'postproc_vel_uy': False, 'postproc_force_x': False, 'postproc_force_y': False, 'postproc_vorticity': True, 'postproc_density': True, 'postproc_info_niter': 10}
```

*end of example*

```python
    # 定义 方法3 accessor 将读取的filename.dico 中的相关值赋给domain中的对应key
    def apply_inputfile(self, domain):
        """ apply inputfile """
        domain.reynolds = self.dico['reynolds']
        domain.lu_x = self.dico['lu_x']
        domain.scale = self.dico['scale']
        domain.characteristic_dimension=self.dico['characteristic_dimension']
        domain.viscosity = self.dico['viscosity']
        domain.bnd_bottom = self.dico['bnd_bottom']
        domain.bnd_up = self.dico['bnd_up']
        domain.bnd_left = self.dico['bnd_left']
        domain.bnd_right = self.dico['bnd_right']
        domain.tau = self.dico['tau']
```

*end of class inputfile*

```python
    # 通过inputfile 赋值
    inputfile.apply_inputfile(domain)
主
```

- 这里调用`inputfile.apply_inputfile()`实例为`domain`

```python
    # 特征流速计算
    domain.velocity=(domain.viscosity*domain.reynolds)/
    domain.characteristic_dimension
主
```

law of similarity : Reynolds number must be identical in both unit systems(the physical and the lattice system)

$$U=\frac{\nu Re}{l}$$

```python
    # [lu] do not understand
    domain.characteristic_dimension_lu =domain.characteristic_dimension*domain.lu_x 
主
```

```python
    # 读取网格文件key'mesh'所示文件
    domain.read_mesh(inputfile.dico['mesh'])
主
```

```python
    #domain.write_mesh()   # 该行被注释
    # 时间步长 # [s]
    domain.delta_t=1./3.*(domain.tau-1./2.)*domain.lu_x*domain.lu_x/domain.viscosity 
    # Law of similarity
    # Conversion factor for the velocity
    domain.c_vel = domain.lu_x / domain.delta_t
    domain.velocity_lu = domain.velocity / domain.c_vel
主
```

**[The lattice boltzmann method]** by Timm,Kruger   P283 EQ(7.27)等， 关于 simulation of incompressible fluids 的一些参数确定

$$\Delta t=\frac{ c_s^{\*2}(\tau ^\*-\frac{1}{2}) \Delta x^2}{\nu}$$

公式中添加了“\"来使\* 不被认为是 倾斜符号

这里$c_s^{*}$取1/3 好像与之前的代码不一致。

Conversion factor for the velocity

$$C_u=\Delta x / \Delta t$$

$$U^ * =U/C_u$$

```python
    print(" === INFOS == ")
    print(" Reynolds : ", domain.reynolds)
    print(" Viscosity [m2/s] : ", domain.viscosity)
    print(" L [m] : ", domain.characteristic_dimension)
    print(" L [lu] : ", domain.characteristic_dimension_lu)
    print(" init and inlet velocity [m/s] :", domain.velocity)
    print(" delta_t [s] :", domain.delta_t)
    print(" conv_vel [m/s] : ", domain.c_vel)
    print(" velocity [lu/ts] :", domain.velocity_lu)
主
```

```python
    ########
    # init
    initialize(domain, lattice)
主
```

- 调用函数`initialize(domain,lattice)`  计算$f_{eq}$  初始化 f_n  f_tmp  和 domain.density

```python
def initialize(domain, lattice):
    """ Initialize the domain
        Initialize rho, u, fi, fi_eq
    """
    #domain.define_solid()   # 该行被注释了
    # Initialize velocity
    # 调用类domain中的initialize_velocity()方法
    domain.initialize_velocity() 
    # Initialize density
    # 调用类domain中的initialize_density()方法
    domain.initialize_density()
    # Initialize the equilibrium  distribution function with a density of one
    # 调用函数compute_equilibrium_function()
    domain.feq=compute_equilibrium_function(domain,lattice,domain.init_density,
                                            domain.init_velocity)
    # Use it to initialize the distribution function
    domain.f_n = domain.feq.copy()
    domain.f_tmp = domain.feq.copy()
    # Initialize density
    # 调用类domain中的compute_density()方法
    domain.density = domain.compute_density(lattice, domain.f_n, explicit=False)
```

*end of initialize()*

 函数`initialize(domain,lattice)` 再调用的函数`compute_equilibrium_function()`  主要计算 $f_{eq}$ in equation 17

```python
def compute_equilibrium_function(domain, lattice, rho, u, explicit=False):
""" D2Q9 Equilibrium distribution function. see p35 eq 17 with 
    the basic speed of the lattice set at c=1"""
    # Add a volume force
    # gravity
    force = numpy.zeros(2)
    force[0] = 0
    force[1] = 0.0003
    #u[0, :, :] = u[0, :, :] + domain.tau*force[0]/rho # 该行被注释
    # see eq47 in P54 Lattice Boltzmann Models by M.Sukop
    u[1, :, :] = u[1, :, :] + domain.tau*force[1]
    # Cohesion/interaction 内聚力 see Figure 52 in p91
    # \psi_0
    psi0 = 4.0
    # \rho_0
    rho0 = 200.0
    # cohesion parameter
    G = 120.0
    cohesion_force_buffer = numpy.zeros((2, domain.np_x, domain.np_y))
	cohesion_force = numpy.zeros((2, domain.np_x, domain.np_y))
	# 初始化二维矩阵
	psi = numpy.zeros((domain.np_x, domain.np_y))
	# eq61 in p76 interaction potential
	psi = psi0 * numpy.exp(-rho0/rho)
	# 初始化三维矩阵
	psi_streamed = numpy.zeros((lattice.dim_q, domain.np_x, domain.np_y))
```

$$\vec{u} ^{eq}=\vec u + \tau \frac{\vec F}{\rho}(47)$$  

$$\psi _0=4.0 ,\rho _0 =200.0, G=120.0$$

$$\psi (\rho)=\psi _0 \exp(-\rho _0/\rho)$$

$$\psi(\vec x+\vec e_a \Delta t,t)$$



```python
	for i_dir in range(lattice.dim_q):
    	# .roll 先按第一维度移动，再按第二维度移动 eq60中\psi部分
    	psi_streamed[i_dir,:,:]=numpy.roll(numpy.roll(psi[:,:],
           lattice.directions[i_dir, 0],axis=0),lattice.directions[i_dir,1],axis=1)
```

```python
	for i_dir in range(lattice.dim_q):
    	#print("Shape of weights:", numpy.shape(lattice.weights[i_dir]))
    	#print("Shape of directions:", numpy.shape(lattice.directions[i_dir, :]))
    	#print("Shape of streamed psi:", numpy.shape(psi_streamed[i_dir, :, :]))
   		# eq60 中部分
    	cohesion_force_buffer[0,:,:]+=lattice.weights[i_dir]*
          lattice.directions[i_dir, 0]*psi_streamed[i_dir, :, :]
    
    	cohesion_force_buffer[1,:,:]+=lattice.weights[i_dir]*
          lattice.directions[i_dir, 1]*psi_streamed[i_dir, :, :]
```

$$\sum \limits _{a=1}^8 w_a \psi(\vec x+\vec e_a \Delta t,t)\vec e_a$$

```python
	# attractive/cohesive force  eq60
	cohesion_force[:, :, :] = -G * psi[:, :] * cohesion_force_buffer[:, :, :]
	domain.cohesion_force = cohesion_force
```

$$\vec F(\vec x,t)=-G\psi(\vec x,t) \sum \limits _{a=1}^8 w_a \psi(\vec x+\vec e_a \Delta t,t)\vec e_a(60)$$   

```python
	# eq47 in p54
	u[0,:,:] = u[0,:,:] + domain.tau*cohesion_force[0,:,:]/rho
	u[1,:,:] = u[1,:,:] + domain.tau*cohesion_force[1,:,:]/rho
```

```python
	# Computation of ea.velocity
	if explicit:
    	# Here is the explicit formulation
    	# 初始化
    	ea_u = numpy.zeros((lattice.dim_q, domain.np_x, domain.np_y))
    	# 计算e_a点乘u in eq17
    	for i_x in range(domain.np_x):
        	for i_y in range(domain.np_y):
            	for i_dir in range(lattice.dim_q):
                	ea_u[i_dir,i_x,i_y]=(lattice.directions[i_dir,0]*u[0, i_x, i_y]+
                                         lattice.directions[i_dir,1]*u[1, i_x, i_y])
```

$$f_a^{eq} ( \vec x)=w_a \rho ( \vec x)  \left \[1+3\frac{ \vec e_a \cdot \vec u} {c^2}+ \frac{9}{2}\frac{(\vec  e_a \cdot \vec u)^2} {c^4}-\frac{3}{2}\frac{ \vec u^2} {c^2}\right\](17)$$    其中a=0到8  为了使得'[]'不被注释，加了\

```python
	else:
    	# It can be done much faster inline with numpy:
    	ea_u = numpy.dot(lattice.directions, u.transpose(1, 0, 2))
	# Computation of velocity squared field (npx, npy)
	usqr = u[0]**2 + u[1]**2
```

```python
	# equilibrium distribution function
	# 初始化
	domain.feq = numpy.zeros((lattice.dim_q, domain.np_x, domain.np_y))
	# eq17
	for i_dir in range(lattice.dim_q):
   	 domain.feq[i_dir, :, :] = (rho * lattice.weights[i_dir] *
                        (1.+3.*ea_u[i_dir]+ 4.5 * ea_u[i_dir]**2-1.5*usqr))
	return domain.feq
```

*end of compute_equilibrium_function()*

```python
	# 调用类domain 中的 print_infos() 输出区域相关信息
    domain.print_infos()
主
```

```python
    # Time loop
    time_loop(domain, lattice, inputfile)
主 程序完
```

- 调用`time_loop()` 函数 按时间进行迭代计算，1计算密度；2计算流速；3计算feq； 4 计算collision； 5计算streaming；6应用边界条件；7输出结果图片。

```python
def time_loop(domain, lattice, inputfile):
    """ Perform the time loop """
    # 从参数中返回一个坐标矩阵
    X, Y = numpy.meshgrid(numpy.arange(0, domain.l_x, domain.lu_x), 
                          numpy.arange(0, domain.l_y, domain.lu_x))
```

**example**

domain.lu_x=0.0001

l_x=np_x*lu_x

l_y=np_y*lu_x

```python
>>> import numpy
>>> domain_lu_x=0.0001
>>> domain_np_y=10
>>> domain_np_x=6
>>> domain_l_x=domain_np_x*domain_lu_x
>>> domain_l_y=domain_np_y*domain_lu_x
>>> X,Y=numpy.meshgrid(numpy.arange(0, domain_l_x, domain_lu_x),numpy.arange(0, domain_l_y, domain_lu_x))
>>> print(X)
[[ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]
 [ 0.      0.0001  0.0002  0.0003  0.0004  0.0005]]
>>> print(Y)
[[ 0.      0.      0.      0.      0.      0.    ]
 [ 0.0001  0.0001  0.0001  0.0001  0.0001  0.0001]
 [ 0.0002  0.0002  0.0002  0.0002  0.0002  0.0002]
 [ 0.0003  0.0003  0.0003  0.0003  0.0003  0.0003]
 [ 0.0004  0.0004  0.0004  0.0004  0.0004  0.0004]
 [ 0.0005  0.0005  0.0005  0.0005  0.0005  0.0005]
 [ 0.0006  0.0006  0.0006  0.0006  0.0006  0.0006]
 [ 0.0007  0.0007  0.0007  0.0007  0.0007  0.0007]
 [ 0.0008  0.0008  0.0008  0.0008  0.0008  0.0008]
 [ 0.0009  0.0009  0.0009  0.0009  0.0009  0.0009]]
```

*end of example*

```python
	# lattice domain velocity
	bnd_ux = domain.velocity_lu
	print(" === Beginning iteration loop ... ")
```

$U^*$

```python
	for iteration in range(inputfile.dico['max_iter']):
        # Compute macroscopic density from f_tmp. 调用domain中的方法
        domain.density=domain.compute_density(lattice,domain.f_tmp,explicit=False)
        # Compute macroscopic velocity from f_tmp. 调用domain中的方法
        domain.velocity=domain.compute_velocity(lattice,domain.f_tmp,explicit=False)
```

```python
        # Filtering
    	# 以下三行被注释 未了解
        #domain.density = gaussian_filter(domain.density, 0.1)
        #domain.velocity[0, :, :] = gaussian_filter(domain.velocity[0, :, :], 0.5)
        #domain.velocity[1, :, :] = gaussian_filter(domain.velocity[1, :, :], 0.5)
        
		# 调用函数compute_equilibrium_function()
        # Compute the equilibrium distribution function.
        domain.feq=compute_equilibrium_function(domain,lattice,
                                                domain.density,domain.velocity)
```

```python
        # Collision of the particles from f_tmp and f_eq
        domain.f_n = collision(domain, lattice)
```

`time_loop()` 再调用函数 `collision(domain, lattice)`

```python
def collision(domain, lattice):
	""" Perform D2Q9 collision """
	
	# Collision of the particles everywhere eq16
	domain.f_n = domain.f_tmp - (domain.f_tmp - domain.feq) / domain.tau

	# Collision with the Solid part 这部分已经算好 找到在哪块
	# Bounceback boundary (see p44) 
	for i_dir in range(lattice.dim_q):
    	domain.f_n[i_dir,domain.solid]=domain.f_tmp
                              [lattice.bnd_noslip[i_dir],domain.solid]
	return domain.f_n
```

$$\underbrace{ f_a (\vec x + \vec e_a \Delta t, t+\Delta t) = f_a(\vec x, t) }_{Streaming} - \underbrace{\frac{[f_a(\vec x,t)- f_a^{eq}(\vec x,t)]}{\tau} }_{Collision}(16)$$ 

*end of collision()*

```python
        # Streaming of f_n into f_tmp.
        # (Applying periodicity everywhere)
        domain.f_tmp = streaming(domain, lattice, domain.f_n)
```

 `time_loop()` 再调用`streaming(domain,lattice,domain.f_n)`

```python
def streaming(domain, lattice, f_in, explicit=False):
""" Streaming via the distribution function.
    Direction specific densities are moved to
    the nearest neighbor lattice nodes.
    see p36
    Periodicity is applied at all the boundaries.
"""
    # 初始化
	f_out = numpy.zeros((lattice.dim_q, domain.np_x, domain.np_y))

	if explicit:
    	# Here is the explicit formulation
    	# See p36-37
    	for i_x in range(domain.np_x):
        	for i_y in range(domain.np_y):
            	for i_dir in range(lattice.dim_q):
                    # eq16 part of streaming in x 
                	i_xn = i_x + lattice.directions[i_dir, 0]
                	# Periodicity along x ?
                	if i_xn < 0:
                    	i_xn = domain.np_x - 1
                	elif i_xn >= domain.np_x:
                    	i_xn = 0
                    # eq16 part of streaming in y
                	i_yn = i_y + lattice.directions[i_dir, 1]
                	# Periodicity along y
                	if i_yn < 0:
                    	i_yn = domain.np_y - 1
                	elif i_yn >= domain.np_y:
                    	i_yn = 0
                    # Streaming ?
                	f_out[i_dir, i_xn, i_yn] = f_in[i_dir, i_x, i_y]
	else:
    	# It can be done much faster inline with numpy:
    	for i_dir in range(lattice.dim_q):
        	# numpy.roll(a, shift, axis)
        	# Roll array elements along a given axis. Elements that roll beyond the 
        	# last position are re-introduced at the first (ensuring periodicty).
        	# shift: The number of places by which elements are shifted. If a tuple, 
        	# then axis must be a tuple of the same size, and each of the given axes 
        	# is shifted by the corresponding number. If an int while axis is a tuple 
        	# of ints, then the same value is used for all given axes.
        	f_out[i_dir,:,:]=numpy.roll(numpy.roll(f_in[i_dir,:,:],
                                        lattice.directions[i_dir,0],axis=0),
                                        lattice.directions[i_dir, 1],axis=1)
	return f_out
```

*end of streaming()*

```python
		domain.f_tmp = apply_boundaries(domain, lattice, bnd_ux)
```

 `time_loop` 再调用 `apply_boundaries()`  关于边界的相关知识 需要再细看

```python
def apply_boundaries(domain, lattice, bnd_ux):
    """ Initialize the domain
        Initialize rho, u, fi, fi_eq
    """
    # 左边边界
    # link-Wise approach
    # 1 周期 该边界已经在前述代码中构建 
    if domain.bnd_left == "periodic":
       pass # Nothing to do
    # 2 bounce-back (BB) belong to link-wise scheme?
    elif domain.bnd_left == "wall_noslip":
       # bounce back
       for i_dir in range(lattice.dim_q):
            # lattice.bnd_noslip 已经在domain中定义？
           domain.f_tmp[i_dir, 0, :] = domain.f_n[lattice.bnd_noslip[i_dir], 0, :]
        
    elif domain.bnd_left == "wall_slip":
       #  symmetry bounce
       for i_dir in range(lattice.dim_q):
            # lattice.bnd_noslip 已经在domain中定义？
           domain.f_tmp[i_dir, 0, :] = domain.f_n[lattice.bnd_slip_x[i_dir], 0, :]
        
    #  Wet-Node Approach    
    elif domain.bnd_left == "inlet_neq":
       # Non equilibrium extrapolation method NEEM wet-node techniques
       # p 79 book mohamad
       bnd_uy = 0
        # p77 book mohamad eq(5.22)
       domain.density[0,:]=((domain.f_n[2,0,:]+domain.f_n[0,0,:]+domain.f_n[4, 0, :]            +2.*(domain.f_n[6,0,:]+domain.f_n[3,0,:]+domain.f_n[7,0,:]))/(1.-bnd_ux))
       # eq(5.40)
       domain.f_tmp[1,0,:]=domain.f_n[3,0,:]+2./3.*domain.density[0,:]*bnd_ux
       # eq(5.41)
       domain.f_tmp[5,0,:]=domain.f_n[7,0,:]+domain.feq[5, 0, :]-domain.feq[7,0,:]
       # eq(5.42)
       domain.f_tmp[8,0,:]=domain.f_n[6,0,:]+domain.feq[8,0,:]-domain.feq[6,0,:]
    
    elif domain.bnd_left == "inlet_eq":
       # Equilibrium bc inlet ES
       # Recompute density and a new equ
       # p191 book sukop
       bnd_uy = 0
       domain.density[0,:]=((domain.f_n[2,0,:]+domain.f_n[0,0,:]+domain.f_n[4,0,:]+
             2.*(domain.f_n[6,0,:]+domain.f_n[3,0,:]+domain.f_n[7,0,:]))/(1.-bnd_ux))
    
       domain.feq=compute_equilibrium_function(domain,lattice,domain.density,
                                               domain.init_velocity)
        # 
       domain.f_tmp[5, 0, :] = domain.feq[5, 0, :]
       domain.f_tmp[1, 0, :] = domain.feq[1, 0, :]
       domain.f_tmp[8, 0, :] = domain.feq[8, 0, :]
    
    elif domain.bnd_left == "inlet":
       # Non equilibrium bouce back
       # Zhu He
       # p77 book Mohamad
       # p198 book Sukop

       bnd_uy = 0
       domain.density[0,:]=((domain.f_n[2,0,:]+domain.f_n[0,0,:]+domain.f_n[4,0,:]+
           2.*(domain.f_n[6, 0, :]+domain.f_n[3,0,:]+domain.f_n[7,0,:]))/(1.-bnd_ux))
        # eq5.23
       domain.f_tmp[5,0,:]=(domain.f_n[7,0,:]+(1./6.)*domain.density[0,:]*bnd_ux)
       domain.f_tmp[1,0,:]=domain.f_n[3,0,:]+2./3.*domain.density[0,:]*bnd_ux
       domain.f_tmp[8,0,:]=(domain.f_n[6,0,:]+(1./6.)*domain.density[0,:]*bnd_ux)
      
```

**example**



![](http://opy4d6q9k.bkt.clouddn.com/1114.png)

Wet-Node Approach:

Finding the Density on Boundaries

for left boundaries

$$\rho _w = \sum \limits _i f_i=f_0 +f_1 +f_2+f_3+f_4+f_5+f_6+f_7+f_8$$

$$\rho _w \vec u=c(f_3 +f_6 +f_7)- c(f_1 +f_5 +f_8)$$ 

combining the two equations above 

$$\rho _w=\frac{c}{c+u_x} [f_0 +f_2 +f_4 +2(f_3 +f_6 +f_7)]$$

Non equilibrium extrapolation method

$$f_i(\vec x_b,t) =f_i^{eq}(\rho _w, \vec u_w)+(f_i(\vec x _f,t)-f_i^{eq}(\rho _f,\vec u_f))$$

![](http://opy4d6q9k.bkt.clouddn.com/1115.png)



$$f_w=f_\text f+(f_w^{eq} - f_\text f^{eq})$$

$$f_1=f_3 + (f_1^{eq} - f_3^{eq})$$  equal to  $$f_1=f_3 + \frac{2}{3}\rho _w u_w$$

$$f_5 = f_7 +(f_5 ^{eq} - f_7^{eq})$$

$$f_8 = f_6 +(f_8 ^{eq} - f_6^{eq})$$



Equilibrium Scheme

$$f_i(\vec x_b,t) =f_i^{eq}(\rho _w, \vec u_w)$$

$$f_1=f_1^{eq} $$ 

$$f_5 = f_5 ^{eq} $$

$$f_8 = f_8 ^{eq} $$

Non-equilibrium Bounce-Back Method

$$f_{\bar i}^{neq}(\vec x_b,t)=f_i^{neq}(\vec x_b,t) $$    $$(\vec c_{\bar i}=- \vec c_i)$$

$$f_1=f_3 + \frac{2}{3}\rho _w u_w$$

$$f_5 = f_7-\frac{1}{2}(f_2 - f_4) + \frac{1}{6} \rho _w u_{wx} + \frac{1}{2} \rho _w u_{wy}$$

 $$f_8 = f_6-\frac{1}{2}(f_2 - f_4) + \frac{1}{6} \rho _w u_{wx} - \frac{1}{2} \rho _w u_{wy}$$

for $u_{wy}=0$ 故 右边第二项和最后一项为零。

*end of example*

```python
# 右边边界
    if domain.bnd_right == "periodic":
       pass # Nothing to do
    elif domain.bnd_right == "wall_noslip":
       for i_dir in range(lattice.dim_q):
           domain.f_tmp[i_dir, -1, :] = domain.f_n[lattice.bnd_noslip[i_dir], -1, :]
    elif domain.bnd_right == "wall_slip":
       for i_dir in range(lattice.dim_q):
           domain.f_tmp[i_dir, -1, :] = domain.f_n[lattice.bnd_slip_x[i_dir], -1, :]
            
    elif domain.bnd_right == "outlet":
       # Open imposed density boundary
       # p79 mohamad
       rho_outlet = 1.0
       domain.velocity[0,-1,:]=((domain.f_n[2,-1,:]+domain.f_n[0,-1,:]+
               domain.f_n[4, -1,:]+2.*(domain.f_n[5,-1,:]+
               domain.f_n[1,-1,:]+domain.f_n[8, -1, :]))/rho_outlet- 1.)
       rhoux_out = rho_outlet * domain.velocity[0, -1, :]
       domain.f_tmp[6, -1, :] = domain.f_n[8, -1, :] - 1. / 6. * rhoux_out
       domain.f_tmp[3, -1, :] = domain.f_n[1, -1, :] - 2. / 3. * rhoux_out
       domain.f_tmp[7, -1, :] = domain.f_n[5, -1, :] - 1. / 6. * rhoux_out
        
    elif domain.bnd_right == "outlet_simple":
       # Simple extrapolation at the outlet
       # p79 mohamad
       domain.f_tmp[6, -1, :] = 2. * domain.f_tmp[6, -2, :] - domain.f_tmp[6, -3, :]
       domain.f_tmp[3, -1, :] = 2. * domain.f_tmp[3, -2, :] - domain.f_tmp[3, -3, :]
       domain.f_tmp[7, -1, :] = 2. * domain.f_tmp[7, -2, :] - domain.f_tmp[7, -3, :]

```

**example**

右边界，The outlet velocity is not known

Assume that the pressure at the boundary is known, The density at the boundary set to a constant (1 in the code)

$$u_x=\frac{f_0 + f_2 +f_4 +2(f_1+f_5+f_8)}{\rho _{outlet}} -1$$

$$f_3 = f_1 - \frac{2}{3} \rho _{outlet} u_x$$

$$f_6 = f_8 -\frac {1}{2}(f_2 - f_4) -\frac{1}{6} \rho_{outlet} u_x$$

$$f_7 = f_5 -\frac {1}{2}(f_2 - f_4) -\frac{1}{6} \rho_{outlet} u_x$$

Use extrapolation for the unknown distribution functions

$$f_{3,n} = 2 \cdot f_{3,n-1} - f_{3,n-2}$$

$$f_{6,n} = 2 \cdot f_{6,n-1} - f_{6,n-2}$$

$$f_{7,n} = 2 \cdot f_{7,n-1} - f_{7,n-2}$$

*end of example*

```python
  # 底部边界
    if domain.bnd_bottom == "periodic":
       pass # Nothing to do
    elif domain.bnd_bottom == "wall_noslip":
       for i_dir in range(lattice.dim_q):
           domain.f_tmp[i_dir, :, 0] = domain.f_n[lattice.bnd_noslip[i_dir], :, 0]
    elif domain.bnd_bottom == "wall_slip":
       for i_dir in range(lattice.dim_q):
           domain.f_tmp[i_dir, :, 0] = domain.f_n[lattice.bnd_slip_x[i_dir], :, 0]
  # 上部边界
    if domain.bnd_up == "periodic":
       pass # Nothing to do
    elif domain.bnd_up == "wall_noslip":
       for i_dir in range(lattice.dim_q):
           domain.f_tmp[i_dir, :, -1] = domain.f_n[lattice.bnd_noslip[i_dir], :, -1]
    elif domain.bnd_up == "wall_slip":
       for i_dir in range(lattice.dim_q):
           domain.f_tmp[i_dir, :, -1] = domain.f_n[lattice.bnd_slip_x[i_dir], :, -1]

    return domain.f_tmp
```

*end of apply_boundaries()*

```python

        # Information
        # 位数6 小数点后精度3 float类型 str.format() 函数
        it_str = "{:6.3f}".format(iteration*domain.delta_t)
        # 这里的 % 为取余运算
        if iteration % inputfile.dico['postproc_info_niter'] == 0:
            print("- it = " + str(iteration).zfill(6) + " - t [s] =" + it_str)
            
        # Visu output
        if iteration % inputfile.dico['postproc_dump_niter'] == 0:
            # 输出流速图
            if inputfile.dico['postproc_vel_mag']:
                # 清理当前的figures
                plt.clf() 
                # Display an array as a matrix 
                # cmap: colormap,optional,default:None
                # vmin,vmax: scalar, optional,default:None
                # numpy.ma 掩码数组 将无流动区域mask
                plt.matshow(numpy.ma.masked_array(domain.c_vel*numpy.sqrt
                    (domain.velocity[0]**2 + domain.velocity[1]**2).transpose(),                          mask=domain.solid.transpose()),cmap="viridis", vmin=0, 
                     vmax=domain.c_vel*bnd_ux*4.)
                # 显示 colorbar
                plt.colorbar()
                # 子标题
                plt.suptitle('Velocity[m/s]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                # 文件命名格式
                output_filename = "output/vel." + str(iteration).zfill(6) + ".png"
                # 保存文件
                plt.savefig(output_filename)
                # 关闭图
                plt.close()
                
                # 密度图
            if inputfile.dico['postproc_density']:
                plt.clf()
                plt.matshow(numpy.ma.masked_array(domain.density.transpose(), 
                            cmap="viridis", mask=domain.solid.transpose()))
                plt.colorbar()
                plt.suptitle('Density[-]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                output_filename = "output/rho." + str(iteration).zfill(6) + ".png"
                plt.savefig(output_filename)
                plt.close()
                
            if inputfile.dico['postproc_vel_ux']:
                plt.clf()
                plt.matshow(numpy.ma.masked_array(domain.c_vel*
                             domain.velocity[0].transpose(), 
                             mask=domain.solid.transpose()), cmap="viridis")
                plt.colorbar()
                plt.suptitle('U_x[m/s]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                plt.savefig("output/ux." + str(iteration).zfill(6) + ".png")
                plt.close()
                
            if inputfile.dico['postproc_vel_uy']:
                plt.clf()
                plt.matshow(numpy.ma.masked_array(
                    domain.c_vel*domain.velocity[1].transpose(),
                    mask=domain.solid.transpose()), cmap="viridis")
                plt.colorbar()
                plt.suptitle('U_y[m/s]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                plt.savefig("output/uy." + str(iteration).zfill(6) + ".png")
                plt.close()
                
            if inputfile.dico['postproc_force_x']:
                plt.clf()
                plt.matshow(numpy.ma.masked_array(domain.c_vel*
                         domain.cohesion_force[0].transpose(), 
                         mask=domain.solid.transpose()), cmap="viridis")
                plt.colorbar()
                plt.suptitle('F[-]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                plt.savefig("output/fx." + str(iteration).zfill(6) + ".png")
                plt.close()
                
            if inputfile.dico['postproc_force_y']:
                plt.clf()
                plt.matshow(numpy.ma.masked_array(domain.c_vel*
                            domain.cohesion_force[1].transpose(), 
                            mask=domain.solid.transpose()), cmap="viridis")
                plt.colorbar()
                plt.suptitle('F_y[-]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                plt.savefig("output/fy." + str(iteration).zfill(6) + ".png")
                plt.close()
                
            if inputfile.dico['postproc_vorticity']:
                domain.vorticity = numpy.zeros((domain.np_x, domain.np_y))
                for i_y in range(0, domain.np_y-1):
                    for i_x in range(0, domain.np_x-1):
                        domain.vorticity[i_x, i_y] = 
                       ((domain.velocity[1, i_x+1, i_y] -
                        domain.velocity[1, i_x, i_y]) -
                        (domain.velocity[0, i_x, i_y + 1] -
                         domain.velocity[0, i_x, i_y]))
                plt.clf()
                vortmax = domain.velocity_lu/2.
                plt.matshow(numpy.ma.masked_array(
                         domain.vorticity.transpose(), cmap="viridis",                                      mask=domain.solid.transpose()),vmin=-vortmax, vmax=vortmax)
                plt.suptitle('Vorticity[m2/s]-it'+str(iteration).zfill(6)+'-t[s]'+it_str)
                plt.colorbar()
                plt.savefig("output/vorticity." + str(iteration).zfill(6) + ".png")
                plt.close()
```

*end of time_loop()*