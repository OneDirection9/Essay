### Content

1. [Introduction](#introduction)
1. [Related Work](#related-work)
1. [Deep Residual Learning](#deep-residual-learning)
1. [Experiments](#experiments)
1. [总结](#%E6%80%BB%E7%BB%93)

# 引言

ResNet的论文名称为 [Deep Residual Learning for Image Recognition](https://arxiv.org/pdf/1512.03385.pdf)。

ResNet网络是微软亚研院提出的，在2015年的ImageNet和COCO 2015竞赛上获得以下项目的第一名：ImageNet detection, ImageNet localization, COCO detection, COCO segmentation。该网络结构主要的特点是跳跃连接，下面对论文的一个简单解读。

这篇[博客](http://blog.csdn.net/u011534057/article/details/51819968)写的还不错。

# Introduction

由于网络的深度越深而导致的梯度消失/梯度爆炸的问题，可以通过归一化的初始化和归一化的中间层来解决。但是出现了新的问题**Degradation**:

> with the network depth increasing, accuracy gets saturated (which might be unsurprsing) and then degrades rapidly.

并且该问题不是由于过拟合引起的。如下图所示：

![ResNet_Degradation_Problem](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Degradation_Problem.png?raw=true)

当层数越深的时候，正确率反而下降了。该问题表明并不是所有的问题都是容易优化的。

> 如果在一个浅层网络A上叠加几层layer形成网络B，如果这些新添加的layer是Identity mapping，那么网络B性能至少不会比A差。但是实际实验结果却显示网络越深，性能越差。所以作者猜测solver对于学习Identity mapping比较困难。既然学习恒等映射比较麻烦，那干脆直接给它加上一个shortcut，直接给这个模块输出叠加上输入。实际情况中，恒等映射x并不是最优解H(x)，最优解在恒等映射附近，这个最优解与恒等映射之间的差就叫做residual F(x)。

为了解决这个问题，这里我们提出一个 deep residual learning 框架来解决这种因为深度增加而导致性能下降问题。假设我们期望的网络层关系映射为`H(x)`, 我们让 the stacked nonlinear layers 拟合另一个映射，`F(x):= H(x)-x`, 那么原先的映射就是`F(x)+x`。 这里我们假设优化残差映射`F(x)`比优化原来的映射 `H(x)`容易。`F(x)+x`可以通过 shortcut connections 来实现，如下图所示：

![ResNet_Residual](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Residual.png?raw=true)

# Related Work

ResNet的网络结构与highway network类似，对比如下：

1. DRN的shortcut connections一直是输入s，并没有其它参数，学习比较简单。而且一直传递x，每个模块只学习残差F(x)，网络稳定而且容易学习。
1. DRN证明了随着网络深度的增加，性能变好。而highway network并没有证明。

> 实际运用中，DRN中的shortcut有些是带有参数的，因为有的模块有降维操作，输入输出的维度不一样。

# Deep Residual Learning

## Residual

*这部分总结了论文的3.1和3.2节的内容*

Degradation问题说明原本的网络通过非线性层学习Identity mapping比较困难，就使用了introduction里面介绍的残差网络的连接方式。如果恒等变换是最优的，那么可以将非线性层的参数置为0来达到恒等映射的目的。实际上学习残差网络使得每层的响应输出变小。

为了保持输入x和F(x)的维度一致，有时候需要加入线性映射Ws：

![ResNet_Linear_Projection](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Linear_Projection.png?raw=true)

> 虽然也可以给x加上参数，但是直接加上x已经足够解决degradation问题并且计算难度不高，所以一般只有在遇到维度不一样的时候才需要加上Ws。

残差函数F是可变的，本篇论文涉及了一下两个形式（网络层数更多也是可能的）：

![ResNet_Residual_Funciton](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Residual_Function.png?raw=true)

> 如果只是一层的话，那么就等于线性网络了y = W * x + x，并没有什么好处。

## Network Architectures

**Plain Network**的设计受VGG的启发，主要采用3\*3的卷积核，遵循以下两个规则：

1. 对于相同输出特征图尺寸，卷积层有相同个数的滤波器；
1. 如果特征图尺寸缩小一半，滤波器个数加倍以保持每个层的计算复杂度。

还通过步长为2的卷积层直接进行降采样。去除了全连接层，参数相比VGG-19大幅度降低。

**Residual Network**基于plain network的基础上，加上了shortcut。当维度上升的时候，加入shortcut有以下两个选择：

1. 对shorcut加上zero padding，来达到同等维度。该方法并不需要额外的参数。
1. Linear Projection来实现维度变换。（通过1\*1的卷积，来达到同等维度的目的。）

网络结构如下：

![ResNet_Network](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Network.png?raw=true)

## Implementation

网络的实现细节。

1. 短边随机缩放到\[256, 480\]，从图片或者水平变换的图片中截取224\*224大小的图片，每个像素减去像素的平均值。
1. standard color augmentaiton（AlexNet论文里面data augmentation章节的第二个方法吧，暂时不太懂）
1. 在每个卷积之后，激活之前，加入了Batch normalization。
1. 随机梯度下降方法，mini-bach的大小为256.
1. 学习率初始化为0.1，当错误率停止下降的时候，将学习率除以10.
1. 采用动量更新，momentum设置为0.9
1. 不采用dropout

测试采用类似于AlexNet的方法，从四个角和中心截取4个patch，并水平翻转得到总共10个patch，取这个10个的平均值。

# Experiments

测试所使用的网络结构如下表所示：

![ResNet_Architecture](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Architecture.png?raw=true)

ResNet的三种评估方法：

1. A：zero-padding shortcuts are used for increasing dimensions, and all shortcuts are parameterfree.
1. B：projection shortcuts are used for increasing dimensions, and other
   shortcuts are identity.
1. C：all shortcuts are projections.

![ResNet_Table3](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Table3.png?raw=true)

![ResNet_Table4](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Table4.png?raw=true)

![ResNet_Table5](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/ResNet_Table5.png?raw=true)

# 总结

该网络和GoogLeNet一起，都是与众不同的连接方式，GoogLeNet的作者也结合了这两个网络发表了一篇论文。总的来说残差就是网络学习的是H(x)-x，在维度不同的时候有两个解决办法：1. zero-padding；2. projection shortcut。

______________________________________________________________________

Author: Zhipeng Han

Time: 2017/04/24
