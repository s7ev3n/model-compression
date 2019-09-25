@(论文2)[Model Compression|Knowledge Distilling]
#Improved Knowledge Distillation via Teacher Assistant: Bridging the Gap Between Student and Teacher
##Introduction
* 主要就是提出了这样一个观点：teacher网络和student网络的复杂度如果差距很大，则教学效果不好
* 解决方法是引入一个size的量级处于两者之间的模型来辅助训练，把它叫做TA
##Related Work
* 提到了一些跟GAN或者对抗方法相关的知识蒸馏(Heo et al., 2018; Xu et al., 2018; Wang et al., 2018b)，应该看一看。
* 提到了如何多个网络一起学习的工作，但是感觉没有特别解决这个问题的pipeline，尤其是针对一个student，多个teacher的这种情况，好像是做ensemble的。
* 可以看下You et al. (2017)，决定怎么跟随多teacher学习；以及Anil et al. (2018)，如何进行分布式的在线学习（大概是多teacher多student的情况？）
* 提到了为什么KD是有效的。 Furlanello et al. (2018) 这个是从梯度的角度说的，像看下。
## Assistant based Knowledge Distillation
###Background and Notations
* 讲用output做KD的方法是怎么做的。需要注意的是，softmax(a/τ)这个形式的Softmax，不但teacher要除以τ，student也要除以τ，这两个去计算KL散度作为损失。这是我以前想错了的。
* 最后的KD损失是τ^2 * KL(y_s, y_t)，y_就是softmax(a/τ)，即前面要有个τ^2
* 这篇文章用的是一起KD loss和真实label一起训练的模式
### The Gap Between Student and Teacher
* 这篇文章认为，teacher和student的大小应该是有一个对应的关系的，即一定大小的student适合一定大小的teacher，反之亦然。
* 他做了两个实验，一个是固定student的大小，扩大teacher，发现随着teacher变大（同时性能变强），student的性能是先提高再减小的（并不是teacher越强越好）
* 这可能有两个原因：teacher太强student学不了；teacher太强导致输出的softmax不够平滑，即跟直接用label训练一样了。
* 另一个实验是，固定teacher的大小，扩大student的大小。结果也是先变好再变差。这说明一定大小的teacher适应一定大小的student。
###Teacher Assistant Knowledge Distillation (TAKD）
* 就是讲了插入一个中间scale的模型
* 这里提出了一些观点，有一些我觉得还挺有意思的。比如讨论TA最适合的大小应该怎么定义，是不是应该引入多级的TA，或者说干脆重新训练一个小一点的模型是不是效果提升会更明显。他说这些实验他们都做了。
##Experimental Setup
* 实验在cifar10和100上做的。
* 不知道为什么他们的训练设计的都比较粗糙，比如普通的CNN就直接用0.1的学习率训练150个epoch
* KD loss 的超参数是用hyper-parameter optimization toolkit(Microsoft-Research, 2018) 来做的。
##Results and Analysis
###Will TA Improve Knowledge Distillation
* 结果是提升的，能提升不到一个点（有时候是半个点）。不同的TA带来的精度不一样，有的TA基本不会带来很大的变化，有的甚至带来了负变化。
* 关于应该选择什么复杂度的TA，这里实验的结果是，最好的TA的复杂度不一定在teacher和student之间，但是其精度应该在两者之间。
###Why Using a Distilled TA
* 这里讨论为什么TA也要用数据蒸馏的方式获得，而不是从头训练一个较小的网络替代那个大网络
* 他这里做了两个解释。第一个是，做了这样的实验：网络的大小一致时，网络的精度越高，蒸馏出来的小网络效果也就越好（因此蒸馏过的TA精度大于没蒸馏过的TA可以推出用蒸馏过的TA蒸馏出的小网络精度也更好）
* 第二个是，实验证明确实是这样的。
* 实验还发现，用从头训练的TA比直接用大teacher的效果好。这说明，有时候如果size差别很大，还不如用精度稍低但是size差别不大的模型蒸馏好。
###Why Limiting to 1-step TA
* 实验证明，好像TA越多越好。
###Comparison with Other Distillation Methods
* 效果也比做的别人的好
##Why Does Distillation with TA work
###Theoretical Analysis
* 列了一个公式，大意就是，模型学到的函数的错误率与真实函数的错误率的差（前者显然大）是有个上界的。这个上界一个取决于模型能力与样本难度（包括样本数量），以及模型和真实函数的距离期望。
* 他由5，6推7我感觉推不出来。 Lopez-Paz et al. (2015)这篇文章应该是有别的条件（O可能是个线性函数？然后还有别的条件保证小于号成立）
* 感觉他就是想说，Lopez-Paz et al. (2015)用模型能力解释了模型能力的影响，而他这里添加TA的操作则是通过改变样本难度来进一步缩小上界的。
###Empirical Analysis
* 直觉上的分析就是，引入TA以后损失更平滑了，更好学习
