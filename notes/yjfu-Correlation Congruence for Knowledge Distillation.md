@(论文2)[Knowledge Distilling|Relation]
#Correlation Congruence for Knowledge Distillation
##Introduction
* 传统知识蒸馏只在乎面对单个样本，teacher和student的一致性。而这里提出的则是要把样本之间的关系也考虑进去，即teacher认为两个样本比较接近，student就也要认为他们比较接近。
##Related Work
* 提到了设计轻量模型相关的一些文章
* 提到了对噪声在kd里的应用的一点初步研究【27】
* 提到了训练策略：是否可以边训练teacher边做kd【39，1】
##Correlation Congruence Knowledge Distillation
* 普适的说，就是student对两个样本提取了特征，teacher也提取了特征，然后保证这两个样本的某个距离在student和teacher上是一致的
* __这里有一个问题就是：我们到底是不是要求这个距离完全一致？我感觉是不用的。它的距离可以不一致，只需要结构上是一致的就行了，比如样本abc，不需要ab，bc，ac的距离在teacher和student都一样，只需要在student和teacher的表达里ab都大于ac就可以了__
* 他这里的距离是用的高斯核来衡量的
* __这里提到了第二个问题：因为sgd是随机采样的，所以，尤其是当batchsize比较小而类别比较多的时候，很容易采样的数据都不属于同一个类别，至少属于不同类别的比较多__
* __我感觉这就是他的方法的上述缺陷导致的。即，映射到高维空间以后，高维流形只在局部符合欧氏距离的关系（因此学习这个距离可能是有用的），而对于较远的的样本则不能用欧氏距离去度量（因此学习这个距离是没用的）__
* 他有两种解决方案：按照类别数目平均的sample，和按照超类数目平均的sample。前者很正常，后者就是考虑到前者有时类别比较多，就需要bachsize很大，相当于类别数跟batchsize强耦合了。因此先用teacher的feature做聚类，聚成几个超类，再进行平均的sample
##Experiments
* 他的实验中发现，有些kd的方法有时表现的甚至没有原始kd好
* 发现高斯核其实之比点积作为metric好一点点，但是比1范数要好一个点。
* 展开项影响比较大，用3项泰勒展开去展开高斯核比用2，1项要好的挺明显的
* 关于采样的影响，这里有一个这样的观察：他是固定batchsize为40，但是有些实验是设置每个类别样本数为一个定值的。当这个值为8，20时（即一个batch只有5，2类），效果会比随机采样要差。感觉这是因为batch的随机性太差了，导致下降的方向有很强的的偏向性。__这绝对是他的方法的一个弱点，即想要做这个方法必须要有充足的相近的样本才比较好，但相近的样本多了随机下降的效果就会差__
* 感觉他的聚类方法比直接按类别取平均分布的方法没有很明显的提升
* 