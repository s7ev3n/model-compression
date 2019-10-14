<!--
 * @Description: 
 * @Date: 2019-09-26 10:02:53
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-14 10:20:09
 -->
# Born-Again Neural Networks
## Introduction
- Born-Again trees用ensemble模型训练出a single model比他们的各个集成模型都好
- 很惊奇的发现,用一个和teacher一样的student模型蒸馏自己,student会变得更好;
- 这里的loss有两项,一个是kd项, 因为有soft参数t, 所以kd是包含错误分类的信息的; 一个是和gt的, 这里比较有意思, 他把gt解释为using importance weights for each sample based on
the teacher’s confidence in its maximum value

## Related work
- 模型压缩和decision tree结合的工作也有不少
- KD用来解决forgetting in continual learning,这个topic感觉有点意思
- self-distillation自蒸馏
- 突然想到这种自己蒸馏自己,算是用soften softmax,不断降低对噪音的不确定性,继续训练吗??

## Born-Again Networks
- sequence of teaching selves, 不断用自己训练出来的自己蒸馏自己, 然后ensemble就是平均多个蒸馏出来的自己, 可以取得提高
- 试图解释什么是Dark Knowledge
作者的角度挺有意思, 把kd loss的公式做了拆解, 分为onehot中1的那一项,(注意不是1,是teacher输出的对应1的那一项), 和其余项
soft logits很像是对label做了importance weight
- 作者问了两个问题针对dark knowledge为什么有效:
1. 因为non-argmax outputs of teacher, gt不是1的项的贡献?
2. 还是说因为dark knowledge是因为importance weight有效??
针对此设计了两组实验:
1.针对第二个问题 Confidence Weighted by Teacher Max, 即student的zi乘以了teacher的预测概率, 再做标准的cross enthrophy
2.针对dark knowledge with Permuted Predictions (DKPP), 这次把除了teacher输出的max的那项的其余non-argmax项打乱
实验设计挺有意思的


## Experiments
- Table2, 总体来说, 用了BAN子蒸馏, 在各个模型的提高都是很明显的. 他想讨论的CWTM和DKPP, 好像DKPP提高了, CWTM的结果较差.
- DKPP demonstrates that the higher order moments of the
output distribution that are invariant to the permutation procedure 因为DKPP打乱了, 所以他说输出的高阶矩对打乱是invariant的
- 连续的BAN, 可以看到很微小的提高, Ensemble了结果好很多
- 为什么加了label supervision, 即BAN+L, 相比BAN会一致的下降
[总体上,还是没太清楚BAN, BAN, CWTM, DKPP这几个实验的区别]