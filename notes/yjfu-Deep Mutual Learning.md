@(论文2)[Knowledge Distilling|Ensemble]
#Deep Mutual Learning
##Introduction
* 这篇文章里没有teacher了，他用一群相互学习、ensemble起来的student来代替teacher。
* 这里讲了数据蒸馏的一个理解方式：较小的模型并不是能力不够，而是较为难以直接训练（但是没有说为什么）。
* 大概好像是student之间相互一边用gt训练ensemble的结果，一边让结果相互靠近。感觉这虽然跟ensemble的理念相违背（即保证各个成员的多样性），不过他目的是训练单个网络，然后如果ensemble的结果比较好的话，这个样子优化的最优解确实也是把所有的子网络都训练成ensemble的结果，好像说得通。
* 提到了Dual Learning，就是两个语言相互翻译的那个。感觉关系其实不大。
* 也提到了跟ensemble的理念的冲突，但是他也不在乎。
##Deep Mutual Learning
* 好像上面说的有点不对，用gt训练的并不是ensemble的结果，而是各个分支分别独自训练的。然后除此之外还有一个kl散度来作为让两个分支的输出接近的loss
* 如果网络有很多，那每个网络训练的时候，k个网络就要计算k-1次kl散度
* 他这里提到了，把ensemble的结果作为teacher进行训练，说效果不如这个好。他说原因可能是这种情况下gt的那个标签被突出，而其他的标签被抑制，就不够soft。感觉这个解释可能是有道理的，如果真的是这样那么dark knowledge可能真的跟soft有关系。
##Experiments
* 这里也是想探讨一下dark knowledge到底是什么。发现，用了kd以后比没用kd输出的结果更加平滑（用top5的预测结果表示，发现其他类别的输出结果比不用kd要高）。他还计算了输出的平均熵，不用kd的话熵会更低。感觉这可能是一个比较好的指标
* 讲了上面说的那个ensemble的实验。效果没有之前好。他说是因为熵更低，即能量更集中。但是之前的实验中，多加网络会对性能提升有帮助。我感觉多加网络本质上是一个增多了梯度的不确定性的操作。这可能会让收敛变慢，但是对性能会有什么影响，这个我不太确定。