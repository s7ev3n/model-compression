<!--
 * @Description: 
 * @Date: 2019-10-23 15:14:39
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-23 17:21:54
 -->
# PAYING MORE ATTENTION TO ATTENTION: IMPROVING THE PERFORMANCE OF CONVOLUTIONAL NEURAL NETWORKS VIA ATTENTION TRANSFER

## Intro
- 两种attention map, activation-based and gradient-based
- 用attention map来做KD; 看到过很多KD关于学习什么内容了: 最基础的学习soften logits的, 直接学习feature map, 学习feature map之间的Gram矩阵, 学习feature map经过autoencoder后的code的, 学习feature map经过activation之后的, 学习一个batch内样本关系一致性的等等层出不穷

## Related work
- 提到了很多解释CNN的可视化方法, 这些可视化的图片应该就是用来transfer的Attention map, 就像NVIDIA之前可视化过的驾驶关注区域的文章

## Attention transfer
###  ACTIVATION-BASED ATTENTION TRANSFER
- 把CHW的feature map(经过了activation), 沿着C做了三种操作(绝对值求和, 绝对值的幂的和, 选最大值), 把feature map压平到HW, 这就是用来进行transfer的Attention map
- 做了几个把Attention map可视化叠加回原图片的例子来说明不同层关注不同区域
- Loss 是KD+S和T的Attention map的距离, attention map需要除以模长, 他说这点很重要
- 这个Attention map的本质和直接在feature map做有本质的区别吗??

### GRADIENT-BASED ATTENTION TRANSFER
- feature map对输入求导作为Attention map
- 这个loss在计算Attention map的距离时, 需要两次反向传播, 自动求导机
- 对水平翻转的Attention map有特殊的处理

## Experiments
- AT+KD一般会取得较好的结果
- activateion-based的比full activation transfer的结果要好, 这个实验说明这个压平了feature map的Attention map是有用的
- activateion-based比gradient based结果好