---
title: A script for plot
date: 2018-1-31 22:49:53
categories: Research
tags: 
   - python
mathjax: true
---
一个读取txt文件和excel文件作图的脚本
<!--  more -->
```python
import os
import numpy
import matplotlib
import matplotlib.pyplot as plt
from itertools import islice
import openpyxl
'''write by zzk 18.1.31'''


# txt文件名
file_name_lis1=['a1-10.TXT','a2-9.TXT','a3-7.TXT','a3-10.TXT',
               'A4.TXT','a4-9.TXT','a5-10.TXT','A6.TXT']
# Excel 文件名
file_name_lis2=['a1-10.xlsx','a2-9.xlsx','a3-7.xlsx','a3-10.xlsx',
               'A4.xlsx','a4-9.xlsx','a5-10.xlsx','A6.xlsx']
# 
file_dic_x = {'a1-10.TXT':[],'a2-9.TXT':[],'a3-7.TXT':[],'a3-10.TXT':[],
		'A4.TXT':[],'a4-9.TXT':[],'a5-10.TXT':[],'A6.TXT':[]}

file_dic_y = {'a1-10.TXT':[],'a2-9.TXT':[],'a3-7.TXT':[],'a3-10.TXT':[],
		'A4.TXT':[],'a4-9.TXT':[],'a5-10.TXT':[],'A6.TXT':[]}

file_dic_y_excel = {'a1-10.xlsx':[],'a2-9.xlsx':[],'a3-7.xlsx':[],'a3-10.xlsx':[],
		'A4.xlsx':[],'a4-9.xlsx':[],'a5-10.xlsx':[],'A6.xlsx':[]}

# 从 EXCEL 文件所在文件夹 读取EXCEL文件内容
os.chdir('E:\Program Files\\Sublime Text 3\\zzk-learning\\drawdown')
for file_name in file_name_lis2:
	y=[]
	wb= openpyxl.load_workbook(file_name)
	sheet=wb.get_sheet_by_name('Sheet1')
	for row in range(1,140):
		y_tmp=sheet['B'+str(row)].value
		y.append(y_tmp)
	file_dic_y_excel[file_name]=numpy.array(y)



# 从 txt 文件所在文件夹 读取txt文件内容
os.chdir('E:\Program Files\\Sublime Text 3\\zzk-learning\\simulation')
for file_name in file_name_lis1:
	in_file = open(file_name,'r')
	x=[]
	y=[]
	
	for line in islice(in_file,1,None):
		
		file_line = in_file.readline()
		# 将整行数据分割处理
		x_tmp,y_tmp = [float(i) for i in file_line.split()]
		# 添加新读取的数据
		x.append(x_tmp)
		y.append(y_tmp)
	in_file.close()
	for a in y:
		if a<1.43e-10:
			b=y.index(a)
			x.remove(x[b])
			y.remove(a)
	
	file_dic_x[file_name]=numpy.array(x)
	file_dic_y[file_name]=numpy.array(y)*100
	

# 绘图
plt.figure(figsize=(12,6))

plt.subplots_adjust(top=0.92,left=0.07,right=0.97,hspace=0.3,wspace=0.3)
i=0
for file_name in file_name_lis1:
	file_name2 = file_name.replace('TXT','xlsx')
	file_name3 = file_name.rstrip('.TXT')
	i=i+1


	plt.subplot(2,4,i)
	#fig,ax=plt.subplots(num=i,figsize=(2,2))
	plt.plot(file_dic_x[file_name],file_dic_y[file_name])
	x=numpy.arange(1,140,1)
	plt.plot(x,file_dic_y_excel[file_name2])
	plt.title(file_name3)

plt.show()

#plt.savefig('example.jpg')
```