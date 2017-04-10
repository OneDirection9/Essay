# 引言

该[链接](http://www.jianshu.com/p/33197e469414)是别人总结的GoogleNet的阅读笔记，里面包含了GoogleNet的四个版本，以及相应的论文，相当好。

# Motivation and High Level Considerations

改善深度神经网络性能的方法大体有两种：

1. 增加深度：增加网络的层数；
2. 增加宽度：增加每一层神经元的数目。

但是这样做有两个缺点：过拟合；计算量增加（如果最后网络权重为0的话，那么就是无效的增加计算量）。

解决上述两个问题的基本原则是将全连接改为**稀疏连接**。但是目前的计算架构对稀疏结构的支持不是很高效（查找和缓存失效是稀疏矩阵的瓶颈）。

本文希望能够获得一个结构，既能保证稀疏性，又能利用现有矩阵进行密集计算。

# Architectural Details

![Inception的结构](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/GoogLeNet_Inception_Module.PNG?raw=true)

加入1\*1的卷积，是为了降维。特别是在inception结构中，每一层都是前一层各种卷积汇聚起来，再加上额外的pooling层，将会导致层数的上升，所以在每个卷积层之前加入1\*1卷积进行降维。

上述结构有两个优点：
1. 为每个stage增加units的数目而不用担心不可控的计算复杂度。因为在下一个stage的时候通过dimension reduction，来防止上一层产生的过多的channel传递到下一次层。
2. 这种结构满足了在不同的scale下处理视觉信号然后聚集在一起输入到下一个阶段以至于可以继续从多scales下提取特征。

![GoogLeNet的结构](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/GoogLeNet_Architecture.PNG?raw=true)

> "\#3\*3reduce"和"\#5\*5reduce"代表应用于对应的卷积核之前的1\*1卷积核的数目。  
> pool proj列：表示应用于max pooling之后的1\*1卷积核的数目

**网络的细节**：
1. 网络的所有激活函数都是Rectified Linear Activation(ReLU)。
2. 网络有参数的层是22个（加上池化层是27个）
3. 网络中间层的所产生的特征具有很好的区分度，所以增加了auxiliary classifiers与中间层相连接，是为了在反向传播的时候得到更多的梯度信号，并且提供额外的正则化。在计算的时候，总体损失回加上auxiliary classifiers的损失乘以一个权重（论文里面是0.3）。在预测的时候，这些auxiliary classifier会被丢弃。

# Training Methodology

1. 使用带动量的随机梯度下降方法。momentum设置为0.9
2. 学习率每8个epoch下降4%。

# ILSVRC 2014 Classification Challenge Setup and Results

1. 集成方法：训练了7个相同结构的GoogLeNet模型，初始化方法，学习率调整策略相同，图像采用（patch）以及随机输入的顺序不相同。
3. aggressive cropping方法：ILSVRC中使用的很多图是矩形，非正方形。将图像resize成4种scales，使得最短的边分别为256，288，320和352，然后从左、中、右分别截取方形square图像（如果是肖像图像，则分为上、中、下），然后对于每个square图像从4个角及中心截取224x224 square images，并把原square图像resize成224x224，在对上面5种做镜像变换。所以这样一幅图像可以得到4x3x6x2=144个crops。
4. 对multiple crops的softmax概率取平均效果最好。

***

Author: Zhipeng Han

Time: 2017/04/10
