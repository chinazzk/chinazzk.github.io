---
title: 关于numpy包中数组(array)的操作
date: 2018-1-12 22:49:53
categories: Research
tags: 
   - python
mathjax: true
---
关于数组的一些“芝士”
<!--  more -->
**矩阵(matrix)**
矩阵更多的是一个数学上的概念，看维基百科的解释
In mathematics, a **matrix** is a rectangular **array** of numbers, symbols, or expressions, arranged in rows and columns.如下 $ 2 \times 3 $ 2行(rows)3列(columns)

$$ \left[\begin{matrix} 1 & 2 & 3 \\ 4 & 5 & 6 \end{matrix}\right] $$

The size of a matrix is defined by the number of rows and columns that it contains. A matrix with** m rows** and **n columns** is called an **m × n** matrix or m-by-n matrix, while m and n are called its **dimensions**. 
Matrices which have a single row are called **row vectors**, and those which have a single column are called **column vectors**.  In some contexts, such as computer algebra programs, it is useful to consider a matrix with no rows or no columns, called an empty matrix. The individual items in an **m × n** matrix A, often denoted by $ \mathbf{a_{i,j}} $, where max i = m and max j = n, are called its **elements or entries**.
**数组(array)**
主要是计算机中的一种数据结构与矩阵基本是等价的Common name for matrix 
**In python**
列表list就相当于数组，但不同的是list可以保存各种对象，list中的保存的是对象的指针。
数组不是python中内置的标配数据结构，但可以通过numpy包在python中使用数组结构
numpy matrix 必须是二维的， 但numpy array可以是多维的，matrix是array的一个分支
#### 列表(list)
list是Python中最基本的数据结构。也即是数组结构
序列中的每个元素都分配一个数字 - 它的位置，或索引，第一个索引是0，第二个索引是1，依此类推。
Python有6个序列的内置类型，但最常见的是列表和元组。
序列都可以进行的操作包括索引，切片，加，乘，检查成员。
Python已经内置确定序列的长度以及确定最大和最小的元素的方法。
列表作为一个方括号内的逗号分隔值出现。
列表的数据项不需要具有相同的类型
创建一个列表，只要把逗号分隔的不同的数据项使用方括号括起来即可。
```python
list1=['a','b',1,2]
print list1[0]
```
#### 数组(arrary)
在numpy中通过array定义数组
```python
from numpy import *
c = array([(x,y) for x in [0,-1,1] for y in [0,-1,1]])
>>>print(c)
[[ 0  0]
 [ 0 -1]
 [ 0  1]
 [-1  0]
 [-1 -1]
 [-1  1]
 [ 1  0]
 [ 1 -1]
 [ 1  1]]
```
```python
>>>print(list(c))
[array([0, 0]), array([ 0, -1]), array([0, 1]), array([-1,  0]), array([-1, -1]), array([-1,  1]), array([1, 0]), array([ 1, -1]), array([1, 1])]
```
```python
>>>print(c.tolist())
[[0, 0], [0, -1], [0, 1], [-1, 0], [-1, -1], [-1, 1], [1, 0], [1, -1], [1, 1]]
```
关于transpose()函数
```python
>>>print(array([[[0,1,2,3],[4,5,6,7]],[[8,9,10,11],[12,13,14,15]]]))
[[[ 0  1  2  3]
  [ 4  5  6  7]]

 [[ 8  9 10 11]
  [12 13 14 15]]]
>>>print(arr1.transpose(1,0,2))
[[[ 0  1  2  3]
  [ 8  9 10 11]]

 [[ 4  5  6  7]
  [12 13 14 15]]]
>>>print(arr1.transpose(0,2,1))
[[[ 0  4]
  [ 1  5]
  [ 2  6]
  [ 3  7]]

 [[ 8 12]
  [ 9 13]
  [10 14]
  [11 15]]]
>>>print(arr1.transpose(2,1,0))
[[[ 0  8]
  [ 4 12]]

 [[ 1  9]
  [ 5 13]]

 [[ 2 10]
  [ 6 14]]

 [[ 3 11]
  [ 7 15]]]
```
arr1是2\*2\*4维数组；transpose(0,1,2)中的0,1,2分别表示三种维度(2,2,4)

transpose(1,0,2)表示原第一维度与原第二维度**转置**第三维度保持不变
![1](http://opy4d6q9k.bkt.clouddn.com/%E6%8D%95%E8%8E%B71.PNG)
transpose(0,2,1)表示原第二维度与原第三维度**转置**第一维度保持不变
![2](http://opy4d6q9k.bkt.clouddn.com/%E6%8D%95%E8%8E%B72.PNG)
transpose(2,1,0)表示原第一维度与原第三维度**转置**第二维度保持不变
![3](http://opy4d6q9k.bkt.clouddn.com/%E6%8D%95%E8%8E%B73.PNG)

