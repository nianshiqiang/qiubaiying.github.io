---
layout:     post
title:      tensorflow非线性回归示例
subtitle:   
date:       2018-03-17
author:     粘世强
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - tensorflow
---

### 非线性回归的例子

首先生成了200个随机点，作为真实值。然后利用神经网络进行运算，中间层有1层，10个节点。最后利用梯度下降法进行优化，优化次数为5000次。然后利用matplotib作图。观察训练情况。

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
```

```python
#生成200个随机点,范围自-0.5至0.5.
x_data = np.linspace(-0.5,0.5,200)[:,np.newaxis]#使x_data成为200行1列的数据
noise = np.random.normal(0,0.02,x_data.shape)#参数依次为均值，标准差，形状
y_data = np.square(x_data) + noise
```

```python
#定义两个占位符
x = tf.placeholder(tf.float32,[None,1])
y = tf.placeholder(tf.float32,[None,1])

#定义神经网络中间层
weight_l1 = tf.Variable(tf.random_normal([1,10]))
b1 = tf.Variable(tf.zeros([1,10]))
wx_plus_b_l1 = tf.matmul(x,weight_l1) + b1#形状为[None,10]
l1 = tf.nn.tanh(wx_plus_b_l1)
#定义神经网络输出层
weight_l2 = tf.Variable(tf.random_normal([10,1]))
b2 = tf.Variable(tf.zeros([1,1]))
wx_plus_b_l2 = tf.matmul(l1,weight_l2) + b2#形状为[None,1]
prediction = tf.nn.tanh(wx_plus_b_l2)
#代价函数
loss = tf.reduce_mean(tf.square(y - prediction))
#梯度下降法优化，学习率为0.1
train = tf.train.GradientDescentOptimizer(0.1).minimize(loss)
#启动会话
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
#训练5000次
    for i in range(5000):
        sess.run(train,feed_dict = {x:x_data,y:y_data})
        if i%500 == 0:
            print(i,sess.run(loss,feed_dict = {x:x_data,y:y_data}))
        
    prediction_value = sess.run(prediction,feed_dict={x:x_data})


```

```python
#作图
plt.figure()
#真实值的散点图
plt.scatter(x_data,y_data)
#经过神经网络运算之后的图
plt.plot(x_data,prediction_value,"r-",lw = 4)
plt.show()
    
```

```python
0 0.205317
500 0.000539622
1000 0.000492743
1500 0.000487392
2000 0.000483957
2500 0.000481407
3000 0.000479364
3500 0.00047762
4000 0.000476059
4500 0.000474615

```

![img](https://github.com/nianshiqiang/nianshiqiang.github.io/blob/master/contentimg/%E9%9D%9E%E7%BA%BF%E6%80%A7%E5%9B%9E%E5%BD%92%E7%BB%93%E6%9E%9C.png?raw=true)