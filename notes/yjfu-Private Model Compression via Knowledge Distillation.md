@(论文2)[Model Compression|Knowledge Distilling]
#Private Model Compression via Knowledge Distillation
## Introduction
* 模型压缩，这篇文章好像还讨论了关于隐私保护的内容
* 最后的结果是压缩率*20， 速度*19，精度下降大概1%
* 提到了知识蒸馏，即teacher-student
* 提到了量化和低秩分解。
* 他说这篇文章主要搞知识蒸馏，并且关注了隐私
* 假设是这样的：数据分为隐私数据和公开数据两种，teacher网络用两者训练，而student网络则只能用公开数据
## Related Work
* 他说，压缩方法主要分四种：parameter sharing, network pruning, low- rank factorization, and knowledge distillation。前三种是在元模型的基础上进行简化，最后一种是训练一个小的新模型
* 这里提到了差分隐私（differentially private），指的是，假设一个数据库里有100个人的数据，如果差分隐私比较差，那么知道99个人每个人的数据和100个人的总体查询数据（比如99个人的身高和100个人的平均身高）就能知道第100个人的数据。一套算法的差分隐私的衡量标准就是对于两个adjacent inputs（在数据库里叫兄弟表，只有一项不同（这一项就是差）），他们的经过这个算法的输出越接近越好，隐私水平就越高。
## Proposed Method
* 小模型用了hint learning，distillation learning，和self learning的方法。
###Model Compression
* hint learning：实际上就是feature的相似性，做一个l2的损失。这里有一个要注意的是，比如teacher是512通道而student是256通道，那就用一个1*1的卷积去增加通道数来计算这个损失。这个加进来的卷积层叫做adaptation layer。
* distillation learning和self learning就是，teacher的输出（logits）过一个softmax（对soft参数可以进行改变以避免忽略概率小的类别）作为label，计算交叉熵损失。所谓的self learning，跟一般意义上的不一样，这里的self是跟teaching相对应的，实际上就是让student除了跟着teacher学之外也跟gt学，计算跟gt的交叉熵。
* 他说，为了避免隐私泄露（即不让student有太高的差分隐私），应该控制student向teacher学习的次数（即distillation learning的次数），而self learning的次数可以无限大。
###Privacy Protection
* 目的就是限制 distillation learning过程中student模型的差分隐私，我感觉这里其实不是特别确切，实际上只是在减少student对隐私信息的拟合程度而已。
* 做法就是，限制一个上限B，如果loss比较大就resacle到这个上线限B。然后还给loss加一个高斯噪声，但是加噪声这个事情本身是不可导的，不知道有什么用。如果是给teacher的预测结果加一个噪声那还可以理解。
* B的设计，他的跟我的很像，都是在batch里取平均值。
* 然后还算出了一个训练次数的上限T。因此在这个distillation learning的过程中需要取一个最有效的训练子集进行训练

###Query Sample Selection
* 样本选择的目的是找到一个最能代表整体的子集。
* 最后的经过证明，结论是要弄出一个子集，使得每个点附近都有邻近的点

##Experimental Evaluation
* 用了MNIST，CIFAR-10和SVHN
* teacher网络用的是Conv-Large（(Laine 2017; Park et al. 2017）
* 实验里面感觉比较比较重要的结论是，教学样本的选择很重要。甚至于加了这么多限制以后，数据蒸馏的效果还有提升。这可能是一个可以主要研究的点。
* 他也没能给隐私性一个很好的定义。
* 对batch size的影响的评估不太公平。