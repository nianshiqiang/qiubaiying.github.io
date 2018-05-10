---
layout:     post
title:     Pandas中Series操作
subtitle:   pandas Series
date:       2018-05-09
author:     粘世强
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
        - python
        - pandas
---

## Pandas中Series操作


pandas是一个专门用于数据分析的python工具库

### Pandas简介
* python数据分析处理的一个package
* 基于numpy(对“矩阵”做科学计算)
* 有一种用python去操作Excel/SQL的感觉

Pandas中的数据结构主要有两种，一是Series，二是DataFrame。可以先直观的把Series理解为一维的数组，把Pandas理解为二位数组（类似excel表格，由行和列组成）。

我们首先来看一下Series。

### 一：数据结构Series


```python
#导入包，np和pd是约定俗称的缩写
import numpy as np
import pandas as pd
```

#### 构造和初始化series

初始化一个Series对象，可以将列表传入，进行初始化。


```python
s = pd.Series([7, 'Beijing', 3.14, -12345, 'HanXiaoyang'])
```
我们可以查看一下s是什么样子的


```python
0              7
1        Beijing
2           3.14
3         -12345
4    HanXiaoyang
dtype: object
```

可以看到好像输出了两列？咦？Series不是一维数组吗？？

**划重点啦！！！**

首先Series是一维数组，这一点是没有任何错误的。上边看到的左边0，1，2，3，4代表的是元素的索引号，并不是元素的内容，第二列的内容才是真正的Series。

比如我们来查看一下s中的第2个元素：

一定要注意python中的索引都是从0开始的哦!

```python
s[1]
'Beijing'
```

说到这里感觉这个Series和列表好像也没有什么区别呀，为什么还要这么麻烦搞一个Series呢?

接下来我们就会看到Series比列表厉害的功能了。

pandas会默认用0到n作为Series的index，但是我们也可以自己指定index。index可以类比理解为dic当中的key。


```python
s = pd.Series([7, 'Beijing', 3.14, -12345, 'HanXiaoyang'], index=['A', 'B', 'C', 'D', 'E'])
```

上面这行代码中，我们指定了索引值为'A', 'B', 'C', 'D', 'E'，我们再来查看一下s，可以看到索引值都已经变了。


```python
s
A              7
B        Beijing
C           3.14
D         -12345
E    HanXiaoyang
dtype: object
```

这时候可以按照索引的名字，来进行索引了。比如：我们想要找到索引值为“A”的元素的值。


```python
s['A']
7
```

再比如下边这个，索引取出特定的值。


```python
s[['A','D','B'] ]：

A          7
D     -12345
B    Beijing
dtype: object
```

有爱思考的同学可能回想，这也没什么厉害的嘛！我也可以按照数字来进行索引呀。可是当这个Series元素由成百上千个的时候，万一我们不知道要找的元素在第几个位置，直知道它的索引名称呢？

除了上边这种用list来构建Series，同时指定index的方法，我们还可以利用字典来构建Series，字典本身就是key_value的组合呀。


```python
#定义一个字典
cities = {'Beijing':55000, 'ShangHai':60000, 'Shenzhen':50000, 'Hangzhou':30000, 'Guangzhou':40000, 'Suzhou':None}
```

接下来我们利用这个字典来构建一个Series.


```python
apt = pd.Series(cities, name='income')
```

查看一下结果：


    apt:
    Beijing      55000.0
    Guangzhou    40000.0
    Hangzhou     30000.0
    ShangHai     60000.0
    Shenzhen     50000.0
    Suzhou           NaN
    Name: income, dtype: float64

这时候自然而然的就可以利用索引名称来进行索引了。


```python
# 索引
apt['Guangzhou']
```


    40000.0
同时，也可以利用数字来索引，比如：

```python
apt[1]
```


    40000.0
索引，从上边的例子来看，即使我们给索引起了名字，我们依旧可以利用数字来进行索引，功能是不是很强大？

