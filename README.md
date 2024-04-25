# 研一数据挖掘课程大作业
# 一、注册github用户

#### 之前已经注册过github用户：

![image](/images/账户.PNG)

<img src="\images\账户.PNG" style="zoom: 50%;" />

# 二、建立一个库GuoSijieDatamining

#### 填写仓库名称，简介，设置为公开，确定添加一个README文件，方便他人进行搜索和阅读：

<img src="C:\Users\DELL\Desktop\数据挖掘\建库.png" style="zoom:50%;" />

# 三、上传PPT到GuoSijieDatamining中

#### Add file、Upload files：

<img src="C:\Users\DELL\Desktop\数据挖掘\上传文件.png" style="zoom:50%;" />

# 四、学习计算机技能——扩散模型

## 1. 扩散模型的训练过程

#### 前向加噪：从1到T的时间采样一个时间t，生成一个对应的随机噪声加到图片上。

<img src="C:\Users\DELL\Desktop\数据挖掘\训练过程.PNG" style="zoom: 50%;" />

#### 逆向去噪：训练一个神经网络，从神经网络获取预测噪声ε ，计算预测噪声与随机噪声的损失后更新梯度；从正态分布随机采样和训练样本一样大小的纯噪声图片x T，在之后的T，T-1…等步骤中，根据神经网络求出ε，结合x t和采样利用重参数化技巧，得到x t-1，循环结束后返回x 0。

## 2. 基于UNet的扩散模型

### （1）为什么用UNet做扩散模型

####     扩散模型大部分采用UNet架构来进行建模，UNet可以实现输出和输入一样维度，所以天然适合扩散模型。扩散模型使用的UNet除了包含基于残差的卷积模块，同时也往往采用self-attention。

### （2）UNet整体网络结构

<img src="C:\Users\DELL\Desktop\数据挖掘\UNet.PNG" style="zoom: 67%;" />

### （3）UNet所含ResBlock的具体结构

<img src="C:\Users\DELL\Desktop\数据挖掘\ResBlock.PNG" style="zoom: 50%;" />

* #### shortcut：在输入通道数和输出通道数不匹配时，会使用卷积操作来调整维度。

* #### attn：如果 attn 参数为 True，表示当需要应用注意力机制时，会使用AttnBlock对输出通道数为out_ch的特征进行处理。

## 3. 扩散模型中的TimeEmbedding

### （1）为什么引入TimeEmbedding

####         引入TimeEmbedding是为了模拟一个随时间逐渐增强的扰动过程。每个时间步长 t 代表一个扰动过程，从初始状态开始，通过多次应用噪声来逐渐改变图像的分布。因此，较小的 t 代表较弱的噪声扰动，而较大的 t 代表更强的噪声扰动。

####         此外，DDPM 中的 UNet 都是共享参数的，那如何根据不同的输入生成不同的输出，最后从一个完全的一个随机噪声变成一个有意义的图片，这还是一个非常难的问题。我们希望这个 UNet 模型在刚开始的反向过程之中，它可以先生成一些物体的大体轮廓，随着扩散模型一点一点往前走，然后到最后快生成逼真图像的时候，这时候希望它学习到高频的一些特征信息。由于 UNet 都是共享参数，这时候就需要TimeEmbedding去提醒这个模型，我们现在走到哪一步了，现在输出是想要粗糙一点的，还是细致一点的。

### （2）训练过程中的TimeEmbedding

<img src="C:\Users\DELL\Desktop\数据挖掘\训练过程1.png" style="zoom: 67%;" />

<img src="C:\Users\DELL\Desktop\数据挖掘\详细训练过程.png" style="zoom: 80%;" />

#### ① 训练样本选择一个随机时间步长 t

#### ② 将time step t 对应的高斯噪声应用到图片中（按照指定的时间索引t提取系数）

#### ③ 将time step转化为对应embedding，送入网络用于预测噪声

### （3）采样过程中的TimeEmbedding

<img src="C:\Users\DELL\Desktop\数据挖掘\采样过程.png" style="zoom: 80%;" />

### （4）TimeEmbedding处理步骤

#### ① 预先计算时间嵌入权重矩阵emb

<img src="C:\Users\DELL\Desktop\数据挖掘\emb.PNG" style="zoom:60%;" />

#### ② 将时间索引 t 映射为对应的嵌入向量 emb[t]

<img src="C:\Users\DELL\Desktop\数据挖掘\t映射.PNG" style="zoom: 67%;" />

