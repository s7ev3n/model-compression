<!--
 * @Description: 
 * @Date: 2019-10-16 10:34:55
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-16 15:08:22
 -->
# LARGE SCALE DISTRIBUTED NEURAL NETWORK TRAINING THROUGH ONLINE DISTILLATION

## Abstract
- ensemble and distillation都可以带来模型的performance提高. 但是ensemble在infer时候太费时, distillation训练过程太复杂, 不适合large-scale的工业级的场景. 这个online distillation的实用性为主, 可以对大数据集训练加快速度; 可以增强复现性 ??

## Introduction
- distributed SGD的worker的规模是对performance提升的边际效应是很有限的, 100个worker之后就很难再有什么提高了
- 到达worker的限制之后, ensemble方法还是可以带来性能提升, 一种是多个模型同时inference, 输出再ensemble; 另一种是把n个模型ensemble到一个, 这种有两阶段: 第一阶段, 假设用100台机器训练5个模型, 共用到500台机器, 训练好5个后, 再用100台机器训练一个Student模型来mimic这5个模型的Ensemble


## Codistillation
- stale是陈旧的,老的
- 提出了online distillation方法codistillation, **同时**并行训练n个模型, 每个模型loss项多一个项, 第i个model去match其他所有model的prediction的平均值, 不过不是一开始就加这个loss, 而是并行训练的模型有一定准确率之后再加
- 128个GPU是最好的数目, 256个GPU增益就非常小了
- 图2的实验, 证明了这样的训练方式, val ce loss下降接近2-way Ensemble, 即训练两个模型, 并用两个模型infer再平均
- 图2b的实验还比较了codistillation时training set是否相同, 当数据集不同时候比数据集相同时, 结果更好, codistillation时的确是transfer了不同训练数据间的information