@(论文2)[Knowledge Distilling, Gramian Matrix, Gram Matrix]
#A Gift from Knowledge Distillation: Fast Optimization, Network Minimization and Transfer Learning
##Introduction
* 普通的知识蒸馏是用student去逼近teacher的feature或者logits，但是这篇想要逼近的是flow，即feature从这一层到那一层是怎么变化的。
* 提到了gramian matrix。这个矩阵是由n个向量的内积组成的n*n的矩阵。可以用来反应向量之间的一些关系（如，正负表示各个向量方向的关系）。好像风格迁移里有用这个矩阵来衡量某些metric的。
* 提到了【6】，好像就是做风格迁移的。这里简单理解一下好像是用gram矩阵来表示feature各个channel向量之间的关系，从而这个矩阵可以在一定程度上表示feature的某些特征（他说映射到图片上就是图片的纹理特征）。从而可以用两张图片的feature的gram矩阵来计算某个相似度
* 他说他也是用这种思路。比如，第k层（m个channel）和第j层（n个channel）的feature就可以计算出一个m*n的矩阵（叫flow of solution procedure，FSP matrix），让student和teacher的这个矩阵尽量相似
* 这篇文章因为发的比较早，所以还讨论了下知识蒸馏的意义，包括可以作为一种初始化方法，可以模型压缩，可以知识迁移
##Method
* 他的想法就是，普通知识蒸馏只要结果对就行，但是过程不一定对。这里就是通过这种方式去学习各层之间的变化趋势，保证过程也相同
* 其实跟逐层的确保每一层特征都一样感觉差不多，只是相比较而言更加自由__我感觉这种自由是挺重要的，看到有几篇都在讨论teacher的最优解是不是并不是student的最优解，本质上就可以通过提升学习的自由度来决定。感觉这是一个trade off__
* 做法就是把两组feature各自的每个channel都当成向量做点积，然后组成一个矩阵。
* 损失函数就是student和teacher对应矩阵的差的2范数
##Experiments
* 用resnet做实验，就可以按照feature的size划分section，学习进section到出section的变化。
* 他讨论了如果feature map的size不一样的时候可以对大的做池化，但是一直没讨论如果student和teacher的对应的channel数目不同的时候该怎么办。不晓得1*1卷积改channel数行不行
* 他这里也讨论了一下上面说的那个自由度的问题。不过他是从ensemble的角度来说的，说这样子训练出来的多个模型因为更富有多样性，会让ensemble时的性能有提升
* 他还提出了一个很有意思的增加student的多样性的方法，就是改变teacher计算fsp矩阵的方法，这个样子student学到的计算法就有很大变化了。
* 提到了一点迁移学习，其实就是相当于预训练之外找到了一种新的方式，这篇文章比较早所以单独说了这种方法。用的是鸟类数据集（CUB）做的实验。