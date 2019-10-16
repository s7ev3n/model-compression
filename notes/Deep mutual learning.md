<!--
 * @Description: 
 * @Date: 2019-10-16 11:19:42
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-16 16:27:42
 -->
# Deep Mutual Learning

## Intro
- 这篇和LARGE SCALE DISTRIBUTED NEURAL NETWORK TRAINING THROUGH ONLINE DISTILLATION的方法基本是一样

## Deep Mutual Learning
- 如果有两个Model, 训练的模型1的loss:LΘ1 = LC1 + KL(p2||p1), 模型2也同理: LΘ2 = LC2 + KL(p1||p2); 但是更新参数Θ是交替进行的

## Experiments
- Table2的实验是两个Model结构不相同, 但是DML也可以对原模型有提高
- Table4实验, 和KD做对比, 显示的是比KD要好, MobileNet和MobileNet做DML, 比原始的MobileNet还高
- 总觉得有点奇怪

### How and Why does DML Work?
- 他说DML找到了一个更wide的minima, 证明是加了相同的noise, loss值的提高, MDL比independent的要低; 更宽的minima会有更好的generalize ability
- How a Better Minima is Found? has connection to entropy regularisation-based approaches [4, 17] to finding wide minima
这里有些不理解, 可以参考下文献4,17
- the results of ensemble rather than peer teaching are worse