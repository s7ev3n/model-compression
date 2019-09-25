@(论文2)[Knowledge Distilling, Model Compression]
#A Comprehensive Overhaul of Feature Distillation

##Introduction
* 这篇文章是完全做蒸馏的，好像是制定了一套特殊的规则
* 说最早的知识蒸馏是直接用softmax去做，但是对于较好的teacher来说，softmax的结果往往就跟groundtruth没什么区别。所以现在更多的方法关注的是利用特征做蒸馏。提到了两个比较近的方法，AB【7】和FT【13】，我觉得都值得看一看。
* 他说他针对feature转换的问题，主要研究了teacher的feature和student的feature应该怎么做转换，距离函数怎么写，用哪个位置的feature等。
* 他说它主要关注的转换有两个，一个是经过ReLU之后的feature大小的量级有没有得到保存，另一个是各个神经元的激活状态有没有得到保存（我觉得这好像是一个东西呀）
* 他提出了一个损失函数，部分的计算l2距离，忽略一些响应值
* 除了cifar10和imagenet，他还做了detection和segmentation的实验。
##Motivation
###Teacher Transformation
* 这个是用来对teacher的feature做一个转换，也就是决定student要学什么的操作。提到的方法除了第一个做feature蒸馏的工作，其他的工作都是依据某些先验去忽略一些信息，造成了一定的信息损失（不一定是坏事，可能损失的是有害的信息）。
* 他说他的方法是一个新的ReLU模块，可以让有害信息被阻隔，只让好信息通过。
###Student Transformation
* 一般来说这个部分跟上个部分是对称的。也就是说，teacher没交什么，student就不要学什么
* 也有的不是对称的。比如teacher的feature很厚，他们就用1*1的卷积把student的feature变厚
###Distillation feature position
* 这个问题有不同的选择方式，有些方法提取每一个阶段（scale变化）的最后feature，有些方法则只用最后一层feature。
* 他这里用的是前者，主要讨论的是应该用ReLU之前还是之后的feature（当然他自己还有一个ReLU，之前之后都要加他自己的这个）。很多人都用之后的，他用的是之前的。
###Distance function
* 他说因为他用的是ReLU之前的feature，所以距离函数也要考虑这个因素，有害信号会相对增多，因此需要在此处滤除一些有害的信号
##Approach
###Distillation position
* 也没有特别有insight的说明，大意就是列举了几种情况，在ReLU之前做蒸馏，然后效果不错
###Loss function
* 其实这里连着teacher和student的transformation也一起讲了。
* teacher 的transformation其实就是一个向左下方平移了一下的ReLU，以前是小于0的都置为0，现在是小于m（m是个负数）的都置为m
* 他说以前也有人这么干过，但是m都是定死了的。他这里的m取的是各个神经元的均值，可以是以batch为单位算的，也可以让teacher 在整个训练集上过一遍计算这个均值。
* student 的transformation其实就是一个1*1卷积加一个bn
* 关于损失，也就是距离函数，他主要是考虑了负值。对于那些负值，他认为student应该是“可以更小（因为反正要ReLU，更小表示更会被切掉），但不能更大（更大，尤其是变成了0以上的数，就会被留下）”
* 他用的蒸馏是同时做蒸馏和用真实样本训练的过程。
###Batch normalization
* 这里主要是提了一下，teacher的BN层应该在什么模式（train or test）下。这好像是一个比较容易忽略的问题。应该在train下。
##Experiments
* 先在Cifar100上做了实验。效果确实比别人的好。测试了不同压缩方式（比如，是压缩深度，channel数，还是都压缩）、不同网络结构的影响。发现压缩深度的效果是最好的，然后teacher和student如果结构相差比较大，效果就会不好。
* imagenet用了ILSVRC 2012的部分，120万张图片，224×224。他直接用pytorch提供的预训练模型当teacher
* 做了ResNet152-》ResNet50的，和ResNet50-》MobileNet的
* 做了detection的实验，用SSD的架构，teacher用的是VGG和ResNet50的主干网络，student用的是ResNet18的主干网。
* 比baseline 有提升，没做什么对比的实验
* segmentation基本也差不多。不过特意提了一句，student是先经过预训练的。
* 他这里做了个统计，统计了student的输出和teacher输出的kl散度，和label的交叉熵。说，有些方法把蒸馏当成一种初始化手段，这会导致后期的训练破坏了跟原模型的相似性。（这类方法叫做Initial distillation，与之相对的就是一直作为一个损失存在，continuous distillation）
* <font color=red>ablation study：BN的状态大概能有不到半个点的影响。他的损失函数影响挺大的。位置的影响最大。所以负值即使在原模型里被ReLU给cut掉了，也是有一定意义的，但是需要谨慎对待，不能全面的接受。这几条应该是他的主要贡献。</font>
