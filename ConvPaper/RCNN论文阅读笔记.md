### Content
1. [Object detection with R-CNN](#object-detection-with-r-cnn)
2. [总结](#总结)

# 引言

RCNN论文名称为： [Rich feature hierarchies for accurate object detection and semantic segmentation](https://arxiv.org/pdf/1311.2524.pdf)。

该论文是RCNN系列论文的开山之作。RCNN论文提出了目标检测的一个整体的框架。步骤分为3步：1. 提取proposal；2. 训练CNN；3. 训练SVM。下面是论文的细节。

# Object detection with R-CNN

R-CNN由三个模块组成：

![RCNN_System_Overview](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/RCNN_System_Overview.png?raw=true)

1. 通过 selective search 算法生成候选的 proposals；
2. CNN：将第一步提取到的 proposals 送入卷积网络进行特征提取；
3. SVMs：将 CNN 提取得到的特征输入 SVM 进行分类。

## Model design

1. Region proposals: 通过 selective search 算法从原始图片中提取出大概2000个 proposals 。

2. Feature extraction: 利用 AlexNet 从每个 proposal 中提取4096维度的特征向量。在将 proposal 输入 CNN 之前需要将其转换为固定的大小。

## Test-time detection

测试的时候从测试图片中提取出大概2000个 proposals 。将 proposals 然后送入 CNN 提取特征。然后对每个类别，利用为该类别训练的 SVM 对提取出的特征进行打分。然后为每个类别打分好的 proposals 进行 **non-maximum suppression** ，目的是为了降低 bounding box 的重合度。

> 应该是2000个 proposals 都被每个类别的 SVM 打分，然后每个类别对这个2000个 proposals 分别进行 NMS。
>
> 代码里面是每个类别大于某个阈值的 proposals 才进行 NMS。

两个特性使得检测高效：

1. 所有的 CNN 参数对于所有类别来说都是共享的；
2. 相比于其他的方法，CNN 计算出来的特征向量维度比较低。维度低的话所需要的存储空间就少。

## Training

1. **Supervised pre-training**：在辅助数据集(ILSVRC2012 classification)上 pre-trained 一个CNN模型。
2. **Domain-specific fine-tuning**：在 pre-trained CNN 模型上，使用 VOC 数据集上提取出的 proposals 进行参数调整，使得模型适应新的任务和领域。只需要将CNN模型的分类的层替换为一个随机初始化的N+1层（N个物体类别，1个背景类别）。将 warped region proposal 与人工标注的 bounding box 的 IoU 大于等于0.5的看做正样本，剩余的看做负样本。在进行SGD的时候，将learning rate设为0.001(初始预学习率的1/10)。SGD的每次迭代的时候，随机抽样32个正样本窗口，96个背景窗口形成128大小的batch进行迭代。
3. **Object category classifiers**：这是一个二分类问题，我们为每一个类训练一个SVM。将 CNN 得到的 fc7 层的特征作为 SVM 的输入。通过验证得到，将与 bounding box 的 IoU 大于0.3的 region 看做正样本。

    在使用每个类别的 SVM 对每个 proposal 进行打分之后，会对该 proposal 进行 bounding box regressor，矫正 box 的位置，提高正确率。

> 但是我看代码里面的 demo 并没有运行 bounding box regressor 。

# 总结

论文里面说测试的使用 NMS，这样可以减少输出的 bounding box 的个数。在训练的时候会调用 bounding box regressor 来矫正 bounding box 的位置。我的理解是训练的时候不会使用 NMS，而只使用 bounding box regressor。在测试的时候会进行 bounding box regression，这样就生成了 2000*N 个 box，会对这么多个 box 按类别进行 NMS。

RCNN的优缺点：

- 优点：
    - 正确率高。
- 缺点：
    - 训练分为多个阶段，步骤繁琐：微调网络+训练SVM+训练边框回归器
    - 训练耗时，占用磁盘空间大：5000张图像产生几百G的特征文件
    - 速度慢：使用GPU，VGG16模型处理一张图像需要47s。对图像提完region proposal（2000个左右）之后将每个proposal当成一张图像进行后续处理(CNN提特征+SVM分类)，实际上对一张图像进行了2000次提特征和分类的过程！
    -crop/warp图片来适应CNN网络的输入，这样降低了识别正确率。
 
***

Author: Zhipeng Han

Time: 2017/04/25
