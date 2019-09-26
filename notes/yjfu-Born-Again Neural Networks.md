@(论文2)[Model Compression|Knowledge Distilling]
#Born-Again Neural Networks
##Introduction
* 这个名字来源于Born-Again Trees。大意就是，数个模型ensemble成了一个模型，而一个重生的模型（复杂度不超过这些模型之一）可以在性能上与ensemble模型比较。
* 他的实验是，训练模型得到teacher，然后重新初始化一个相同的模型作为student，同时用teacher和label进行训练，得到的结果比teacher好。这个他叫做self-distillation。
##Related Literature
* 有些工作是关于如何用其他学习方法去蒸馏一个神经网络的，这有利于提高可解释性。
* Czarnecki et al. (2017) 最小化teacher和student对同一图片的loss的梯度。
* Urban et al. (2016)（我没找到这个引用）好像是完成了把多个teacher网络蒸馏到一个里
* Zagoruyko & Komodakis (2016a)用attention map蒸馏，即把channel压成一层用来计算损失
* Rusu et al. (2015) 也是多个teacher蒸馏成了一个，不过是增强学习
* 还有用知识蒸馏做增量学习、对抗学习的
* 说，Yim et al. (2017)这篇文章跟本文很像，都是自己蒸馏自己。这篇文章的做法很有意思，对于一个网络的同尺度的两个feature，他计算了他们的一个统计量（内积）。然后他让teacher和student的这个统计量保持一致，也就是变化一致。
* 他说这个文章提出的损失没有比正常的KD loss好，然后说自己试验做的很严谨，结果也是最好的。
##Born-Again Networks
* 他提到了他的流水线，依次的self distill下去，搞出来好多，然后简单的ensemble一下。
###Dark Knowledge Under the Light
* 提出了两种对知识蒸馏的解释。一种是认为，teacher输出的预测结果包含着一些dark knowledge，这些信息反映了一定的类间关系。另一种从梯度上解释，说KD loss相当于是一种重要性的权重，跟attention有点像。
* 这里的公式是要把KD loss的梯度分成两部分（假设输出用sigmoid，即是logits），一部分是正确的标签计算出来的梯度，另一部分是这个样本的其他标签（错误的标签）计算出来的梯度
* 而相比较普通的交叉熵，跟label计算出来的loss的梯度，第二部分是完全没有的，梯度由原来的q变成了q-p（q和p分别是相对应的这个类别的logits）；第一部分则相当于原始的label（表现为1）乘上一个系数，由q-1变成了q-p*1，即可以看成是对正确分类的梯度的reweight.
* 他考虑的就是，这个所谓的dark knowledge到底是来源于这两部分的哪部分。他把第一部分叫做reweight，第二部分叫做non-argmax outputs，大概是即把0改成了p，没经过argmax使得变成0、1的意思
* 他做了两个实验。一个是，完全删去了第二部分，即只有reweight这种情况，一个batch里teacher对这个样本越肯定（logits越大）就乘以更大的系数；另一种还不太确定，好像是第二部分改成了随机的p（打乱了对应的关系）
##Experiments&Results
* 这里提到，他有试过用feature层次的distill，但是发现效果还不如logits的distill的好。
* cifar10上其实表现的不很明显。cifar100上能有1个点的提升，关于dark knowledge 哪一部分更重要这方面，结果并不是很明确。有的网络两者效果差不多，有的网络则表明reweight比non-argmax outputs更差，甚至还会起反作用
* 也就是说，KD有作用的可能不是attention，重要性，reweight，关键可能在non-argmax outputs上。鉴于这些东西被弄混了还能有一些提升（比起不弄混有大概半个点左右的下降），作者猜测可能是因为顺序打乱不影响他们的矩（moment），所以起作用的应该是这种统计量。
* 当然，reweight大多数时候还是能带来提升的，所以这种关注同样是有意义的
* 加了label的loss可能反而会不如不加
* 多自蒸馏几次的结果很好，能再提升接近一个点。
* ensemble的结果也很好，能再提升一个点
* 关于结构等的影响，感觉没有什么系统性的结论，只能得到一些孤立的观察现象。比如用densenet做老师教resnet效果居然很好（比用resnet教都好）。反过来用不同的resnet做teacher教一个densnet，只展示了学生超过了老师的效果。