## Series 索引

接下来，看一些稍显复杂一点的索引：索引规则和字符串，列表的规则是一致的。记住四个字“左闭右开”即可熟练掌握。

```python
#取出第1个位置到最后一个位置的值
apt[1:]
```


```python
Guangzhou    40000.0
Hangzhou     30000.0
ShangHai     60000.0
Shenzhen     50000.0
Suzhou           NaN
Name: income, dtype: float64
```


```python
#取出从开始到最后一个位置的值，但是不包括最后一个
apt[:-1]
```


    Beijing      55000.0
    Guangzhou    40000.0
    Hangzhou     30000.0
    ShangHai     60000.0
    Shenzhen     50000.0
    Name: income, dtype: float64


```python
#取出特定位置的元素，按照数字进行索引
apt[[3,4,1]]
```


    ShangHai     60000.0
    Shenzhen     50000.0
    Guangzhou    40000.0
    Name: income, dtype: float64


```python
#取出特定位置的元素，按照索引名称进行索引
apt[ ['ShangHai', 'Shenzhen', 'Guangzhou'] ]
```


```python
ShangHai     60000.0
Shenzhen     50000.0
Guangzhou    40000.0
Name: income, dtype: float64
```

## Series广播特性


```python
# 广播特性
3*apt
```


```python
##可以看到每个元素的值都变为原来的3倍
Beijing      165000.0
Guangzhou    120000.0
Hangzhou      90000.0
ShangHai     180000.0
Shenzhen     150000.0
Suzhou            NaN
Name: income, dtype: float64
```

下边同理：


```python
apt/2.5
```


```python
Beijing      22000.0
Guangzhou    16000.0
Hangzhou     12000.0
ShangHai     24000.0
Shenzhen     20000.0
Suzhou           NaN
Name: income, dtype: float64
```

list是不能够直接做数学运算的，不具有广播特性。


```python
# list不可以直接做数学运算
my_list = [2,4,6,8,10]
```


```python
#这样会报错
my_list/2
```


```python
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-74-05191d4696a7> in <module>()
----> 1 my_list/2
TypeError: unsupported operand type(s) for /: 'list' and 'int
```
## 基于索引的计算

我们首先看一下apt[1:]和apt[:-1],然后计算这两个的和。


```python
apt[1:]
```


```python
#apt[1:]的样子
Guangzhou    40000.0
Hangzhou     30000.0
ShangHai     60000.0
Shenzhen     50000.0
Suzhou           NaN
Name: income, dtype: float64
```


```python
apt[:-1]
```


```python
#apt[:-1]的样子
Beijing      55000.0
Guangzhou    40000.0
Hangzhou     30000.0
ShangHai     60000.0
Shenzhen     50000.0
Name: income, dtype: float64
```


```python
# 基于索引去做计算的
apt[1:] + apt[:-1]
```


```python
Beijing           NaN
Guangzhou     80000.0
Hangzhou      60000.0
ShangHai     120000.0
Shenzhen     100000.0
Suzhou            NaN
Name: income, dtype: float64
```

可以看出，如果两个相加的值中有一个不存在，最后的结果就空值。比如“beijing ”和"shenzhen"。

## 判断元素是否在Series中

这个只要一个`in`就搞定了


```python
# in判断index是否存在
'Hangzhou' in apt
```


    True
```python
'Chongqing' in apt
```


    False
## boolean indexing/条件判断索引

现在，我们想看一下房价大于40000的城市都有哪些，这时候，我们可以先做一个判断，看下每个城市的房价是否大于等于40000。


```python
#判断每个城市的房价是否大于等于40000
apt >= 40000
```


```python
Beijing       True
Guangzhou     True
Hangzhou     False
ShangHai      True
Shenzhen      True
Suzhou       False
Name: income, dtype: bool
```

然后，将这个判断条件作为索引，看下边的代码，成功找到符合要求的城市。


