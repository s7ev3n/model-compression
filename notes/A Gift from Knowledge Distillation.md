<!--
 * @Description: 
 * @Date: 2019-10-14 19:52:20
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-15 11:04:39
 -->
# A Gift from Knowledge Distillation: Fast Optimization, Network Minimization and Transfer Learning

## Abstract
最大贡献是学习跨层之间的称为flow的distilled knowledge, 达到了1)更快速收敛;2)student效果超过teacher

## Intro
- Gram matrix是feature vectors的内积, 包含feature的方向性; 作者就是用不同层的feature之间的内积来定义前面说的flow. 他说不同是这个计算是不同层之间的, 有些是计算一层之内的. 起了个名字角 flow of solution procedure
(FSP) matrix.

## Method
- 解释为什么用两层layer之间的内积定义knowledge的说法有点扯, 居然拿人学习做类比...
- FSP矩阵定义是(hwm x hwn = mn), 两个矩阵相乘
- loss是Student和Teacher之间的两个矩阵的2范数
- 学习过程: 1. 最小化S和T之间FSP矩阵的loss; 2.student网络再接下来正常训练, 可以与Teacher相同的任务也可以不同

## Experiments
- 第一个fast optimization实验, 用了一个和teacher一样的结构的student进行学习过程1, 训练过程2继续正常的与label进行训练, student网络比teacher收敛更快, 大概用1/3的iters就达到了相同结果, 最后结果更好超过了teacher; 这里最小化FSP只是为了initial weight?

