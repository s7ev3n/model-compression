@(论文2)[Knowledge Distilling, GAN]
#Training Shallow and Thin Networks for Acceleration via Knowledge Distillation with Conditional Adversarial Networks
##Introduction
* 提到了这么几种蒸馏：浅而宽的学生与较深的teacher（teacher的参数甚至更少）【3，40】；多teacher与单学生（就是最开始的一篇，多teacher 进行ensemble，用logits做蒸馏）；深且窄的student与浅且宽的teacher；
* 他把知识蒸馏分成了上面几类，然后说自己专注于研究student 浅且窄，teacher深且宽的那种。感觉这不是主要创新，他的主要创新应该是用条件gan去做知识蒸馏
* 他这里说之所以用gan而不是传统的kd loss，是因为本来就没必要完全学习teacher的每一个数值，因为student的能力确实有限。而gan大概相当于是平滑之上的又一次平滑，学习的是更高的模态。我感觉这个解释我是可以承认的。
* 他举例子就是，teacher学了个（0.7，0.3，0），学生学个（0.8，0.2，0）也挺好的，只要别搞个（0.7，0，0.3）这种就可以
##Related work
* 感觉比较重要的都在前面提了
##Learning loss for knowledge distillation
* 结构就是resblock的组合。可以通过控制每个block里的通道数来让teacher网络变宽，控制block数让它变深
* 这里提到temperature很大的时候，kd loss会接近于欧氏距离
* 感觉韩国的那篇kdgan就是从这篇来的，这篇就是用一个Discriminator来判断这个logits是来自于teacher的还是来自于student的，用对抗loss代替kd loss。然后kdgan说觉得这个方法收敛很慢，所以在这个基础上又把kd loss加回去了。
* 注意这一篇文章他的Discriminator没有输入真实图片，输入只是真实标签和logits。
* __因此我认为Discriminator学到的应该是类别之间的关系，而不是具体到某个图片的相似程度（即，学到的是所有猫和所有狗都很像，而没法学到这张图里的猫长得像一只乌鸦）。这应该是这篇文章主要的亮点，也就是类间关系的学习。__
* 他提到这样做的一个好处是对超参数不敏感了
* 上面说错了，输入的不是真实标签，只有logits。他是用真实标签作为condition，即Discriminator要通过输入的logits预测他的来源（是student还是teacher），同时预测这个logits对应的标签是啥。
* 不过感觉没什么很本质的区别，都是为了防止student坍塌，不管什么类别，都只模仿一种类别的输出模式。
* 这里有个问题，他也要求Discriminator去依靠student的logits预测类别。我感觉这个是有点问题的。
* 还讨论了一下real/fake跟分类是不是相互独立的（是不是real情况下的类别的分类器跟fake情况下应该不一样，因此应该分别用两套输出的FC层）。实验证明无所谓。
* 他也有进一步的研究，即能不能Discriminator学习更多的内容，除了类别关系之外还能搞一点样本级别的关系。他试着把一些其他内容输入到Discriminator里（比如，用一个网络把图像压缩成与logits一样长的向量，然后和logits拼在一起送进去），但是发现这些信息都被忽略了，没有用。
* 诶这篇文章也加了类似于kd loss的损失在student里面（只是l1距离），所以kdgan感觉跟这篇重合很大呀
##Experiments
* Discriminator就是一个三层感知机
* 这里有些我比较感兴趣的内容：关于temperature，从1到10基本上是越大越好。听说设到20是比较合适的。但是这里只做到了10
* 他说训练的准确率，他的方法比正常的会低；但是测试的时候就会更高。
* ablation study，只有gan的损失是很差的。而在基础为只用label学习的情况下，gan带来的收益没有kd的收益大，只是二者可以叠加
* 关于discriminator，在这个任务中同样不是越强越好。
* 做了实验，发现（在一定范围之内）增加深度比增加宽度（channel数目）效果好
* 好像确实他的方法能让student学的更像teacher