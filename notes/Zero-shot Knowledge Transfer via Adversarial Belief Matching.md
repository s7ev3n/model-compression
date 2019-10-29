<!--
 * @Description: 
 * @Date: 2019-10-28 15:11:11
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-29 10:28:22
 -->
# Zero-shot Knowledge Transfer via Adversarial Belief Matching

## Intro
- 本文是针对无data的distillation, 通过对抗方式训练生成器生成teacher和student的不match的样本,然后用这些样本来训练student; 这和用generator生成与teacher相似的Data-free那篇论文正相反; 看Figure1, 非常像是supporting boundary那篇论文的想法, 即生成的是decision boundry附近的样本用来训练, 即让student学习decision boundary的信息

- We achieve this by training an adversarial generator to search for images on which the student poorly matches the teacher, and then using them to train the student.
## Related work
- inducing point method, 从叙述上看是用来生成pseudo data

## Zero-shot knowledge transfer
- 他的训练过程还挺有意思, 即先用一个generator来生成teacher和student之间KL散度最大的样本得到样本xp, 再用样本xp来训练正常的KD, 拉近teacher和student之间KL散度
- toy example训练发现, 最大化teacher和student之间KL散度, Pseudo data基本都是沿着decision boundary, 再用这些点来训练, 可以让student更清楚地得到临界点的信息
- KL散度在两个预测的向量预测完全不同的类别(例如[0.99, 0.005, 0.005]和[0.005, 0.005, 0.99])时值最大, 即一个样本如果让Teacher和Student预测完全不同的类别; 一般情况下, 因为决策面相当于临界点, 这个样本可能是在Teacher的决策面附近的
- 有没有其他情况, 除了决策面附近, 也会出现KL最大?? 毕竟生成器最开始是噪音啊, 最开始Teacher对噪音会预测出来什么???

### Potential conceptual concerns
- 这个章节很好, 解释一些很可能被质疑的点. 
- 第一点是也是我的疑问, 最大化KL散度一定会生成有意义(在决策面附近)的样本吗? 他的意思是说teacher可能会prefer探索它没被训练过的输入空间
- 他做了一个实验超出我的理解: 对CIFAR-10网络输入1000张uniform分布的噪音伪图片, 网络的预测只集中在非常少数的类别, 甚至有些网络对噪音只预测一个类别. uniform noise我的理解是input space中是均匀的, 如果绝大部分都预测成一类, 它的解释是决策boundary在实际图片外的input空间比较小. 这点再让我想想. 他接着解释到, 如果在实际图片外的空间, teacher太predictable了, 生成器G就会很难找到样本骗过student.

- 第二个问题是对抗的问题, 担心生成器G只生成一些类似对抗样本(改变几个像素就会改变分类), 他也没深入解释

## Experiments
- 第一个实验的M samples per class怎么理解, 怎么训练的
- 有图片展示生成的样本, 随着训练深入, 样本从最初的纹理, 到很高频的pattern, 高频pattern是什么意思???
- the average probability of the class predicted by the teacher is about 0.8. On the
other hand, the most likely class according to student has an average probability of around 0.3. These confidence levels suggest that the generator focuses on pseudo data that is close to the boundary decisions of the student, 这个是什么意思??

### Measuring belief match near decision boundaries
- pj curve这个实验挺有意思的啊