@(论文2)[GAN, Knowledge Distilling]
#KDGAN: Knowledge Distillation with Generative Adversarial Networks
##Introduction
* 这里给了一个更general的描述，把计算资源，包括训练数据等，都叫做privileged provision。部分privileged provision只有训练的时候有，而推理的时候就没有了
* 他这里说的是：对于知识蒸馏而言，是希望通过teacher来教会student（本文叫classifier）真实的分布，但是问题在于，teacher的训练也不一定能很好地学到这个分布。
* 他说，这个问题可以让GAN来解决。他可以很好地学到真实的分布。但是他也有自己的问题，就是训练的效率太低，不管是对数据的需求还是训练的难度都是制约因素。
* 所以他要把这两个结合起来。看到这里我觉得这个结合有点生硬。这个结合就是，他会把student和teacher和Discriminator放在一起，前两个都是生成器，Discriminator就正常的判别。然后除了正常的两个adversarial loss之外，还有一个DK loss。他就解释说前者有利于让student学习真正的分布，后者有利于稳定loss，提高训练效率
* 感觉这种做法没有从本质上解决问题啊
* 这好像只想做一个多标签选取的任务。他的想法就是用gan生成标签，而不是把它看成一个多分类任务。
* 另一个方面感觉好像挺厉害的，他说gan训练的低效率源于他的梯度不稳定，而这又源于gan的生成样本是离散的。他说要生成连续样本，从而减少梯度的不稳定性。
##Related Work
* 提到了一个triple-gan【13】，我觉得这个好像挺好玩的。他的多出来的两个生成器分别是给定特征生成label，和给定label生成特征，好像有点类似于cycle的意思。这篇文章两个生成器都是给定特征生成label
##Methods
* 这里重新描述了一遍privileged provision的定义，即既可能是缺少算力（因此需要减小模型大小），又可能是缺少数据（因此不能重现训练）。这里还提到一种是在推理的时候可能会缺少某种输入。这种我觉得挺有意思的。这里给的例子是【47】，不过看名字好像跟迁移比较有关系。
###NaGAN Formulation
* 这是naive版本的kdgan
* 其实就是用gan来做这个kd的任务。他们认为一个大的网络作为Discriminator可以充分地学到如何判别真实的分布，然后用一个小的网络做预测，充当generator。
* 这里学的分布好像就是真实的分布，而不存在一个强大的teacher网络。
* 感觉有点问题的点是Discriminator的强大可能并不一定是一件好事
* 他说这个方法保证了student学习到的分布和真正的分布相同，而kd的话只是要求他跟teacher相同，没有要求跟真的分布相同。
* 他说缺点在于这种训练方式比较慢。
* 我有一个问题是，为什么不直接用真实的label作为辅助loss做训练。
###KDGAN Formulation
* kdgan就比较复杂，他的交互包括：student和teacher有一个kd的交互，teacher，student与Discriminator都有gan的交互，而同时teacher和student还有一个反向的kd的交互。他说这个加上去效果会更好
* 他给出的证明是何时这个损失取到最小值
###KDGAN Training
* 他这里提到一个观点：训练的效率是与梯度的不一致程度（variance）相关的【8，46】。他说gan之所以训练慢就是因为梯度太随机，而kd就可以减少这种冲突。
##Experiment
* 他只在cifar10和minist上面做了实验。
* 然后又在标签推荐的任务上做了实验
* 在分类问题（前者）上，用naive gan的效果没有正常kd的效果好，多标签推荐的任务上没有比较两者的效果。
* 比较加不加扰动（一个重参数方法做的随机采样），不加会好一些。这个好像是他用来证明梯度随机性的弊端的方法。