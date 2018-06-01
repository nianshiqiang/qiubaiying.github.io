---
layout:     post
title:    sklearn 数据预处理函数fit,transform和fit_transform的区别
subtitle:   sklearn fit,transform和fit_transform
date:       2018-05-31
author:     粘世强
header-img: img/beautiful.jpg
catalog: true
tags:
    - machine learning
---
## sklearn 数据预处理函数fit,transform和fit_transform的区别

```python
from sklearn import preprocessing   
import numpy as np    
X = np.array([[1., -1., 2.],
              [2., 0., 0.],
              [0., 1., -1.]])  
```

```python
#创建一个数据规约的对象，然后拟合数据
scaler = preprocessing.StandardScaler().fit(X)
#output: StandardScaler(copy=True, with_mean=True, with_std=True)  
```

```python
#计算出原数据中每个特征（列）的均值
scaler.mean_
#output: array([ 1.,  0. ,  0.33333333])
```

```python
#计算出原数据中每个特征（列）的标准差
scaler.std_
#output: array([ 0.81649658,  0.81649658,  1.24721913]) 
```

按照计算出来的均值和标准差对数据进行标准化（使特征数据方差为1，均值为0），即通过公式

(x - x.mean)/x.std得到标准化后的值

```python
scaler.transform(X)
 #output: array([[ 0., -1.22474487,  1.33630621],
 #             [ 1.22474487, 0. , -0.26726124], 
 #             [-1.22474487,1.22474487, -1.06904497]])  
```

```python
#对新的数据进行标准化，此时进行标准化的均值和标准差依旧是fit得到的数据的均值和标准差
scaler.transform([[-1., 1., 0.]])
#output: array([[-2.44948974,  1.22474487, -0.26726124]]) 
#解释：（-1 - 1 ）/ 0.81649658 = -2.44948974
#	： （ 1 - 0 ) /  0.81649658 =  1.22474487
#   :  (0 - 0.33333333) / 1.24721913 = -0.26726124
```

所以从上边的代码中可以看出：`fit`的作用在于到数据中去学习，得到数据的均值和标准差。`transform`的作用在于按照学习到的均值和标准差对新的数据进行标准化。

那`fit_transform`又有什么用呢？

从名字上就可以理解，这个命令是先对数据进行`fit`,得到数据的相关参数，然后再进行`transform`，这个命令只是将`fit`和`transform`两个命令结合在一起了。

也就是说，当我们在训练集上调用`fit_transform`，对训练数据进行先拟合后标准化的处理时，此时我们已经得到数据的均值和方差，这一操作接下来不用重复执行，因此我们在测试集上只需要调用`transform`，对测试数据进行标准化的处理。数据处理过程如下图所示：

![](https://raw.githubusercontent.com/nianshiqiang/nianshiqiang.github.io/master/contentimg/fit_transform.png)