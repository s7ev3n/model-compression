<!--
 * @Description: 
 * @Date: 2019-10-22 10:28:01
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-22 16:57:53
 -->
# ACCELERATING DEEP LEARNING BY FOCUSING ON THE BIGGEST LOSERS

- 提出Selective-Backprop算法, 来决定是否采用一个example来计算梯度并反向传播更新; 这个有老傅的一个batch内选择样本有神似之处, 但目的和结果都不一样
- 一篇写technique的文章, 用类似难例挖掘的假设, 用来做加速
## Intro
- 假设: not all examples are equally useful, hard贡献多
- SB的做法是:prioritizes gradient updates for examples for which a forward pass reveals high loss, probabilistically skipping the
backward pass for examples exhibiting low loss.
- 优点是, 带来的更快的收敛速度, 达到相同的正确率大约可以减少一半的时间
- SB是每次forward pass都评估sampling probabilities, 还有一个每隔n epoch做一次评估的称为Stale-SB, Stale-SB速度比SB更快一些
- 一次反向传播计算用时大约是前向传播的一半
## Related work
- 和Importance sampling对比, IS对rare example更多的抽样, 需要维护一个history
- Katharopoulos & Fleuret,2018这篇文献和SB比较像
- 最后和样本选择相关的, 例如importance sampling和curriculum learning列举和分类的很多文献

## Loss-based sampling with selective-backprop
- 算法的过程大致是: 采样Batch个样本, 计算每个样本的loss, SB计算一个probability,输入是loss, probability会决定是否采用这个样本来计算梯度并反向传播; 这里比较反直觉: 一般一次采样Batch个样本, 剔除一些, 这样是不够一个完整的batch的, 那就再采样一次, 凑够了Batch个就来一次梯度计算和反向传播; 
- 疑问是凑够Batch个满足probability的样本, 用每次采Batch个, 肯定会多出来满足要求的样本,这些样本是存下来吗, 还是扔掉??
- 计算probability是用CDF, 累积分布函数(Cumulative Distribution Function), 是概率密度函数的积分

## Implementation
- CDF-R是有界队列
- 前向pass计算每个sample的loss-->add to CDF-R and 计算这个loss的percentile--> input percentil and calculate selection probability --> add this probability to example for training
- SB中data组织成selection minibatches
- 对选中的sample, 要有两次forward pass计算, 一次用来筛选, 一次用来training, SB的筛选是和训练代码并行的; 感觉不对啊, 并行的话, 怎么凑满一个batch?
- 好多实现细节论文没说清楚, 看得云里雾里
## Experiments
- SB可以更快到达给定的Test error
