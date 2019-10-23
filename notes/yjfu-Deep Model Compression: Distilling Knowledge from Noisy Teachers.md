
@(论文2)[Knowledge Distilling]
#Deep Model Compression: Distilling Knowledge from Noisy Teachers
##Introduction
* 比较早的文章，前面主要讲的是知识蒸馏相关，没有讲明白noisy是怎么回事
* 这里基于的方法不是hinton的那一篇，不是带t的softmax求kl散度，而是直接最小化logits的距离
##Proposed Methodology
* 这里的理论基础是：噪声在一定程度上相当于正则项。比如已经得到证明的，输入加入高斯噪声等价于l2正则项。
* 他的做法就是把一个高斯噪声加在teacher的输出上。他最初的motivation是用这个来模拟多teacher的学习（认为多个teacher可能会引入更多地噪声？这步推理我没明白）
* 他这里把损失函数拆开来分析。感觉好像做kd的时候加在label上的噪声是一个l1的正则项。
* 他也不是所有样本都加这个噪声，是以一定概率加的。最后实验里面用的大概是15%这个样子的比例
##Experiments
* cifar10上有挺明显的提升，但是baseline的结果感觉太差了，而其他数据集上的结果其实不很明显。
* 他做了一个这样的实验：不把噪声加在teacher的输出上，而把噪声加在student的输出上（其实就相当于teacher减了一个这个噪声）。注意之前的实验中，噪声是在训练前提前加进去的，即一个样本”加了噪声的label”是确定的，两个epoch是相同的。但是这个实验里噪声是在训练的时候放进去的，即相当于对于两个epoch里同一个样本，teacher的输出减去的噪声是不同的。结果是前者更好
* 这说明稳定的随机噪声有好处，即，即使这个噪声没有意义，让student去拟合它也有些好处（解释的是正则化），而真正的、每次都变的噪声则会让性能下降
* 试了不用高斯噪声而用dropout，效果没有高斯好
* 也试过对于高斯噪声的标准差也做随机，效果没有最好的那个固定标准差好
* 试了真正的多teacher，说效果比单teacher好，但是没有他的单teacher+noise好
* 