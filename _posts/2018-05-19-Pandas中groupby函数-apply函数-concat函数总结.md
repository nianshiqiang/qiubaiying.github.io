---
layout:     post
title:     Pandas中groupby函数，apply函数，concat函数总结
subtitle:   groupby,apply,concat
date:       2018-05-19
author:     粘世强
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
        - python
        - pandas
---

之前的两期中，我们简单的讨论了Pandas中两种基本的数据结构，Series和DataFrame。今天我们来说一说pandas中的几个重要的函数，分别是groupby，apply和concat。

## groupby函数

首先来看一下groupby函数，顾名思义，groupby就是按照xx分组的意思，所以这个函数就是用来对DataFrame进行分组使用的。下边来看一个例子，就能清楚的看明白这个函数的功能了。

```python
salaries = pd.DataFrame({
    'Name':['BOSS','HanMeimei','HanMeimei','Han','BOSS','BOSS','HanMeimei','BOSS'],
    'Year':[2016,2016,2016,2016,2017,2017,2017,2017],
    'Salary':[40000,5000,4000,3000,38000,42000,6000,39000],
    'Bonus':[12000,3000,3500,1200,16000,18000,7000,21000]
})
```
上边的代码定义了一个DataFrame，名字叫做salaries。

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/salaries.png?raw=true)

接下来，我们对这个salaries按照“Name”属性进行分组。

```python
group_by_name = salaries.groupby('Name')
```

那么`group_by_name`到底是个什么样子的呢？

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/group_by_name.png?raw=true)

可以看出，`groupby`其实是将原来的`salaries`按照`Name`分成了三类，这时候，我们自然而然的就会想到对每一类里边的属性（如`Bonus`  ,  `Salaries`等）做一些操作，如求和(`sum`)，求平均值(`mean`)，求中位数(`median`)等。

```python
group_by_name.sum() #求和
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/sum.png?raw=true)

```python
group_by_name.mean() #求平均
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/mean.png?raw=true)



当属性特别多的时候，我们只想对某几列做统计算，这时候需要在`group_by_name`中挑选特定的列进行计算：

```python
group_by_name[['Bonus','Salary']].sum() 
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/sum1.png?raw=true)

有时候，我们肯定想知道某项在整个数据中到底出现了多少次，这时候可以利用`groupby`来做。

```python
salaries.groupby('Name').size()
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/size.png?raw=true)

现在我们想要看一下每个类别的人的工资和奖金的均值，中位数，求和，标准差。这时候要分为两个步骤，首先对数据进行`groupby`分组，然后对分组后的数据进行求和，求均值等操作。

```python
salaries.groupby('Name')[['Bonus','Salary']].agg(['sum','mean','std','median'])
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/groupby agg1.png?raw=true)

```python
#使用numpy中的函数
salaries.groupby('Name')[['Bonus','Salary']].agg([np.sum,np.mean,np.std,np.median])
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/groupby agg2.png?raw=true)



## apply函数

为`salaries`添加一个临时列`tmp_col` 。

```python
salaries.loc[:,'tmp_col'] = ( salaries['Salary'] * 2 -1500 ) / 0.8
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/new_salaries.png?raw=true)

```python
def trans(x):
    return ( x * 2 - 1500 ) / 0.8
```

```python
salaries.loc[:,'tmp_col2'] = salaries['Salary'].apply(trans)
```

![](C:\Personal files\Academic\data science\公众号内容\groupby,apply,concat\apply1.png)

从上边的例子中可以看出，apply函数可用来对某一列进行自定义的操作，只需要自己定义一个函数，然后对这一列调用`apply` 函数即可。

大家不要小瞧`apply`函数，这个函数厉害的地方就在于能够对某一列进行自定义的操作，只要你相对某一列做各种复杂的操作，只需要自己写一个函数，然后调用`apply`就可以了。



## concat函数

`concat`函数是一个拼接函数，作用就在于将几个DataFrame进行拼接，从而形成一个新的DataFrame。

我们首先定义三个DataFrame，分别命名为`df1`,`df2`,`df3`

```python
df1 = pd.DataFrame({'apt':[55000,60000],'cars':[200000,300000]},index = ['Shanghai','Beijing'])
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/df1.png?raw=true)

```python
df2 = pd.DataFrame({'apt':[35000,45000],'cars':[120000,100000]},index = ['Hangzhou','Guangzhou'])
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/df2.png?raw=true)

```python
df3 = pd.DataFrame({'apt':[30000,10000],'cars':[120000,100000]},index = ['Nanjing','Chongqing'])
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/df3.png?raw=true)

```python
#将上述三个DataFrame进行拼接
result = pd.concat([df1,df2,df3])
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/concat1.png?raw=true)

从上边的结果可以看出，`concat`默认是按照列对齐的方式进行拼接，即每一条数据都有这些属性。但有时候，我们也需要按照行对齐的方式进行拼接，针对这些数据，不断的添加属性的种类。

```python
pd.concat([df1,df1,df1],index = 1)
```

![](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/groupby,apply,concat/concat2.png?raw=true)

