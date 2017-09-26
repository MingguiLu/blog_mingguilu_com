---
date: 2017-03-12T13:06:28+08:00
description: 
tags: 
- Python
- Python3
- read()
- readline()
- readlines()
title: Python3从文件中读取数据
categories: 技术分享
photos: /images/photos/python.png
---

Python3有三种从文件中读取数据的方法：

* read()　读取整个文件内容；占用内存多  
* readline()　每次读取并返回一行数据；读取速度慢，每次占用内存少  
* readlines()　读取每一行数据，并作为元素存储在一个列表中  

下面我将圆周率π的部分数值拆分为三行存储在文件`pi_digits.txt`中，然后使用python读取文件并拼接为完整的`π`：3.141592653589793238462643383279，注意得到结果是字符串，并不是数字

使用`.read()`读取文件`pi_digits.txt`的全部内容

	with open('pi_digits.txt') as file_object:
	    '''打开文件'''
	    print(file_object.read())
	    '''打印文件内容'''
	   
打印结果：

	3.1415926535
	  8979323846
	  2643383279

<!--more-->	  
	  
#### 方法一：.read()

	with open('pi_digits.txt') as file_object:
	    '''打开文件'''
	    pi = file_object.read()
	    '''读取文件内容并赋给变量content'''
	    print(pi.replace("\n","").replace(" ",""))
	    '''使用.replace()将每行末尾的换行符和空格，替换为空，打印拼接完成的π'''

#### 方法二：逐行读取

	with open('pi_digits.txt') as file_object:
	    '''打开文件'''
	    pi = ''
	    '''初始化变量pi'''
	    for line in file_object:
			'''逐行读取文件的每一行内容'''
			pi += line.strip()
			'''剔除元素首尾的空字符后做字符串拼接，并赋给pi'''
			# pi += "".join(line.strip())
	    print(pi)
	    '''打印拼接完成的π'''

#### 方法三：.readline()

	with open('pi_digits.txt') as file_object:
	    '''打开文件'''
	    pi = ''
	    '''初始化变量pi'''
	    while True:
			'''???'''
			line = file_object.readline()
			'''读取一行内容赋给变量line'''
			if line:
			    '''如果读取到一行数据'''
			    #print(line)
			    pi += line.strip()
			    '''剔除这行数据两端的空字符之后拼接并赋给pi'''
			else:
			    '''如果读取内容为空'''
			    break
			    '''退出循环'''
	     print(pi)
	     '''打印拼接完成的π'''	

#### 方法四：.readlines()

	with open('pi_digits.txt') as file_object:
	    '''打开文件'''
	    lines = file_object.readlines()
	    '''读取文件的每行作为元素存入列表'''
	    print(lines)
	    '''打印列表'''
	    pi = ''
	    '''初始化变量pi'''
	    for line in lines:
			'''遍历列表的元素'''
			pi += line.strip()
			'''剔除元素首尾的空字符后做字符串拼接，并赋给pi'''
	    print(pi)
	    '''打印拼接完成的π'''

#### 方法五：.read().split()

	with open('pi_digits.txt') as file_object:
	    '''打开文件'''
	    lines = file_object.read().split()
	    '''将文件分割为字符串列表，相对于.readlines(),这种方法在分割内容时，就剔除了空字符'''
	    print(lines)
	    '''打印列表'''
	    pi = ''
	    '''初始化变量pi，用于表示完整的π'''
	    for line in lines:
			'''遍历列表的元素'''
			pi += line
			'''将每个元素做字符串拼接，并赋给pi'''
	    print(pi)
	    '''打印拼接完成的π''
	    
