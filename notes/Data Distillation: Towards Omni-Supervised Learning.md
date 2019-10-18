<!--
 * @Description: 
 * @Date: 2019-10-18 10:15:29
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-18 20:37:10
 -->
# Data Distillation: Towards Omni-Supervised Learning
## Intro
- 文章用了一个词omni-supervised learning, 想用有限的labeled data训练的模型来对无限的unlabeled data做annotations,进而利用生成的annotations提高, 像是self-training
- self-training一般没有新信息
- 模型在inference时, 对输入进行transform, 并合并结果, 可以得到性能提高-->transforms包含nontrivial的信息, 模型的一次预测没有捕捉到; 这个assumption是文章基础
- 实际上, 语义分割时候, 不同尺寸input的prediction结果ensemble的确是会提高的

## Related work
- 文献26从privileged information的角度来讲KD, 看到多次了,应该看一下
- 

## Data Distillation
- 模型正常在labeled data训练-->用这个训练好的模型对unlabel data每种transformation的数据生成prediction-->ensemble各个prediction生成labels-->labeled和生成的labeled的data的合集进行训练
- 他认为叠加多个prediction实际上是产生了新的信息, 这写信息可以用来训练自身; 等等, 让我再想想这个假设

- 训练混合dataset时, 每个batch要保持人工label的data和生成的label的data都要有

## DD for Keypoint Detection
- transformation是几何的形态变化, resize和水平翻转
- 水平翻转, detection box对resize和水平翻转是一致的没啥问题, 但是keypoint heatmap对水平翻转来说不是相反的吗?heatmap可以直接相加吗?
- prediction的选择是要高于一定阈值的(prediction score), 扔掉质量差的; 但是false negative处理, 最后说其实影响不大
- labelled数据和生成label数据采样比6:4
- retrain比再原来模型finetune要好

## Experiments
- 大规模实验, unlabel data和label data相似的distribution, DD效果提升非常明显, 1.7-2个点的AP
- 大规模实验, unlabel data和label data不相似的distribution, 对比baseline有提高, 但是对比上面的实验提高幅度要小
- 如果可以对比DD和原始模型多个transformations ensemble的结果就更好了
- 做了training iterations的abalation study, DD训练在90K步之后, 几乎都是超过原始
- unlabel数据也是越多结果越好, 但是估计unlabel数据带来的提高肯定有上限
