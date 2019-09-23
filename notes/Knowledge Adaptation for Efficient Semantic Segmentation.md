<!--
 * @Description: 
 * @Date: 2019-09-20 19:34:01
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-09-23 09:41:50
 -->
# Knowledge Adaptation for Efficient Semantic Segmentation

## Introduction
* 贡献1：一般student会逐层地监督学习teacher的中间表示。本文，将teacher的中间表示用autoencoder学习后表征成一个compact knowledge，student学习这个。这个点挺有意思的。

* 贡献2：affinity distillation module regulate relationships among widely sperated spatial regions between teacher and student.
* 作者说它最终的feature map比8还小，用low resolution可以实现很好的精度。

## Related work
* 现有的用于图像分类的方法，即直接学习中间表示，不适用与语义分割任务：1）忽略了两个模型capture context和long range dependency的能力，而图像分类都是global的信息；对语义分割任务，decision space或许是不同的；2）超参数T对任务很敏感，不容易调。

## Proposed Method
### Knowledge translation and Adaptaion
* autoencoder是对称的3层encoder和3层decoder，loss除了recontruction loss外，另外加了l1正则项，a*||E(x)||，a=10e-7;
* student的输出加了一层卷积层(feature adaper？？)再和teacher的autoencoder的输出做loss，adapt loss
* adapt loss是normalization后的adapt和decoder输出相减

### Affinity Distillation Module
* 这个Affinity Distillation Module其实是一个loss，因为是相似，所以pixel with different labels generate lowrepsonse and high for the same labels
* 是两个(h*w)*(h*w)的矩阵之差

## 总结
1.利用autoencoder很新颖
2.很小的feature map，缩小16倍，实现这个效果让我很惊讶
3.affinity distillation这个地方我并没有很理解：1）一个是公式中如何体现的相同label更大response，2）一个是它如何实现了long range dependency，(不理解long range dependency到底是什么意思）
4.结果上在val(500张)上71，在test(1500张)72.7，因为test上是pretrain+trainval