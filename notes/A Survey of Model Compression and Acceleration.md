<!--
 * @Description: 
 * @Date: 2019-09-19 20:18:13
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-09-20 19:19:59
 -->
# A Survey of Model Compression and Acceleration for Deep Neural Networks

## Introduction
* 作者分了四类：parameter pruning and sharing, low-rank factorizaton,
transferred/compact convolution filters, and knowledge distillation

* parameter pruning and sharing 去除模型参数中的冗余，应用在卷积层和全连接层，支持从零开始训练和在原模型finetune
* low-rank factorizaton 低秩分解技巧使用矩阵或张量分解，应用卷积层和全连接层，方法直观，支持从零开始训练和在原模型finetune
* transferred/compact convolution filters，设计特殊的结构化的卷积核来减少参数，只应用在卷积层，根据应用而定
* knowledge distillation，应用卷积层和全连接层，对应用和模型结构敏感，只应用在从零开始训练

## parameter pruning and sharing
* 量化，我理解的量化是NV的tensorrt产品，即一层一层将卷积核和全连接层fp32的浮点数值，映射到int8，每个值的映射范围的上下界是通过最小化一个KL散度得到（具体有些记不清了，这个是个开放性问题，如何最优的映射），这样量化后的网络会生成一个weights都是int8的模型文件，不用重新训练。参考：[https://www.weaf.top/posts/c316071b/]
这里的量化，更多指的是乘积量化，参见[http://www.fabwrite.com/productquantization]
* Han的这篇Deep compression: Compressing
deep neural networks with pruning, trained quantization and huffman
coding看一下，另外记笔记
* Towards the limit of network quantization. 这篇发现Hessian weight可以用来衡量网络参数的重要性？？然后最小化Hessian weight量化的损失。看一下

* Pruning and Sharing
剪枝就是在一个已经训练好的模型中去除掉冗余的weights
Sharing，并不十分确定是怎么做。只有一个例子：HashedNets用一个hash函数来group weights for parameter sharing.

* 设计结构化的矩阵
主要是优化全连接层的矩阵。同样没从简介中看出原理。
An m x n matrix that can be described using much fewer parameters than mn is called a structured matrix.

## LOW-RANK FACTORIZATION AND SPARSITY
* 卷积核是4d的张量，张量分解可以达到去除冗余的作用
* 低秩逼近(low-rank approximation)一层一层操作：一层操作后，固定，在操作下一层。
× 低秩分解需要extensive 重新训练

## TRANSFERRED/COMPACT CONVOLUTIONAL FILTERS
* 基于提出的 equivariant group theory：一个转换矩阵应用在f(x)，f是网络或一层，等价于转换矩阵应用在x，再用f；转换矩阵不相同。
it is reasonable applying transform to layers or filters
Φ(·) to compress the whole network models.

## Knowledge distillation
* Teacher-Student, Student model学习Teacher Model的中间表示，用softmax，带temperature。

## DISCUSSION AND CHALLENGES
建议和挑战并没有深刻的见解。