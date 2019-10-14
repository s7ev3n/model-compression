@(论文2)[Knowledge Distilling, Action Recognizing, Multi-Modal]
#MARS: Motion-Augmented RGB Stream for Action Recognition
##Introduction
* 做动作识别的
* 说有些人用双流方法，rgb+光流，有些人用3d cnn，有些人用rnn。这篇文章是双流的
* 说提取光流的方法很慢，然后只用rgb性能又不好。
* 具体的思路是这样的：首先训练出一个正常的光流分支（多帧图片->传统方法算光流->输入到做光流的网络->获得光流feature），然后让一个3dcnn，输入为多帧图片，输出自己的feature。他要最小化这两个feature的距离，即__相当于是在用光流提取（传统方法）+特征提取（网络）作为teacher去教student（网络）__
* __其实我觉得更general的理解应该是，有a，b两个模态的数据（不用那么严格，可以是如caption、图片甚至是a角度和b角度的图片）teacher用数据i的a模态数据i_a学到了feature a，去教student用数据i的b模态数据i_b学feature a（而不是feature b）__
##Related Work
* 提到了一些通过用3d卷积网络来计算光流的方法。说实际上光流这个事情不太适合用网络来做，因为gt太少太珍贵，不足以训练网络。而如果用合成数据就有gap，用传统方法做标签就相当于设定了上界
* 提到了网络非监督方法算光流【49】
##Learning to Replace Flow
* 他是从privileged information的角度讲的，即训练teacher的时候有光流数据，而训练student的时候没有。
* 分成两种。一种是2-stage的，MERS。就是上面说的那个，先跟着光流分支学feature提取，然后再单独训练分类器。然后是1-stage的，MERS，即一边学着特征提取一边用label训练分类器，这个样子就能学到更好的feature。
* 他说后者更好是因为同时学到了rgb特征的提取和分类，因为如果不看kd那部分损失的话，就完全是rgb那个分支的结果。我感觉这其实无所谓，也很难说到底学到了啥，感觉1-stage这反而是比较直观的想法
##Results and Discussion
* 为了证明确实学到的是光流特征（动作特征）而不是类似的rgb特征（appearance 特征），他把clip变成了静态的，即用相同的帧填充clip。发现真正的rgb分支影响很小（同时也说明了rgb分支虽然也是3d卷积，但是真正关注的只是appearance信息而已），而模仿光流分支的那个student网络，虽然输入跟rgb分支一样，但是受此影响极大。
* 