```python
#条件索引
apt[apt>=40000]
```


```python
Beijing      55000.0
Guangzhou    40000.0
ShangHai     60000.0
Shenzhen     50000.0
Name: income, dtype: float64
```

## Series统计计算


```python
# 计算均值
apt.mean()
```


```python
47000.0
```
```python
#计算中位数
apt.median()
```


```python
50000.0
```
```python
#计算最大值
apt.max()
```


```python
60000.0
```
```python
#计算最小值
apt.min()
```


```python
30000.0
```

```python
#更多高级的数学运算
np.log(apt)
```


```python
    Beijing      10.915088
    Guangzhou    10.714418
    Hangzhou     10.714418
    ShangHai     11.002100
    Shenzhen     11.156251
    Suzhou             NaN
    Name: income, dtype: float64
```


## Series赋值

比如现在我们想修改Series中的某一个元素的值。将“shenzhen”改为70000。

可以直接进行赋值操作，是不是感觉和list很像？


```python
apt['Shenzhen'] = 70000
```


```python
#查看一下apt,发现shenzhen的值已经修改了
Beijing      55000.0
Guangzhou    40000.0
Hangzhou     30000.0
ShangHai     60000.0
Shenzhen     70000.0
Suzhou           NaN
Name: income, dtype: float64
```

我们想要按照某个条件赋值怎么办呢？


```python
#条件赋值，将条件写在索引位置即可
apt[apt<=40000] = 45000
```


```python
apt
```


```python
#查看一下apt,发现满足条件的元素的值已经修改了
Beijing      55000.0
Guangzhou    45000.0
Hangzhou     45000.0
ShangHai     60000.0
Shenzhen     70000.0
Suzhou           NaN
Name: income, dtype: float64
```

接下来，我们定义另外一个Series，来做一些两个Series之间的操作。


```python
cars = pd.Series({'Beijing':350000, 'ShangHai':400000, 'Shenzhen':300000, \
                 'Tianjin':200000, 'Guangzhou':250000, 'Chongqing':150000
                 })
```


```python
#查看cars
Beijing      350000
Chongqing    150000
Guangzhou    250000
ShangHai     400000
Shenzhen     300000
Tianjin      200000
dtype: int64

```

我们定义一个Series，名字为`expense`，经过下边计算，我们可以看出，Series之间是可以进行直接相加减的。


```python
expense = cars + 10*apt
```


```python
#查看expense，发现有空值，说明cars和apts的索引值不完全一样。
Beijing       900000.0
Chongqing          NaN
Guangzhou     700000.0
Hangzhou           NaN
ShangHai     1000000.0
Shenzhen     1000000.0
Suzhou             NaN
Tianjin            NaN
dtype: float64

```





```python
apt
```




    Beijing      55000.0
    Guangzhou    45000.0
    Hangzhou     45000.0
    ShangHai     60000.0
    Shenzhen     70000.0
    Suzhou           NaN
    Name: income, dtype: float64

## 数据缺失问题


```python
#判断元素是否不是空值， bool结果返回
apt.notnull()
```


```python
Beijing       True
Guangzhou     True
Hangzhou      True
ShangHai      True
Shenzhen      True
Suzhou       False
Name: income, dtype: bool
```


```python
#判断元素是否不是空值， bool结果返回
apt.isnull()
```


```python
Beijing      False
Guangzhou    False
Hangzhou     False
ShangHai     False
Shenzhen     False
Suzhou        True
Name: income, dtype: bool
```

**将`expense`中的空值赋值为`expense`的均值**


```python
expense[expense.isnull()] = expense.mean()
```


```python
#查看一下expense
Beijing       900000.0
Chongqing     900000.0
Guangzhou     700000.0
Hangzhou      900000.0
ShangHai     1000000.0
Shenzhen     1000000.0
Suzhou        900000.0
Tianjin       900000.0
dtype: float64
```

