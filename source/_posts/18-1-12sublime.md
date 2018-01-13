---
title: Sublime text 搭建Python IDE
date: 2018-1-12 22:49:53
categories: Research
tags: 
   - python
   - sublime text
---
用sublime text 搭建python的轻量IDE 
<!--  more -->
**1下载安装sublime text 3 **
安装后，在菜单栏help>enter License 输入如下信息(for Build 3143)
```go
—– BEGIN LICENSE —–
TwitterInc
200 User License
EA7E-890007
1D77F72E 390CDD93 4DCBA022 FAF60790
61AA12C0 A37081C5 D0316412 4584D136
94D7F7D4 95BC8C1C 527DA828 560BB037
D1EDDD8C AE7B379F 50C9D69D B35179EF
2FE898C4 8E4277A8 555CE714 E1FB0E43
D5D52613 C3D12E98 BC49967F 7652EED2
9D2D2E61 67610860 6D338B72 5CF95C69
E36B85CC 84991F19 7575D828 470A92AB
—— END LICENSE ——
```
**2安装python3 (Miniconda)**
**3设置python3编译环境**
在sublime text 菜单栏 Tools>Build System> New Build System
打开文件untitled.sublime-build 输入以下内容
```python
{
"encoding": "utf-8",  
"working_dir": "$file_path",  
"shell_cmd": "E:\\Users\\hp\\Miniconda3\\python.exe -u \"$file\"",  
"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",  
"selector": "source.python" 
}
```
注意*E:\\Users\\hp\\Miniconda3\\python.exe*为python安装路径
保存为名称python3.sublime-build
*我的保存路径为C:\Users\hp\AppData\Roaming\Sublime Text 3\Packages\User*
重启sublime text 在菜单栏Tools>Build System>python3 选择python3(打钩)
**test**
在sublime text 新建文件 输入
```python
print ('hello')
```
ctrl + s 保存 后 Ctrl + b 运行
**安装package control**
可以通过官网 https://packagecontrol.io/installation#st3 的提示进行安装
sublime text 菜单栏 View>Show Console 输入如下代码
```python
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
安装成功后 在菜单栏 preferences 下会有 package control
**安装sublimeREPL**
打开package control 选择 install package 回车 并输入 SublimeREPL 安装
安装完成打开Preferences>package settings>SublimeREPL>Setting-User
如有需要输入如下内容
```python
{
	"default_extend_env": {"PATH":"{PATH};E:\\Users\\hp\\Miniconda3\\"}
}
```
注意*E:\\Users\\hp\\Miniconda3\\*为安装路径
**test**
建立带有input()功能的脚本
```python
a=input("s:")
b=int(a)
if b > 20:
    print('a')
else:
    print('b')
```
然后tools>SublimeREPL>python>python -run current file
在新页面中输入数字并回车
**设置快捷键**
打开preferences>key bindings 输入
```python
[
{"keys" : ["f4"],  
"caption": "SublimeREPL: Python ",  
"command": "run_existing_window_command", "args": {"id": "repl_python",  
"file": "config/Python/Main.sublime-menu"}},

{"keys" : ["f5"],  
"caption": "SublimeREPL: Python - RUN current file",  
"command": "run_existing_window_command", "args": {"id": "repl_python_run",  
"file": "config/Python/Main.sublime-menu"}}
]
```
注意F4为运行python console F5为执行脚本
到此基本完成