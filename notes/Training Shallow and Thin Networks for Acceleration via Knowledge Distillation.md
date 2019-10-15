<!--
 * @Description: 
 * @Date: 2019-10-14 21:14:24
 * @Author: s7ev3n
 * @Github: https://github.com/s7ev3n
 * @LastEditors: s7ev3n
 * @LastEditTime: 2019-10-15 10:50:52
 -->
# Training Shallow and Thin Networks for Acceleration via Knowledge Distillation

## Intro
- 文章关注的是shallow and thin的S从一个deep and wide的网络学习??
- 让student来mimicteacher的soft logits没必要, 作者用GAN来学习一个loss, GAN可以帮助 preserve the multi-modal 1 nature of the output distribution. 这个听起来很有意思!
- 虽然Intro的motivation写作不好, 但是整体语言非常流畅非常好

## Learning loss for knowledge distillation

### 3.3  Learning loss with adversarial networks
- 用一个D, 输入student和teacher的logits, 然后来区分哪个是student的,哪个是teacher的, student相当于generator, 它学习来fool discriminator, 这个设计很好耶!!
- 这样子简化了loss, 据说相比调temperature这个比较敏感的参数, 调整student要好的多
- 问题也有, 只有这个GAN loss的化, 训练过程很困难, 很慢很不稳定; 尽管student和teacher的输出分布很近, 但是会出现student预测的是完全错误的情况;
- 因此,在上面的GAN loss之外, 又加了一个对正确label的loss项
- 讨论了更新student和更新discriminator的loss

### Experiments
- 在cifar上, 作者的GAN loss比KD要好不少, KD对T这个超参数的旋转还是比较敏感的
- 只有GAN loss和Label Loss相对会比较差, 几个loss一起最好
- 作者可视化了一个测试集上的supervised student, GAN-loss student和teacher的输出的histgram, GAN-loss和teacher的histgram图看起来更接近;
但是为什么不是KD-loss的和GAN-loss student做对比呢??