<!--
 * @Description: 
 * @Date: 2019-10-21 14:42:32
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-21 15:57:52
 -->
# Like What You Like: Knowledge Distill via Neuron Selectivity Transfer

- 把knowledge transfer看作是distribution matching问题, 然后提出了一个新的loss来最优化Maximum Mean Discrepancy

## Intro
- 这个假设也挺有意思: The intuition behind this model is rather straightforward: Each neuron essentially extracts a certain pattern related to the task at hand from raw input. Thus, if a neuron is activated in certain regions or samples, that implies these regions or samples share some common properties that may relate to the task. Such clustering knowledge is valuable for the student network since it provides an explanation to the final prediction of the teacher model. As a result, we propose to align the distribution of neuron selectivity pattern between student models and teacher models.

## Related work
- Domain adaptation属于transfer learning, 目的是提高一个在source domain训练过的网络在unlabelled target domain(相似但不同的)中提高testing performance, 即核心是拉近两个domain的distributions
- MMD是这个领域广泛应用的criterion

##　Background
- MMD用来描述两个distribution距离的指标
- 计算ＭＭＤ时候，用的是fi是HxW的，即代表经过激活层的
- 也提到了为什么不直接matching feature maps, 即FitNet那样, 作者说,经过激活层之后, 是neuron select之后的pattern, 这个sample distribution反应了网络到底关注的pattern
- kernel选择了三种: linear, poly, gauss
- 激活函数就只有ReLU

## Experiments
- NST相比FitNet直接match feature maps的结果要好, kernel的话poly最好
- KD+NST取得比较好的结果
- 没有直接比较match feature maps(FitNet做法)和NST的对比, 只有间接和FitNet对比, 有一些不公平
