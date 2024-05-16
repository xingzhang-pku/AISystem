# AI计算模式

了解AI计算模式对AI芯片设计和优化方向至关重要。本节将会从如下几个主题来深入了解AI算法的发展现状，引发关于AI计算模式的思考，为了避免篇幅过长，分为2个文档描述。我们先了解一下前三个主题。

- AI的发展和范式
- 经典模型结构
- 模型量化与剪枝
- 轻量化网络模型
- 大模型分布式并行

## AI的发展和范式
人工智能的发展历程可谓曲折起伏，自1956年“人工智能”术语首次被提出至今已经快70年了。学术界对其发展历程的描述各有不同，大概可以分为如下四个阶段：

- 萌芽兴奋期（约1950s）：1943年，美国神经科学家麦卡洛克（Warren McCulloch）和逻辑学家皮茨（Water Pitts）提出神经元的数学模型是现代人工智能学科的重要奠基石。人工智能概念的提出带来了一系列研究成果，如机器定理证明、跳棋程序等，掀起了人工智能发展的第一个高潮。测试机器是否能表现出与人无法区分的智能"图灵测试"就是在1950年提出的。

- 机器学习应用的蓬勃发展期（约1980s）：在1970年代，由于当时算力的限制和理论推导知识的匮乏，人工智能的发展陷入了短暂的低谷期，直到1980年专家系统模拟人类专家的知识和经验解决特定领域的问题，实现了人工智能从理论研究走向实际应用、从一般推理策略探讨转向运用专门知识的重大突破，推动了人工智能走入应用发展的新高潮。与此同时，机器学习在不同的学习策略和方法上的探索，以及大量的实际应用中的复苏，也为人工智能的发展注入了新的活力，贝叶斯网络，多层感知机，ID3决策树算法，支持向量机等都是这一时期所熟知的算法。

- 深度学习的突破驱动的繁荣期（约2010s）：随着大数据、云计算、互联网和物联网等信息技术的不断发展，2010年代，深度学习在人工智能领域掀起了一股热潮，通过多层次的神经网络结构，深度学习模型能够从大规模数据中学习到复杂的特征表示，取得了在图像识别、语音识别、自然语言处理等领域的突破性进展。随后人工智能算法相关应用迎来了爆发式增长的新高潮。在2015年的 ImageNet 挑战赛，在图像识别准确率上，机器的表现首次超过了人类，被认为是人工智能领域的一个里程碑式的突破，卷积神经网络的经典模型结构ResNet就是在这次被提出的。

