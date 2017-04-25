### Content
1. [ConvNet Configurations](#convnet-configurations)
2. [Classification Framework](#classification-framework)
3. [Classification Experiments](#classification-experiments)

# 引言

VGG论文名称为：[Very Deep Convolutional Networks For Large-Scale Image Recognition](https://arxiv.org/pdf/1409.1556.pdf)。

ZF网络在第一层卷积层的大小上就比Alexnet更加小，步长也更加小了。

VGG网络比之前的AlexNet更加deep了。

# ConvNet Configurations

与AlexNet一样，在对图片的预处理上只是每个像素减去了训练集上RGB的平均值。

## Architecture
1. 卷积核的大小是3\*3和1\*1的，并且步长均为1。 3\*3的卷积核有1个像素的padding，这样保证输入输出维度的不变。
2. 网络的池化层方式是max pooling，且大小都是2\*2，步长是2.
3. 在最后的三个FC层之后，是一个softmax层。
4. 卷积核的数目从一开始的64个，在每次池化之后都会乘以2，直到达到512.
5. 所有的层的激活函数都是ReLU。并且网络中并没有使用LRN，因为发现LRN并没有提高模型的正确率。

![VGG_Configuration](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/VGG_Configuration.png?raw=true)

## Discussion

1. 2个3\*3的卷积核的感知野是5\*5,3个3\*3的卷积核的感知野是7\*7，可以替代更大的感知野。
2. 多个3\*3的卷基层比一个大尺寸filter卷基层有更多的非线性，使得判决函数更加具有判决性。
3. 多个3\*3的卷积层比一个大尺寸的filter有更少的参数，假设卷基层的输入和输出的特征图大小相同为C，那么三个3\*3的卷积层参数个数3\*（3\*3\*C\*C）=27CC；一个7\*7的卷积层参数为49CC；所以可以把三个3\*3的filter看成是一个7\*7filter的分解（中间层有非线性的分解）。
4. 1*1 filter: 作用是在不影响输入输出维数的情况下，对输入进行线性形变，然后通过Relu进行非线性处理，增加网络的非线性表达能力。

# Classification Framework

1. MiniBatch的大小设置为256。
2. 采用带有动量的梯度下降方法，momentum设置为0.9
3. 采用L2正则化，参数设为5*0.0001
4. 前面两个全连接层的dropout比例设为0.5
5. learning rate初始值设置为0.01，当验证集的正确率停止改善的时候，将学习因子除以10。在网络训练的时候，学习因子总共只降低了3次。

尽管VGG比Alex-net有更多的参数，更深的层次；但是VGG需要很少的迭代次数就开始收敛。这是因为：

1. 深度和小的filter尺寸起到了隐式的正则化的作用

2. 一些层的pre-initialisation：网络的权重初始化方法：首先利用随机初始化方法初始化网络A，并对网络A进行训练，之后将网络A训练得到的权重用于初始化其余网络结构的前面4层卷积层和最后3个全连接层。其它的层则通过随机初始化方式进行初始化。随机初始化是采用（0，0.01）的正态分布来初始化。

## 训练

记S为训练图片里面最短的边。在训练的时候，每次SGD迭代的时候都会从训练图片中剪取224\*224大小的图片。

有两种方法设置S：
1. 第一种是固定S。论文实验的时候选取S=256和384。为了加速网络的训练，通过训练S=256的网络，来对S=384的网络的权重进行初始化；
2. 第二种方法是给S一个区间（论文采用的是[256,512]），在每张训练图片随机缩放到S大小（个人理解是每张训练图片被输入的时候都会随机crop，应该是只会crop一次）。

## 测试

首先选定一个Q，将测试图片的最小边缩放到这个大小。对同一个S，将Q设为不同的大小能够增加。还需要将卷积网络的FC层全部转换为CONV层，然后将图片输入得到输出。

在测试网络性能的时候，作者测试了multi-crop和dense两种评估方式：multi-crop的方式是对卷积特征进行0填充，而dense的方式填充图片中相邻的像素值。

# Classification Experiments

## Single Scale Evaluation

对于固定的S，选取相同的测试大小Q；对于[Smin, Smax]，选取测试大小为(Smin + Smax)/2. 结果如下图所示：

![Single_test_scale](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/VGG_Single_Scale_Evaluation.png?raw=true)

- A vs A-LRN：A-LRN没有A网络的性能好，说明LRN作用不大，所以在之后的实验中，并没有应用LRN的结构；
- A vs B C D E：网络结构越深越好；
- C vs B：C比B多了两个1\*1的卷积核，由于增加了非线性性，性能得到了提升；
- C vs D：D将C的1\*1的卷积核替换为3\*3的卷积核，获得了更好的效果，使用较大的fliter能够捕获更大的空间特征；
- 作者还将B结构的3\*3的卷积核替换为拥有相同感知野的5\*5的卷积核，事实证明，拥有小的卷积核的更深的网络比拥有大的卷积核但更浅的卷积核的性能更好；
- 训练方法：在scale区间[256，512]通过scale增益来训练网络，比在固定的两个S=256和S=512，结果明显提升。Multi-scale训练确实很有用，因为ZF论文中，卷积网络对于缩放有一定的不变性，通过multi-scale训练可以增加这种不变性的能力。

## Multi-Scale Evaluation

对于固定S的训练方式，使用的测试Q为：{S-32, S, S+32}

对于[Smin, Smax]的训练方式，使用的测试Q为：{Smin, middle, Smax}

![multiple_test_scales](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/VGG_Multiple_Scale_Evaluation.png?raw=true)

- 可以看出使用多尺度评估的性能比单尺度的性能更好；
- D和E这种很深的网络结构更好；
- single-scale在256和384上进行训练，无论用什么测试方法，结果基本差不度。说明网络对单个scale提取能力有限。使用带有尺度范围的训练，能够得到更好的效果。

## Multi-Crop Evaluation

作者还比较了dense和multi-crop两种验证方式的结果：

![MultiCrop_Evaluation](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/VGG_MultiCrop_Evaluation.png?raw=true)

可以看出multi-crop的性能更好，把两者结合起来比单个的性能好。这主要是由于这两种卷积边界的处理方式不同造成的（一个0填充，一个像素填充）。

***

Author: Zhipeng Han

Time: 2017/03/24

读论文不是要翻译，需要了解精髓，并学会总结。
