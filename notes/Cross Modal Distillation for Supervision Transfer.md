<!--
 * @Description: 
 * @Date: 2019-10-17 09:32:24
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-17 13:49:35
 -->
# Cross Modal Distillation for Supervision Transfer
- Modal 模态的意思
- 文章写于2015年, 仍然觉得没有脱节的感觉, 写得很好, idea和insight都很好!
## Intro
- 这篇基本就是傅对MARS那篇说可以更大的framework, 跨模态的transfer或者跨模态的蒸馏
- 如何把一个在更大数据集训练的好的模型的表示能力transfer到数据集更小的的任务上?一般在这些训练好的pretrain模型上对新的任务finetune都可以取得很好的结果. 那和这个相似的paradigm, 新任务的data也很少的情况怎么做?
- 作者用在ImageNet上pretrain过的模型, 把中间的mid-level layer蒸馏到新的模型新的任务上, 这些任务包括depth image, optical flow, object detection等等, 相比原来都有提高
- 称为跨模态的supervision transfer

## Supervision Transfer
- 和MARS的训练光流的分支很像, 用它自己的话:"Let us assume we have a modality Md with unlabeled data, Dd for which we would like to train a rich representation. We will do so by transferring information from a separate modality, Ms, which has a large labeled set of images, Ds, and a corresponding K layered rich representation." 
- 通过训练好的模型的feature进行KD, 输入image是paired, 但是是没有Label的, 例如数据集中有RGB和depth image没有label, 有一个模型在RGB已经ready了, 训练一个模型在depth上, 单独训练depth不好, 作者提出, 可以这样训练: rgb输入已经训练好的模型, 另一个模型输入和rgb paired的depth image, 用rgb输入的中间feature作为另一个模型的学习的label, 就是feature的kd

## Experiments
- 在NYUD2的detection任务中, supervision transfer比直接在小数据集训练的(no init)要好, 比直接从RGB网络(pretrain on ImageNet)拿到weights再训练要好(copy from RGB, 这个实验是用RGB的网络fintune吗??那AlexNet*??是什么); 拿一个在ImageNet pretrain的AlexNet并在这个任务训练称为AlexNet*, 相当于用ImageNet的结果再这个任务finetune, 加上supervision transfer, 结果最好; 可是supervision transfer没有和AlexNet*这个对比
- 讨论了在哪些layer 开始supervision transfer好, 越低层supervision transfer结果越差, conv3之后结果差不多, conv3+fc7得到最好的结果