- 基础模型带来的机遇期（约2020s）：随着人工智能模型结构向更深的网络结构，更多的参数演进，出现了各种基于预训练模型进行参数微调的特大参数模型。2021年8月份，著名人工智能教授李飞飞和100多位学者联名发表一份200多页的研究报告《[On the Opportunities and Risk of Foundation Models](https://arxiv.org/pdf/2108.07258)》，深度地综述了当前大规模预训练模型面临的机遇和挑战，文中将这种基于深度神经网络和自监督学习技术，在大规模、广泛来源数据集上训练的AI模型称为基础模型。基础模型包括多种预训练模型，如BERT、GPT-3、CLIP、DALL·E等。目前基础模型所涉及的技术子领域包括：模型的构建、训练、微调、评价、加速、数据、安全、稳健性、与人类的匹配（Alignment）、模型理论、可解释等，基础模型仍处于快速发展的阶段。

根据学习的模式，可以将AI算法分为有监督学习，无监督和强化学习三种不同的学习范式。

- 有监督学习：要是根据标注数据对模型的输入和输出学习到一种映射关系，以此对测试数据集中的样本进行预测。如分类和回归算法。
- 无监督学习：无监督学习仅依赖于无标签的数据训练模型来学习数据表征，如聚类算法
- 强化学习：强化学习强调如何基于环境而行动，以取得最大化的预期利益。与监督学习不同的是，强化学习不需要带标签的输入输出对，同时也无需对非最优解的精确地纠正。其关注点在于寻找探索（对未知领域的）和利用（对已有知识的）的平衡。在游戏和机器人控制领域具有广泛应用。

深度学习的计算模式和这三种AI范式都可以结合起来，他们的处理流程如下图。假设绿色方框是一个深度学习模型，有监督学习中，会给模型输入一个有标签的数据，然后通过定义一个损失函数把输出误差反馈给模型参数做相应的调整学习过程，如图像分类算法；无监督学习接受的则是无标签的数据，中间通过深度学习模型设计一种统计算法，得到一个分类结果，如推荐系统算法；强化学习则是在给定的环境中，智能体通过感知所处环境的状态改变导致的反应(reward)，来指导动作得到最大收益的过程，如深度强化学习算法。

![](images/approach01.png)

## 网络模型结构设计&演进

### 神经网络的基本概念

神经网络是AI算法基础的计算模型，灵感来源于人类大脑的神经系统结构。它由大量的人工神经元组成，分布在多个层次上，每个神经元都与下一层的所有神经元连接，并具有可调节的连接权重。神经网络通过学习从输入数据中提取特征，并通过层层传递信号进行信息处理，最终产生输出。这种网络结构使得神经网络在模式识别、分类、回归等任务上表现出色，尤其在大数据环境下，其表现优势更为显著。

对一个神经网络来说，主要包含如下几个知识点，这些是构成一个神经网络模型的基础组件。

- 神经元（Neuron）：神经网络的基本组成单元，模拟生物神经元的功能，接收输入信号并产生输出，这些神经元在一个神经网络中称为模型权重。

- 激活函数（Activation Function）：用于神经元输出非线性化的函数，常见的激活函数包括Sigmoid、ReLU等。

- 层（Layer）：神经网络由多个层次组成，包括输入层、隐藏层和输出层。隐藏层可以有多层，用于提取数据的不同特征。

- 前向传播（Forward Propagation）：输入数据通过神经网络从输入层传递到最后输出层的过程，用于生成预测结果。

- 反向传播（Backpropagation）：通过计算损失函数对网络参数进行调整的过程，以使网络的输出更接近预期输出。

- 损失函数（Loss Function）：衡量模型预测结果与实际结果之间差异的函数，常见的损失函数包括均方误差和交叉熵。

- 优化算法（Optimization Algorithm）：用于调整神经网络参数以最小化损失函数的算法，常见的优化算法包括梯度下降，自适应评估算法（Adaptive Moment Estimation）等，不同的优化算法会影响模型训练的收敛速度和能达到的性能。

那么通过上面组件，如何得到一个可应用的神经网络模型呢？神经网络的产生包含训练和推理两个阶段。训练阶段可以描述为如下几个过程。从下图流程中可以发现，神经网络的训练阶段比推理阶段对算力和内存的需求更大，流程更加复杂，难度更大，所以很多公司芯片研发会优先考虑支持AI推理阶段。

![](./images/02.arch.nn.train.inference.png)

整体来说，训练阶段的目的是通过最小化损失函数来学习数据的特征和内在关系，优化模型的参数；推理阶段则是利用训练好的模型来对新数据做出预测或决策。训练通常需要大量的计算资源，且耗时较长，通常在服务器或云端进行；而推理可以在不同的设备上进行，包括服务器、云端、移动设备等，依据模型复杂度和实际应用需求定。在推理阶段，模型的响应时间和资源消耗成为重要考虑因素，尤其是在资源受限的设备上。

下图是一个经典的图像分类的卷积神经网络结构，网络结构从左到右有多个层级设计，用来提取更高维的目标特征（这些中间层的输出称为特征图，特征图数据是可以通过可视化工具展示出来的，用来观察每一层的神经元都在做些什么工作），最后通过一个softmax激活函数达到分类输出映射。

![](./images/02.cnn.demo.png)

接下来我们来了解一下神经网络中的主要计算范式：**权重求和**。下图是一个简单的神经网络结构，左边中间灰色的圈圈表示一个简单的神经元，每个神经元里有求和和激活两个操作，求和就是指乘加或者矩阵相乘，激活函数则是决定这些神经元是否对输出有用，Tanh, ReLU, Sigmoid, Linear都是常见的激活函数。神经网络中90%的计算量都是在做乘加（multiply and accumulate, MAC）的操作，也称为权重求和。

![](images/02.neural.mac.png)

### 主流的网络模型结构

不同的神经网络设计层出不穷，但是主流的模型结构有如下四种。

- 全连接 Fully Connected Layer

- 卷积层 Convolutional Layer

- 循环网络Recurrent Layer

- 注意力机制 Attention Layer

**全连接 Fully Connected Layer**

全连接层（Fully Connected Layer），也称为密集连接层或仿射层，是深度学习神经网络中常见的一种层类型，通常位于网络的最后几层，全连接层的每个神经元都会与前一层的所有神经元进行全连接，通过这种方式，全连接层能够学习到输入数据的全局特征，并对其进行分类或回归等任务。下面是全连接层的基本概念和特点：

- 连接方式：全连接层中的每个神经元与前一层的所有神经元都有连接，这意味着每个输入特征都会影响到每个输出神经元，因此全连接层是一种高度密集的连接结构。
- 参数学习：全连接层中的连接权重和偏置项是需要通过反向传播算法进行学习的模型参数，这些参数的优化过程通过最小化损失函数来实现。

- 特征整合：全连接层的主要作用是将前面层提取到的特征进行整合和转换，以生成最终的输出。在图像分类任务中，全连接层通常用于将卷积层提取到的特征映射转换为类别预测分数。

- 非线性变换：在全连接层中通常会应用非线性激活函数，如ReLU（Rectified Linear Unit）、Sigmoid或Tanh等，以增加模型的表达能力和非线性拟合能力。

- 输出层：在分类任务中，全连接层通常作为网络的最后一层，并配合Softmax激活函数用于生成类别预测的概率分布。在回归任务中，全连接层的输出通常直接作为最终的预测值。

- 过拟合风险：全连接层的参数数量通常较大，因此在训练过程中容易产生过拟合的问题。为了缓解过拟合，可以采用正则化技术、dropout等方法。

全连接层在深度学习中被广泛应用于各种任务，包括图像分类、目标检测、语音识别和自然语言处理等领域，它能够将前面层提取到的特征进行有效整合和转换，从而实现对复杂数据的高效处理和表示。

**卷积层 Convolutional Layer**

卷积层是深度学习神经网络中常用的一种层类型，主要用于处理图像和序列数据。它的主要作用是通过学习可重复使用的卷积核（filter）来提取输入数据中的特征。下面是一些卷积层的基本概念和特点：

- 卷积操作：卷积层通过对输入数据应用卷积操作来提取特征。卷积操作是一种在输入数据上滑动卷积核并计算卷积核与输入数据的乘积的操作，通常还会加上偏置项（bias）并应用激活函数。
- 卷积核：卷积核是卷积层的参数，它是一个小的可学习的权重矩阵，用于在输入数据上执行卷积操作。卷积核的大小通常是正方形，例如3x3或5x5，不同的卷积核可以捕获不同的特征。
- 特征图：卷积操作的输出称为特征图（feature map），它是通过卷积核在输入数据上滑动并应用激活函数得到的。特征图的深度（通道数）取决于卷积核的数量。
- 步长和填充：卷积操作中的步长（stride）定义了卷积核在输入数据上滑动的步长，而填充（padding）则是在输入数据的边界上添加额外的值，以控制输出特征图的尺寸。填充模式一般分为same, valid两种。same模式表示输出feature map和输入的尺寸相同，valid的输出feature map一般比输入小。
- 参数共享：卷积层的参数共享是指在整个输入数据上使用相同的卷积核进行卷积操作，这样可以大大减少模型的参数数量，从而减少过拟合的风险并提高模型的泛化能力。
- 池化操作：在卷积层之后通常会使用池化层来减小特征图的尺寸并提取最重要的特征。常见的池化操作包括最大池化和平均池化。

卷积层在深度学习中被广泛应用于图像处理、语音识别和自然语言处理等领域，它能够有效地提取输入数据的局部特征并保留空间信息，从而帮助神经网络模型更好地理解和处理复杂的数据。

下图是一个一层5x5的feature map的卷积过程，橙色表示3x3的kernel，左侧浅色方框和kernel相乘并累加和，得到右侧浅蓝色标记的一个输出值。same和valid两种padding模式得到了不同的feature map输出。

![](./images/02.arch_slim05.png)

**循环网络Recurrent Layer**

循环神经网络（RNN）是一种用于处理序列数据的神经网络结构。它的独特之处在于具有循环连接，允许信息在网络内部传递，从而能够捕捉序列数据中的时序信息和长期依赖关系。基本的RNN结构包括一个或多个时间步（time step），每个时间步都有一个输入和一个隐藏状态（hidden state）。隐藏状态在每个时间步都会被更新，同时也会被传递到下一个时间步。这种结构使得RNN可以对序列中的每个元素进行处理，并且在处理后保留之前的信息。然而，传统的RNN存在着梯度消失或梯度爆炸的问题，导致难以捕捉长期依赖关系。为了解决这个问题，出现了一些改进的RNN结构，其中最为著名的就是长短期记忆网络（LSTM）和门控循环单元（GRU）。LSTM通过门控机制来控制信息的流动，包括输入门、遗忘门和输出门，从而更好地捕捉长期依赖关系。而GRU则是一种更简化的门控循环单元，它合并了输入门和遗忘门，降低了参数数量，同时在某些情况下表现优异。总的来说，循环神经网络在自然语言处理、时间序列预测、语音识别等领域都有广泛的应用，能够有效地处理具有时序关系的数据。

![](./images/02.arch.rnn.png)

如上是一个RNN结构示意图，左边是在t时刻神经元状态的折叠图，右边是按时间线展开的示意图。如果单看左边的图，不考虑w表示的那个圆圈，则就是一个全连接层的神经元结构：输入为 X，U是从X到S神经元的参数矩阵，S是隐藏层的输出，V是由S到O的参数矩阵。输出层为O；接下来我们来看一下在时间维度上RNN结构是如何把上下文的序列关系连接起来的。右图表示了3个时间节点的示意过程，W是每个时间点之间的权重矩阵，是共用的。RNN之所以可以解决序列问题，是因为它可以记住每一时刻的信息，每一时刻的隐藏层不仅由该时刻的输入层决定，还由上一时刻的隐藏层决定，公式如下，其中$X_t$代表t时刻的输入，$O_t$代表t时刻的输出, $S_t$代表t时刻的隐藏层的值,$S_t$的值和t时刻的输入, t-1时刻的隐藏层值都相关。

$$
O_t=g(V \cdot S_t)\\
S_t=f(U \cdot X_t + W \cdot S_{t-1})
$$

**注意力机制 Attention Layer**

深度学习中的注意力机制（Attention Mechanism）是一种模仿人类视觉和认知系统的方法，它允许神经网络在处理输入数据时集中注意力于相关的部分。通过引入注意力机制，神经网络能够自动地学习并选择性地关注输入中的重要信息，提高模型的性能和泛化能力。其中由谷歌团队在2017年的论文[《Attention is All Your Need》](https://arxiv.org/pdf/1706.03762)中以编码器-解码器为基础，创新性的提出了一种Transformer架构。该架构可以有效的解决RNN无法并行处理以及CNN无法高效的捕捉长距离依赖的问题，后来也被广泛地应用到了计算机视觉领域。Transformer是目前很多大模型结构的基础组成部分，而Transformer里的重要组件就是Scaled Dot-Product Attention和Multi-Head Attention，下图是它们的结构示意图。

![](./images/02.arch.attention.0.png)

Dot-Product Attention也称为自注意力，公式定义为：

$$
Attention(Q, K, V)=softmax(QK^T/ \sqrt d_k)V
$$

下面是借助python代码实现的自注意力网络，可以帮助理解具体的运算过程。

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader
from torchvision import datasets, transforms

# 定义自注意力模块
class SelfAttention(nn.Module):
    def __init__(self, embed_dim):
        super(SelfAttention, self).__init__()
        self.query = nn.Linear(embed_dim, embed_dim)
        self.key = nn.Linear(embed_dim, embed_dim)
        self.value = nn.Linear(embed_dim, embed_dim)

    def forward(self, x):
        q = self.query(x)
        k = self.key(x)
        v = self.value(x)
        attn_weights = torch.matmul(q, k.transpose(1, 2))
        attn_weights = nn.functional.softmax(attn_weights, dim=-1)
        attended_values = torch.matmul(attn_weights, v)
        return attended_values
```

多头注意力机制是在自注意力机制的基础上发展起来的，是自注意力机制的变体，旨在增强模型的表达能力和泛化能力。它通过使用多个独立的注意力头，分别计算注意力权重，并将它们的结果进行拼接或加权求和，从而获得更丰富的表示。

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader
from torchvision import datasets, transforms

# 定义多头自注意力模块
class MultiHeadSelfAttention(nn.Module):
    def __init__(self, embed_dim, num_heads):
        super(MultiHeadSelfAttention, self).__init__()
        self.num_heads = num_heads
        self.head_dim = embed_dim // num_heads

        self.query = nn.Linear(embed_dim, embed_dim)
        self.key = nn.Linear(embed_dim, embed_dim)
        self.value = nn.Linear(embed_dim, embed_dim)
        self.fc = nn.Linear(embed_dim, embed_dim)

    def forward(self, x):
        batch_size, seq_len, embed_dim = x.size()

        # 将输入向量拆分为多个头
        q = self.query(x).view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        k = self.key(x).view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        v = self.value(x).view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)

        # 计算注意力权重
        attn_weights = torch.matmul(q, k.transpose(-2, -1)) / torch.sqrt(torch.tensor(self.head_dim, dtype=torch.float))
        attn_weights = torch.softmax(attn_weights, dim=-1)

        # 注意力加权求和
        attended_values = torch.matmul(attn_weights, v).transpose(1, 2).contiguous().view(batch_size, seq_len, embed_dim)

        # 经过线性变换和残差连接
        x = self.fc(attended_values) + x

        return x
```

### 网络模型设计的演进趋势

AI模型设计层出不穷，但是观察近20年的AI模型特点，我们大概可以看到一些模型设计的演进趋势。下表是从1998年的LeNet-5到2019年基于ImageNet数据集图像分类任务的经典模型相关的参数，可以发现模型的性能越来越好，网络层数越来越多，运算量越来越多，对内存的占用也越来越多了。

![](images/02.net_dev.trend.png)

### AI计算模式思考

结合上面介绍的经典AI模型结构特点，AI芯片设计可以引出如下对于AI计算模式的思考。

1. 需要支持神经网路模型的计算逻辑。比如不同神经元之间权重共享逻辑的支持，以及除了卷积/全连接这种计算密集型算子，还需要支持激活如softmax, layernorm等Vector类型访存密集型的算子。

2. 能够支持高维的张量存储与计算。比如在卷积运算中，每一层都有大量的输入和输出通道，并且对于某些特定场景如遥感领域，feature map的形状都非常大，在有限的芯片面积里最大化计算访存比，高效的内存管理设计非常重要。

3. 需要有灵活的软件配置接口，支持更多的神经网络模型结构。针对不同领域，如计算机视觉，语音，自然语言处理，AI模型具有不同形式的设计，但是作为AI芯片，需要尽可能全的支持所有应用领域的模型，并且支持未来可能出现的新模型结构，这样在一个漫长的芯片设计到流片的周期中，才能降低研发成本，获得市场的认可。

## 模型量化&网络剪枝

什么是模型的量化和网络剪枝呢？现在我们已经了解到了神经网络模型的一些特点，比如模型深度高，每层的通道多，这些都会导致训练好的模型权重数据内存较大，另外，训练时候为了加速模型的收敛和确保模型精度，一般都会采用高比特的数据类型，比如FP32，这也会比硬件的计算资源带来很大的压力。所以模型量化和网络剪枝就是专门针对AI的神经网络模型在训练和推理不同阶段需求的特点做出的优化技术，旨在减少模型的计算和存储需求，加速推理速度，从而提高模型在移动设备、嵌入式系统和边缘设备上的性能和效率。

模型量化是指通过减少神经模型权重表示或者激活所需的比特数来将高精度模型转换为低精度模型。网络剪枝则是研究模型权重中的冗余，尝试在不影响或者少影响模型推理精度的条件下删除/修剪冗余和非关键的权重。量化压缩 vs 网络剪枝的示意如下图所示：

![](./images/02.arch.quant.prune.01.png)

### 模型量化

将高比特模型进行低比特量化具有如下几个好处。

1. 降低内存：低比特量化将模型的权重和激活值转换为较低位宽度的整数或定点数，从而大幅减少了模型的存储需求，使得模型可以更轻松地部署在资源受限的设备上。

2. 降低成本： 低比特量化降低了神经网络中乘法和加法操作的精度要求，从而减少了计算量，加速了推理过程，提高了模型的效率和速度。

3. 降低能耗： 低比特量化减少了模型的计算需求，因此可以降低模型在移动设备和嵌入式系统上的能耗，延长设备的电池寿命。

4. 提升速度： 虽然低比特量化会引入一定程度的信息丢失，但在合理选择量化参数的情况下，可以最大程度地保持模型的性能和准确度，同时获得存储和计算上的优势。

5. 丰富模型的部署场景： 低比特量化压缩了模型参数，可以使得模型更容易在移动设备、边缘设备和嵌入式系统等资源受限的环境中部署和运行，为各种应用场景提供更大的灵活性和可行性。

**量化技术的基本概念**

量化的类型分对称量化和非对称量化。量化策略分为：MINMAX, KL散度，ADMM, EQ 等，下面是基于MINMAX量化算法的公式，其中R表示浮点数，Q表示量化数，S表示缩放因子，Z表示零值。_max, _min表示要转换的浮点或量化数据区间的最大值和最小值。

$$
\begin{align}
&Q=R/S + Z \\
&S=(R_{max} - R_{min})/(Q_{max}-Q_{min}) \\
&Z=Q_{max} - R_{max}/S
\end{align}
$$

对称量化和非对称量化的区别主要是对浮点数的最大值和最小值统计区间的区别，对称量化中R_max, R_min关于0对称，也就是只需要找到浮点区间的绝对值最大的那个值即可。对称量化对于正负数不均匀分布的情况不够友好，比如如果浮点数X全部是正数，进行8bit非对称量化后的数据全部在[0，127]一侧, 那么[-128，0]的区间就浪费了，减弱了8bit数据的表示范围。非对称量化则会更好的将数据表达在[0,255]整个区间。下图是两种量化方法进行8bit量化数据映射的示意图。

![](images/02.arch.quant_demo0.png)

神经网络的权重量化以layer为单位，求每一层权重的量化参数(S,Z)时候，又会分为by channel和by layer两种方式。by channel是指每层的每个channel都计算一个(S,Z)；by layer方式则是这一层所有channel数据共同统计，每个channel共用一组(S,Z)量化参数。by channel的量化方式比by layer会存储更多的量化参数，所以实际部署中需要和带来的性能提升做一个均衡的选择。

按照模型的量化所处阶段，TensorFlow框架将模型量化功能分为训练后量化（Post-training quantization PTQ）和训练感知量化（Quantization-aware training QAT）。PTQ属于离线量化，将一个已经训练好的离线模型通过对权重的数据分布进行统计，记录量化参数S, Z ，然后把浮点参数转化为量化参数，这种简单粗暴的量化方式对模型的精度影响可能会比较大。而QAT方法则是将量化过程代入到了模型的训练阶段，首先基于一个训练好的模型作为baseline model, 通过在模型中间插入一些伪量化节点，重新进行微调训练，从而得到一个更接近训练精度的量化模型。

接着我们了解一下浮点数据的MatMul算子是如何转换为量化数据进行等价运算的。如下图，因为scale, zero数据在模型推理时候都是已知的常量，根据浮点到定点的量化公式，令$Z=X * Y$指代MatMul算子的两个输入和一个输出Tensor，其浮点到定点的运算具体转换过程如下。当然在进行MAC的运算累加时候，为了减少误差精度损失，累加和一般会用INT32类型来表示。

$$
\begin{align}
&Z_{float} = X_{float} \times Y_{float} \\
\Rightarrow &Z_{scale} \times ( Z_{quant} - Z_{zero}) = (X_{scale} \times ( X_{quant} - X_{zero}) ) \times (Y_{scale} \times ( Y_{quant} - Y_{zero}) )\\
\Rightarrow &Z_{quant}  = (X_{scale} \times Y_{scale} / Z_{scale}) \times  ( X_{quant} - X_{zero}) \times ( Y_{quant} - Y_{zero}) + Z_{zero} \\
&令 Multiplier =  (X_{scale} * Y_{scale} / Z_{scale})\\
\Rightarrow &Z_{quant}  = Multiplier\times  ( X_{quant} - X_{zero}) \times ( Y_{quant} - Y_{zero}) + Z_{zero}\\
\end{align}
$$

目前神经网络中的常用的类型有FP32, TF32, FP16, BF16这四种类型。下图是这四种数据类型的指数和尾数定义。

![](./images/02.arch.dtype.png)

**FP32:** 单精度浮点数格式FP32是一种广泛使用的数据格式，其可以表示很大的实数范围，足够深度学习训练和推理中使用。每个数据占4个字节。

**TF32:** Tensor Float 32是Tensor Core支持的新的数据类型，从NVIDIA A100中开始支持。TF32的峰值计算速度相关FP32有了很大的提升。

**FP16:** P16是一种半精度浮点格式，深度学习有使用FP16而不是FP32的趋势，因为较低精度的计算对于神经网络来说似乎并不重要。

**BF16:** FP16设计时并未考虑深度学习应用，其动态范围太窄。由Google开发的16位浮点格式称为“Brain Floating Point Format”，简称“bfloat16”，bfloat16解决了这个问题，提供与FP32相同的动态范围。其可以认为是直接将FP32的前16位截取获得的，目前也得到了广泛的应用。

**模型量化相关的研究热点**

模型量化本质上就是将深度学习模型中权重和激活值从高比特精度（FP32）转换为低比特精度（INT4）的技术，用来加速模型的部署和集成，降低硬件成本，为实际应用带来更多可能性， 所以模型量化依旧是未来AI落地应用的一个重要的研究方向。围绕着模型量化的研究热点可以分为如下几个方面：

1. 量化方法：线性量化和非线性量化

   根据要量化的原始数据分布范围，可以将量化方法分为线性量化（如MINMAX）和非线性量化（如KL散度，Log-Net）。

   线性量化：也称为均匀量化，在线性量化中相邻两个量化值之间的差距是固定的。量化公式中的R表示原来的浮点值，Q表示量化后的整型数据，S表示缩放因子，是量化相关的参数。量化的过程是浮点值除以缩放因子，然后做舍入和clamp操作。反量化就是用量化值乘缩放因子。因为量化过程引入了舍入和clamp操作，因此反量化回去的值与原来的浮点值并不相等。

   非线性量化：量化值之间的间隔不固定。网络中值的分布往往是不均匀的，更多的是类似高斯分布的形式。非线性量化可以更好的捕获分布相关的信息，数据多的地方量化间隔小，量化精度高；数据少的地方量化间隔大，量化精度低。因此非线性量化的效果理论上比线性量化更好。

2. 量化方式：量化感知训练和混合精度量化

   量化感知训练（QAT）是一种在训练期间考虑量化的技术，旨在减少量化后模型的性能损失。目前的研究集中在改进QAT算法，使其能够更有效地保持模型的性能和准确性。

   混合精度量化指的是通过设计某种策略，来对模型各层的weights和activations的量化bit位宽进行合理分配，其重点就是如何设计这个策略，从而使得混合精度量化之后模型能够在精度和硬件指标上达到最佳的平衡。混合精度量化结合了不同精度的量化技术，如8位、16位和32位精度，以平衡模型的性能和计算效率

3. 模型设计：二值化网络模型

   二值化网络模型，也称为二值神经网络（Binary Neural Networks，BNNs），是一种将神经网络中的权重和激活值二值化为{-1, +1}或{0, 1}的模型。这种二值化可以极大地减少模型的存储需求和计算成本，从而使得在资源受限的设备上进行高效的推断成为可能。

   二值化网络模型的训练通常涉及以下关键步骤：

   - 网络结构设计：设计一个适合二值化的网络结构，通常包括卷积层、全连接层和激活函数。

   - 二值化参数：在训练过程中，权重和激活值被不断更新为二值化的版本，通常是根据它们的均值来确定阈值，并据此将它们量化为{-1, +1}。

   - 训练优化：为了使得二值化后的模型能够保持较高的性能，通常需要设计特殊的损失函数和优化方法，例如引入约束条件或者使用适应性的学习率调整。

   - 量化感知训练：在训练过程中，可以采用量化感知训练技术，即在优化过程中考虑二值化的影响，以减少量化后模型的性能损失。

   二值化网络模型的优点包括较小的模型尺寸、低廉的推理成本和更好的能耗效率。然而，由于权重和激活值的二值化可能导致信息损失，因此需要仔细设计和优化网络结构和训练方法，以在保持模型性能的同时实现二值化。

### 模型剪枝
模型剪枝是一种有效的模型压缩方法，通过对模型中的权重进行剔除，降低模型的复杂度，从而达到减少存储空间、计算资源和加速模型推理的目的。模型剪枝的原理是通过剔除模型中“不重要”的权重，使得模型减少参数量和计算量，同时尽量保证模型的精度不降低。具体而言，剪枝算法会评估每个权重的贡献度，根据一定的剪枝策略将较小的权重剔除，从而缩小模型体积。在剪枝过程中，需要同时考虑模型精度的损失和剪枝带来的计算效率提升，以实现最佳的模型压缩效果。

**剪枝技术的基本概念**

根据剪枝的时机和方式，可以将模型剪枝分为以下几种类型：

- 静态剪枝：在训练结束后对模型进行剪枝，这种方法的优点是简单易行，但无法充分利用训练过程中的信息。

- 动态剪枝：在训练过程中进行剪枝，根据训练过程中的数据和误差信息动态地调整网络结构。这种方法的优点是能够在训练过程中自适应地优化模型结构，但实现起来较为复杂。

- 知识蒸馏剪枝：通过将大模型的“知识”蒸馏到小模型中，实现小模型的剪枝。这种方法需要额外的训练步骤，但可以获得较好的压缩效果。

对神经网络模型的剪枝可以描述为如下三个步骤：

- 训练 Training：训练过参数化模型，得到最佳网络性能，以此为基准；
- 剪枝 Pruning：根据算法对模型剪枝，调整网络结构中通道或层数，得到剪枝后的网络结构；
- 微调 Finetune：在原数据集上进行微调，用于重新弥补因为剪枝后的稀疏模型丢失的精度性能。

下图是一个简单的多层神经网络的剪枝示意，卷积算法定义可以分为两种，一种是右图上面示意的对全连接层神经元之间的连接线进行剪枝，另一种是对神经元的剪枝。

![](./images/02.arch.prune01.png)

对神经网络中的卷积层进行剪枝示意来说，剪枝算法定义又可以理解为非结构化剪枝和结构化剪枝。假设卷积层的权重维度为(co, ci, kh, kw)。非结构化剪枝是指随机对独立的权重或者神经元链接进行剪枝，即(co, ci, kh, kw)的任意维度减小为任意值。结构化剪枝则是约束剪枝粒度为具体的layer, channel, filter，即约束减少对应(co, ci, kh, kw)中kh, ci, co维度的值。假设(co, ci, kh, kw)=(2, 3, 3, 3)，非结构化剪枝和结构化剪枝示意如下图所示。

![](./images/02.arch.prune02.png)

**剪枝算法该如何取舍？**

深度神经网络结构具有一定的冗余性，即某些神经元或连接在模型的性能上并不是必需的。这种冗余性使得CNN在一定程度上具有容错性，即使某些部分损坏或丢失，模型仍然可以保持一定的功能和性能，这种特性是模型剪枝算法的重要依据。

如下列表所示，是不同网络维度设计，以及不同的剪枝比率下NMT模型的性能表现。可以观察到如下规律：

1. 模型结构设计的隐藏层维度越大，NNZ params越大，模型性能越好。

2. 同样NNZ params下，大稀疏模型比小密集模型实现了更高的精度。即经过剪枝之后稀疏模型要优于同体积非稀疏模型。

3. 同样的模型结构，不同的稀疏比率，模型的性能缓慢下降。综合考虑资源限制和性能需求，可以选择不同的剪枝程度。

![](images/02.arch.prune03.png)

综上，在资源有限的情况下，剪枝是比较有效的模型压缩策略， 实际应用中可以根据具体的场景和需求来设计模型深度和定义剪枝比率。另外，非结构化剪枝通常会导致模型中大量的零值权重，这些零值权重可以被稀疏矩阵存储所有效地表示和存储。稀疏矩阵存储技术可以利用零值的稀疏性，只存储非零值及其对应的索引，从而大幅度减少模型的存储空间。所以模型剪枝结合硬件稀疏化存储算法可以对模型的部署和运行带来很大的增益，也是目前AI算法落地应用的一个研究热点。

### AI计算模式思考

结合上面AI模型的量化和剪枝算法的研究进展，AI芯片设计可以引出如下对于AI计算模式的思考：

1. 提供不同的bit位数的计算单元和存储
    - 对于低比特量化相关的研究落地提供int8/int4甚至更低的精度
    - 在数据存储类型M-bits and E-bits之间权衡Tradeoff(如TF32/BF16)
2. 利用硬件提供稀疏计算的优化逻辑
    - 支持不同稀疏算法逻辑，硬件计算取数模块的设计优化
    - 增加数据压缩模块设计，减少内存带宽压力。

## 引用

1. https://www.knime.com/blog/a-friendly-introduction-to-deep-neural-networks
2. https://machine-learning.paperspace.com/wiki/activation-function
3. https://developer.nvidia.com/blog/accelerating-ai-training-with-tf32-tensor-cores/

## 本节视频

<html>
<iframe src="https://player.bilibili.com/player.html?aid=993183455&bvid=BV17x4y1T7Cn&cid=1046878675&page=1&as_wide=1&high_quality=1&danmaku=0&t=30&autoplay=0" width="100%" height="500" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
</html>