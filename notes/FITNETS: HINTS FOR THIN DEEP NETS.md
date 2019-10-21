<!--
 * @Description: 
 * @Date: 2019-10-17 14:23:02
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-21 14:30:30
 -->
# FITNETS: HINTS FOR THIN DEEP NETS
- 几个文章的related work都提到了这篇2015年文章, 看一下
## Intro
- 文献是利用KD训练一个thin and deep的student网络, 从wide and shallower的teacher网络; 为什么是deep and thin, depth重要, depth太深不容易训练,应该是ResNet之前的文献了;
- 用teacher的intermediate-level hints来训练, 所谓的hints就是teacher的某个层的输出, 就是feature层的训练
- thin和wide都是相对于channel来讲的
## Hint-based training
- 选择了teacher的中间层来进行, 用中间层来进行Student网络的训练是一种形式的正则化(hints is a form of regularization), 某种形式的正则话说法不是很严谨
- FitNet的guided layer需要和Teacher网络某中间层(hint layer) spatial size和channel都不同的情况下, 加了一个regressor层到guided layer上使他们相同; regressor选择卷积核的大小来控制, 输出与hint layer的spatial size和channel一致.
- 先用hint layer来训练guided layer, 作为pretrain, 然后再用KD

## Experiments
- 同时用hint layer和KD一起训练, 没有work
- thin and deep的FitNet用BP直接训练没法超过5层, 用KD可以到7层, 用作者的Hint Learning可以到13层; KD和hint learning的区别就是hint learning用中间层预训练了,有较好的初始化的参数和local minima; 
- pretrain可以相当于regularizer, HT可以看做一个比较强的regularizer