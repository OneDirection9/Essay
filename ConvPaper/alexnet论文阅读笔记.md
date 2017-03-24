# 引言

[\<\<ImageNet Classification with Deep Convolutional Neural Networks\>\>](http://www.cs.toronto.edu/~fritz/absps/imagenet.pdf)

文章比较老，但是该网络很经典。之后的ZF，VGG等网络都是在该网络基础上改造而来。

ZF网络的结构和Alexnet差不多。具体网络结构对比可见[这里](http://blog.csdn.net/fdchao/article/details/52915716)。

[卷积网络的总结](http://www.mamicode.com/info-detail-1602750.html)，里面还展示了数据增益的水平变化等方法。

# 数据集

在ILSVRC-2010数据集上进行实验，再参加了ILSVRC-2012的比赛。ILSVRC竞赛一般用top-1和top-5来衡量模型的优劣。top-5 where the top-5 error rate is the fraction of test images for which the correct label is not among the five labels considered most probable by the model.

由于卷积网络对输入图片的尺寸有固定的要求，所以我们需要对数据集的图片进行处理，将各种维度的图片转换为256\*256像素的。处理方式如下：将矩形图片的短边缩放到256像素，再居中截取256\*256大小的图片。
 
对于图片的预处理上，只采用了将图片像素值减去训练集上像素值的平均值。

# 网络结构

## ReLU Nonlinearity

神经元的激活函数选取了ReLU函数。这是由于sigmod和tanh函数容易达到饱和，在输入很小或者很大的时候，函数的梯度几乎为0。这样在反向传播算法中反向传播接近于0的梯度，导致权重没什么更新，我们就无法进行学习了。如果你的初始值很大的话，大部分神经元可能都会处在saturation的状态而把gradient kill掉，这会导致网络变的很难学习。

ReLU激活函数的收敛速度比sigmod和tanh快很多。可能是由于它是linear的，并且梯度不会饱和。相比于 sigmoid/tanh需要计算指数等，计算复杂度高，ReLU只需要一个阈值就可以得到激活值。

具体分析可见[几个常用的激活函数](http://blog.csdn.net/u014365862/article/details/52710698)。

## Training on Multiple GPUs

受限于当时的GPU内存，作者在两个GPU上进行训练。在多个GPU上训练的意思是卷积核平均分布在这两个GPU上。并且只有第三层卷积层与POOL2的输出是全连接的，其它层都只与同一个GPU上的层相连接。

## Local Response Normalization

```math
b_{x,y}^i = \frac{\alpha_{x,y}^i}{(k + \sum_{j=max(0,i-n/2)}^{min(N-1,i+n/2)}(\alpha_{x,y}^j)^2)^\beta}
```
Local Response Normalization（LRN），局部归一化，具体公式见论文。`$ \alpha_{x,y}^i $` 的意思是卷积核i得到的feature map上的(x, y)坐标位置的输出。有两种LRN的方法，一个是像论文公示里面说的，通过同一个位置相邻的feature maps上进行LRN；另一个是同一个feature map上，相邻的像素点之间进行LRN。

当我们使用ReLU的激活函数的时候，响应结果是无界的（可以非常大）所以需要归一化。

AlexNet在CONV1和CONV2的卷积层之后应用LRN，对卷积层的激活输出进行归一化，作为池化层的输入。

## Overlapping Pooling

使用了重叠的池化结构。正常的池化层的感知野z\*z和步长s相等，本文的网络选择是z=3, s=2，这样的话就有一部分池化是重叠的。AlexNet网络的池化是max pooling。

最后一个FC层，接下来使用的softmax分类器。

# 降低过拟合的方法

## Data Augmentation

通过增加数据量，减少过拟合现象。

1. 从上面处理得到的256\*256的图片中，遍历选取224\*224大小的图片，总共能够得到32\*32个patch，再通过水平翻转就能够得到2048个patch。在测试的时候，从图片里面选取四个角和中心的五个patch，并通过水平翻转得到10个patch，对这个10patch的预测取平均值，就是测试的输出。
2. 改变RGB颜色通道强度。具体没看太懂

## Dropout   
   
dropout，在前面两个全连接层加入了dropout技术，随机丢弃掉一些神经元。

# 一些细节

训练采用**带动量的梯度下降算法**。好好看一下带动量的梯度下降算法。

***
Author: Zhipeng Han

Time: 2017/03/24

读论文不是要翻译，需要了解精髓，并学会总结。
