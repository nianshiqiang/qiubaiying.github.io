### 激活函数与代价函数的选择

常用的代价函数有二次代价函数，交叉熵代价函数。对数似然代价函数。常用激活函数有sigmoid函数（二分类问题时使用），softmax函数（对于多分类问题，神经网络最后一层的激活函数为softmax函数）。

#### 二次代价函数

首先，二次代价函数如下：
$$
C = \frac{1}{2n}  \sum_{x} (y - prediction)^2
$$
![](C:\Personal files\Academic\data science\tensorflow\10讲课程\二次代价函数2.png)

![](C:\Personal files\Academic\data science\tensorflow\10讲课程\二次代价函数3.png)

从以上的函数偏导可以看出，函数的收敛速度和激活函数的导数是正相关的。导数越大，收敛速度越快。

假设我们的目标时收敛到1，那么A点离目标值很远，梯度很大，收敛很快；而B点离目标很近，梯度很小，收敛很慢。相反地，假设目标为收敛到0，那么A点离目标较近，梯度较大，收敛较快；而B点离目标更远，反而一开始收敛较慢，所以这种方案是不合理的。

所以说，当激活函数是sigmoid时，二次代价函数并不完全合理。

#### 交叉熵代价函数

![](C:\Personal files\Academic\data science\tensorflow\10讲课程\交叉熵代价函数.png)

从上边的推到结果可以看出，权重（w)和偏置（b)的导数与"预测值和真实值的差值"有关，而与激活函数的导数无关。当差值越小，即越接近真实值时，梯度越小，收敛越慢；离真实值越远时，收敛越快。符合我们的要求。这样能够加快收敛速度。



**综上**， 当输出神经元（激活函数）是线性函数时，选择用二次代价函数较为合理，当激活函数为S型函数时，选择用交叉熵函数较为合理。



在深度学习中，更为普遍的做法是将softmax方程作为最后一层的激活函数，此时常用的代价函数是对数似然函数。一般来讲，对数似然函数和softmax函数组合，而交叉熵函数和sigmoid函数组合使用。

在tensorflow中：

tf.nn.sigmoid_cross_entropy_with_logits()来表示与sigmoid搭配的代价函数。

tf.nn.softmax_cross_entropy_with_logits_v2()来表示和softmax搭配使用的代价函数。