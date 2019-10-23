@(论文2)[Knowledge Distilling, Data Free, Zero-Shot KD]
#Data-Free Learning of Student Networks
##Introduction
* 他这个方法是去解决，当训练student的时候，原来训练teacher的那些样本都不能再用了的时候该怎么办的问题
* 好像是训练一个gan来解决问题。把teacher当成discriminator，生成器的目的是生成让teacher某种响应最大的图片（这其实不叫对抗了，因为teacher是固定的。可能还要别的discriminator去保证生成结果不坍塌之类的）
##Related work
* 提到了一个Amalgamation的工作【22】，做teacher特征融合教给student的
* 提到了其他data free的方法，但是只有两个。一个是把fc层相似的神经元融合，另一个是保留训练集各层feature的均值和方差，恢复出来做蒸馏。
##Data-free Student Network learning
* 这里就是在说怎么生成样本的。主要有三点
* 第一，teacher在训练集上输出的结果应该是非常steep的（也就是某个类别很高而其他类别很低），因此如果generator学到了训练集的分布的话，它输出的图片在teacher的输出应该也是很steep的。__问题：这样是否会让kd失去意义？因为kd很需要smooth的输出__
* 也就是说，生成的图片被teacher分成a类以后，就做一个a类的one-hot标签，用正常算出来的交叉熵去反向传播generator的梯度
* 第二是，他认为对于teacher的feature来说，当遇到同域的数据的时候，feature的值应该处于被强烈的激活的状态。因此他取了高层feature的1范数，最大化它，作为第二个loss。__问题：feature不仅仅是被激活，而且是处于一种某些部分被激活，某些部分被抑制的情况。是否可以有更细致的抚照？__
* 第三是，他希望生成的样本尽量均匀。这里他做的挺好的，没有单纯的用最后的分类做限制，而是计算了teacher对所有n个生成样本输出的概率向量，算他们的熵，让他们的信息量最小，从而生成较为均匀的结果。但是值得注意的是，这一条结合第一条，优化到极致之后结果跟上面说的简单的用最后的分类做限制好像并没有区别。
* 提到【23，16】，说他们是根据反向传播去推测生成输入图片的。
##Experiments
* 实验结果中，第三点的loss是一个基础，没有他的话剩下两个效果都很差。mnist上，用随机噪声来训练都能有88%的正确率（作为base line），而如果只用前两个，效果会很差，甚至训练不起来。当然只用第三点结果其实也是88%。都加起来效果最好，但既没有student自己在训练集上训练好，更没有用训练集做kd好。
* 前两个之一附加到第三个上的时候，都会让实验结果有很大的提升。大概可以认为第三个是保证随机性，而前两个保证的是domain？
* 还试过用其他手写体数据集作为教学，效果并没有他的好（但比随机的好很多）
* 做了一个可视化实验，发现teacher和student的filter很像。这是一个很理想的结果了
* 