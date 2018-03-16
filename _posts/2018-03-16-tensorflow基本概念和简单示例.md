---
layout:     post
title:     tensorflow基本概念和简单示例
subtitle:   基本概念、创建图、启动图、变量、简单示例
date:       2018-03-16
author:     粘世强
header-img: img/home-bg.jpg
catalog: true
tags:
        - tensorflow
---
### 一： 基本概念

1. 使用图 (graph) 来表示计算任务.

2. 在被称之为 会话 (Session) 的上下文 (context) 中执行图.

3. 使用 tensor 表示数据.

4. 通过 变量 (Variable) 维护状态.

5. 使用 feed 和 fetch 可以为任意的操作(arbitrary operation) 赋值或者从其中获取数据.

   ![](C:\Personal files\Academic\data science\tensorflow\tensorflow结构图.png)

### 二：创建图和启动图

```python
   import tensorflow as tf
   #首先是构造节点

   #创建一个常量op
   m1 = tf.constant([[3,3]])
   #创建另一个常量op
   m2 = tf.constant([[2],[4]])
   #创建一个矩阵乘法的op
   product = tf.matmul(m1,m2)
   print(product)  #此时product依然是一个op,还没有被运算
```

   Tensor("MatMul_1:0", shape=(1, 1), dtype=int32)

```python
   # 创建一个会话，启动默认图
   sess = tf.Session()
   #调用run方法，进行运算，触发了3个op.
   result = sess.run(product)
   print(result)
   sess.close() #最后要关闭session.
```

   创建会话的另外一种方法（常用）：

```python
   #另一种方法调用session，执行会话，不用关闭sess.
   with tf.Session() as sess:
       result = sess.run(product)
       print(result)
```

### 三：变量

   最重要的一步是在进行session运算之前，进行变量初始化！！

```python
   import tensorflow as tf
```

```python
   #添加一个变量op
   x = tf.Variable([[3,8]])
   #添加一个常量op
   a = tf.constant([[1,5]])
   #增加一个减法op
   sub = tf.subtract(x,a)
   #增加一个加法op
   add = tf.add(x,sub)
   #变量初始化，很重要！！
   init = tf.global_variables_initializer()
   #构建一个session
   with tf.Session() as sess:
       sess.run(init)#在会话中先进行初始化
       print(sess.run(add))
       print(sess.run(sub))
```

   输出结果：

```python
   [[2 3]]
   [[5 11]]
```

   下边的代码是在tensorflow中进行循环。

```python
   state = tf.Variable(0,name = "counter")
   new_value = tf.add(state,1)
   update = tf.assign(state,new_value)#变量赋值
   init = tf.global_variables_initializer()#变量初始化，重要！！
   # print(type(state))
   #构建会话
   with tf.Session() as sess:
       sess.run(init)
   #     print(sess.run(new_value))
       for _ in range(5):
           sess.run(new_value)
           print(sess.run(update))
```

   输出结果：

```
   1
   2
   3
   4
   5
```

### 四：fetch和feed方法

```python
import tensorflow as tf
```

```python
#fetch
#定义三个常量op
input1 = tf.constant(2.0)
input2 = tf.constant(3.0)
input3 = tf.constant(4.0)
#定义一个加法op和一个乘法op
add = tf.add(input1,input2)
mul = tf.multiply(add,input3)

with tf.Session() as sess:
    print(sess.run([add,mul]))#fetch方法的特点就是可以同时run多个op
```

输出结果：

```python
[5.0, 20.0]
```

```python
#feed
#准备两个占位符
input1 = tf.placeholder(tf.float32)
input2 = tf.placeholder(tf.float32)
add = tf.add(input1,input2)

with tf.Session() as sess:
    print(sess.run(add,feed_dict = {input1 : 3.0,input2:4.0}))#在run时传入参数,以字典的形式传入参数
```

输出结果：

```python
7.0
```

### 五：tensorflow简单实例

```python
import tensorflow as tf
import numpy as np
```

```python
#生成100个随机数
x_data = np.random.randn(100)
t_data = np.random.rand(100)
#构造一个多元线性方程
y_data = x_data * 1.5 + 0.5 * t_data + 0.8
```

```python
#构造一个多元线性模型
#定义三个变量
b = tf.Variable(0.)
k = tf.Variable(0.)
m = tf.Variable(0.)
y = x_data * b + k * t_data + m
#构造一个损失函数(二次代价函数)
loss = tf.reduce_mean(tf.square(y_data-y))#先求差值的平方，然后求平均
#构造一个优化器
optimizer = tf.train.GradientDescentOptimizer(0.2)#梯度下降法优化，学习率为0.2
#最小化代价函数
train = optimizer.minimize(loss)
init = tf.global_variables_initializer()#变量初始化
with tf.Session() as sess:
    sess.run(init)
    for i in range(501):
        sess.run(train)
        if i%50 == 0:
            print(i,sess.run([b,k,m]))

```

输出结果：

```python
0 [0.54393262, 0.31414878, 0.53052932]
50 [1.4995866, 0.50569946, 0.79704297]
100 [1.499871, 0.50177789, 0.79907757]
150 [1.4999597, 0.50055462, 0.79971224]
200 [1.4999874, 0.50017303, 0.79991019]
250 [1.4999961, 0.50005412, 0.79997194]
300 [1.4999987, 0.50001693, 0.79999119]
350 [1.4999995, 0.50000542, 0.79999721]
400 [1.4999998, 0.50000167, 0.79999918]
450 [1.4999998, 0.50000155, 0.79999918]
500 [1.4999998, 0.50000155, 0.79999918]
```

可以看出经过500次迭代之后，b,k,m均已经收敛到了真实值附近。