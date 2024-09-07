---
{"dg-publish":true,"permalink":"/counting paper read/CLIP-EBC_CLIP Can Count Accurately through Enhanced Blockwise Classification/"}
---


title: 论文阅读 CLIP-EBC_CLIP Can Count Accurately through Enhanced Blockwise Classification
author: zaqai
date: 2024-6-10 20:18:03.178614
tags: 

---


> https://github.com/Yiming-M/CLIP-EBC
> 2024.3 arxiv
> 55.0 and 6.3 on ShanghaiTech part A and part B
> 基于分类的计数方法，提出了Enhanced Blockwise Classification框架，现有的基于回归的方法可以无缝地集成到EBC框架中
> EBC在discretization, label correction, and loss function三个方面提高基于分类的计数方法的性能
> 基于EBC，提出CLIP-EBC，完全使用CLIP来生成人群密度图

> 基于回归的计数方法通常是编码器解码器架构，回归密度图
> 基于分类的计数方法，将[0,♾️]的整数分为不重叠的间隔，目标就是将预测的数值分类到正确的间隔。可以增加大数值的样本数量，解决计数值的长尾分布问题


## 方法

### Enhanced Blockwise Classification(EBC)
基于回归生成密度图的方法面临大计数值的样本不足的问题
EBC将计数值分组到bin中，以增加每个bin的样本量，从而缓解了样本不平衡的问题。
![image.png](https://oss.zaqai.com/img/202404161627519.png)
$\{\mathcal{B}_i\mid i=1,\cdots,n\}$  是n个提前定义好的箱子，实际上表示一个整数的范围，如10-20
$\boldsymbol{P}^*:n\times{h}\times{w}$  h=H//r, w=H//r, r是模型相关的放缩因子
$\boldsymbol{P}_{:,i,j}^*$  表示该位置的可能性分数，共有n个值，对应n个箱子
$\boldsymbol{Y}_{i,j}^*=\sum_{k=1}^na_k\cdot\boldsymbol{P}_{k,i,j}^*$  Y是预测的密度图，a是箱子的代表值(如范围的中间值)
现有的基于回归的方法可以很容易地通过只改变输出维度来定制以适应EBC。
![image.png](https://oss.zaqai.com/img/202404161655302.png)

#### 离散化

高斯平滑获取GT密度图的方法，由于人头尺度不同，使用固定的核大小，可能会引入噪声
![image.png](https://oss.zaqai.com/img/202404161706990.png)
作者提出绕过高斯平滑，如果一个人头特定的区域内，只用那个块来预测这个人头的存在，同时排除其他块来做出这样的预测。

因为计数值可能不符合均匀分布，使用中间值作为bin的代表值不是最优的
提出使用每个箱子中的平均计数值作为代表点
$a_i=\frac1{|\mathcal{B}_i|}\sum_{k=1}^M1(c_k\in\mathcal{B}_i)\cdot c_k,$

#### 标签校正
对于特别密集的人群，实际上可能包含很多人头，但受限于分辨率，只能观察到较小的人头数
作者将固定大小图像块中可观察到的人的最大数量限制在一个小的范围内，完全由补丁大小决定。
最大数量$m=(r//s)^2$，s是一个人头可被识别的最小尺寸，r是块大小
![image.png](https://oss.zaqai.com/img/202404162235888.png)

#### 损失函数

之前的基于分类的方法的损失函数只关注分类误差，忽视了预测值和GT的差异，尽管两个概率分布分类错误率相同，但他们可能具有不同的期望值，即它们在样本空间上的平均值可能不同
距离感知交叉熵损失（DACE）
$\begin{aligned}\mathcal{L}_{\mathrm{DACE}}=&\mathcal{L}_{\mathrm{class}}(\boldsymbol{P}^*,\boldsymbol{P})+\lambda\mathcal{L}_{\mathrm{count}}(\boldsymbol{Y}^*,\boldsymbol{Y})\\&=-\sum_{i=1}^{H//r}\sum_{j=1}^{W//r}\sum_{k=1}^n\mathbb{1}(\boldsymbol{P}_{k,i,j}=1)\log\boldsymbol{P}_{k,i,j}^*\\&+\lambda\mathcal{L}_{\mathrm{count}}(\boldsymbol{Y}^*,\boldsymbol{Y}),\end{aligned}$
P是one-hot GT概率图，$P^*$是预测出的概率图，维度[n,h,w],n是箱子的数量
Y是密度图

### CLIP-EBC

![image.png](https://oss.zaqai.com/img/202404161627519.png)
CLIP-EBC对CLIP的图像编码器最后的层做了修改，去除池化层，用1×1卷积替换线性投影层。这种修改允许保存本地信息，这在人群计数中起着至关重要的作用。输出[d,h,w]的特征图，h=H//r, r是patch尺寸
文本提示
> There is/are bi person/people
> There is/are between min(Bi) and max(Bi) person/people
> There are more than m people


## 性能
![image.png](https://oss.zaqai.com/img/202404162329604.png)
EBC确实有效，CLIP-EBC (ResNet50, ours)  ResNet50用在了哪里？