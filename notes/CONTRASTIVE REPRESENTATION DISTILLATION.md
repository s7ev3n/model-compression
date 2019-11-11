<!--
 * @Description: 
 * @Date: 2019-11-11 10:47:02
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-11-11 17:05:44
 -->
# CONTRASTIVE REPRESENTATION DISTILLATION

## Intro
- 他说用KL做KD的loss忽略了各项(i和j项)之间的关系，然后提出了constrastive obejcetives来捕捉输出各项的高层的关系。
- 他的假设是output每项是独立的，而我之前认为整个output是某个分布，不同项的概率反应的是各项之间的similarity。这个假设需要思考
- 好处是做了很多其他KD方法的实验作为benchmark，然后开源了代码，我们做的Res110和Res20也有了参照
- 另一个优点是，作者在KD的应用model compression，cross-modal transfer，Ensemble都做了实验，把这三方面同时列出来我还是第一次见

## Method 
- 推导了如何得到作者的constrastive loss，这个loss他说是为了最大化teacher和student的互信息，推导了了一个下界并maximize这个下界，具体看一下论文
- 这个contrastive来自这篇Representation Learning with Contrastive Predictive Coding，提出了一个infoNCE loss
- 关于互信息，https://machinelearningmastery.com/information-gain-and-mutual-information/

- 实验室中，作者的CRD loss，大约相对KD提高0.5-0.8个点左右，在各种setting都稳定地高出其他方法
- 实验与InfoNCE的方法也做了对比，但没看infoNCE有些一头雾水

