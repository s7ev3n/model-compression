<!--
 * @Description: 
 * @Date: 2019-09-23 10:48:46
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-14 19:37:27
 -->
# Improved Knowledge Distillation via Teacher Assistant: Bridging the Gap Between Student and Teacher

## Introduction
* 作者观察到surprisingly a student model distilled from a teacher
with more parameters and better accuracy performs worse
than the same one distilled from a smaller teacher with a
smaller capacity。因此提出了teacher的中间模型:Teacher Assistant Knowledge Distillation

## Related Work
- Distillation Theory 这一类文献主要是讨论Dark knowledge到底是什么, 为什么会work, 为什么比train from scratch要好.

## Assistant based Knowledge Distillation
* 作者提问如果student网络固定，给一推大模型，选哪个大模型？ 直觉是选最大，准确率最高的，但是，作者用一个toy实验说并不是选最大、准确率最高的越好，因为：
- 小模型没有能力学习到大模型的的表示的全部
- 大模型越精确，对data的不确定性越小，小模型越难从soft target中学到东西
这个motivation很好

* 所以作者搞了个中间的、鉴于大模型和小模型之间的TA model。

## Experiments
- 第一个实验讨论S=2, T=10的情况下, TA(4,6,8)哪个作为TA, S的效果最好, 结果是4最好, 为什么呢? 解释说是因为4最接近average accuracy而不是averagesize, 所以6不好. 这种说法没有说服力吧.
- 为什么用TA,因为TA比直接T差,但是比train from scratch好啊, TA的准确率是介于中间的
- 为什么局限一步的TA, 多步的TA不行吗?比如, 前面是10->4->2, 那10->6->4->2呢?

## Why Does Distillation with TA work?
- 他用一个分类误差的上下接来讨论难度, 貌似, 写了挺多但是没看太懂
- 画了loss的landscape图, loss的这个图sharp还是smooth和generalization error有关, 据说.
然后TA的图更Smooth.