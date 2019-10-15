<!--
 * @Description: 
 * @Date: 2019-10-15 10:52:58
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-15 13:53:26
 -->
# Relational Knowledge Distillation

## Intro
- student学习structural relations of outputs, loss提出两种distance-wise(second-order) and angle-wise(third-order)

## Method
### Relational knowledge distillation
- computes a relational potential ψ for each n-tuple of data example
- 下面提出两个potential function

#### 1 Distance-wise distillation loss (RKD-D)
- 一个batch内每个logit的距离, 二阶, 用平均距离作为normalization参数
- student和teacher之间的loss, 是Huber loss, |x-y| <= 1时, =1/2(x-y)^2, otherwise, =|x-y|-1/2

#### 2. Angle-wise distillation loss (RKD-A)
- 3个logit会形成一个cos角度, 就是两个向量的角度
- loss还是Huber loss

- 这些RKD loss还是必须要和task-specific loss 或者正常KD loss一起使用的.
## Experiments

### Metric learning
- Metric learning aims to train an embedding model that projects data examples onto a manifold where two examples are close to each other if they are semantically similar and otherwise far apart.
- 训练Metric learning的baseline是Triplet train from scratch, 还有FitNet, 和Attention, DarkRank方法; RKD是在这个实验是单纯的RKD, 没有task specific loss
- 结果显示, RKD方法比其他Triplet, FitNet等都要好, DrakRank是KD方法, RKD就没有高出特别明显了; 
- Table1 算是Ablation study??
- self-distillation在第一次自蒸馏会比自身提高, 2nd和3rd就下降了
- 感觉RKD对metric learning这个任何是很适合的, 所以没有用task specific loss

### Image Classification
- RKD作用在student和teacher最后一个pooling层之后, 也加上了和gt的cross entrophy loss
- RKD加到了HKD, FitNet, Attention
- 对这些都有小幅地提高,
- RKD是对KD或其他方法的complementary
