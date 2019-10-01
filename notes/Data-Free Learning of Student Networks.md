<!--
 * @Author: s7ev3n
 * @Date: 2019-10-01 20:51:10
 * @Github: https://github.com/s7ev3n
 * @LastEditTime: 2019-10-01 21:12:52
 * @Description: 
 -->
# Data-Free Learning of Student Networks

## Abstract
这篇文章是将，如何没有训练数据，Student网络是如何向Teacher网络来学习的。
作者用GAN来生成训练样本，Teacher作为D，Student作为G。

# Intro
- 作者说没有训练数据的情况对知识蒸馏这一方面没有得到重视，因此值得研究，感觉motivation不强烈。总体来说，作者也不是特别会讲故事的人。

## Data-free Student Network learning
- 总的来说训练分两步：首先，训练GAN，因为没有训练数据，那这个G是如何训练呢：1）fake label，用D生成的标签作为伪标签，也说了这个G不是为了生成逼近真实的照片，而是生成符合D的分布的照片；2） activation loss function = -1/n ||fti||，就是feature map的一范数；3) information entropy loss=-h(1/n * sum(yi)), ，h是信息熵公式，yi是Teacher网络的logits，信息熵在每个类别均等时最大，因此，这个这个loss是为了让生成的每个类别的概率平衡，总之还是第一次见这种loss
- 第二步，就是用GAN生成的图片一同训练，标准的KD过程了

## Experiments
- 验证了一下这三种loss一起的效果是最好的，在minist上

