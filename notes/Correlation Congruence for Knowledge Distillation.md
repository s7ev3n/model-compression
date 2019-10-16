<!--
 * @Description: 
 * @Date: 2019-10-15 15:44:59
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-15 22:02:30
 -->
# Correlation Congruence for Knowledge Distillation
Congruence是相似一致的意思

## Intro
- 大多数方法 only focus on instance congruence, 但是作者说没有coorelation between instance
- instance 我觉得指的是训练时候的一个sample
- 文章应该是学习一个batch内类间关系的
- 他说这个instance间的关系对分类也很重要

## Method
### Correlation Congruence KD
- 两部分, instance congruence(KL of T and S), correlation congruence(euclidean distance on correlation of T and S)
- psai: F --> C (nxn), 这个psai映射是从F(a set of feature f)到一个nxn矩阵, 
- C中的每个元素是由函数fai: fai(fi, fj)得到的, 这个fai是核函数, 他用了Gaussian RBF
- Lcc loss = 1/n ||Cs - Ct ||, 就是两个矩阵的每个元素的距离
- 最后总的loss: Lcckd = a*Lce + (1-a)\*Lkd + b\*Lcc
- 最小化S和T之间每样本之间的距离感觉有点奇怪, 和用GAN作为区分T和S的Discriminator相比, 我觉得优化S和T instance之间的距离约束有些太强了

### mini-batch sampler
- batch 采样会带来比较高的high bias
- 两个sampler: class-uniform random sampler (CUR-sampler)和superclass-uniorm random sampler (SUR-sampler); CUR就是一个batch里固定选几个类别每个类别几个; SUR就是聚类采样

## Experiments
### Ablation study
