---
title: Study Notes of Machine Learning V1
date: 2019-12-11 21:12:56
tags: 
	- 学习笔记
	- 机器学习
categories:
	- 机器学习
---



### Basic concepts

#### Bias and Variance

`bias` is the difference between ground truth and expectation of prediction of your model, which measures fitting ability of models; `variance` is the difference between expectation of prediction of your model and prediction, that is difference between results of different datasets feeding in your model, which measures stability of models.

In supervised learning, model's generalized error consists of bias, variance and noise, which can be formulated by $Err(x)={Bias}^2+Variance+IrreducibleError$.

`bias` and `variance` are standing in two sides of a balance, so reduction of one will increase the other. Obviously, what we do in machine learning is bias-variance trade-off. Taking neural networks as an example, NNs have strong fitting ability, which means they have low bias and high variance, so we need to reduce variance with increasing bias.  

#### Overfitting and Underfitting

`overfitting` and `underfitting` are associated with `bias` and `variance`. Low bias and high variance lead to overfitting, which means your model is too complex for real data and of low generalization; Low variance and high bias lead to underfitting, which means your model is too simple to fit real data.

For a specific task, with increasing of model capacity (it represents complexity of models, which can be understood of number of parameters) the bias will reduce and variance will increase. Obviously, there is an optimal capacity leading best bias-variance trade-off.

Solutions for underfitting:

- More features
- More complex model

Solutions for overfitting:

- Data augmentation

- Early stopping

- Regularization

- Dropout 

- Ensemble 

- ~~Batch Normalization~~(BN can't prevent overfitting according to [paper]( https://arxiv.org/pdf/1611.03530.pdf ), it just can stablize training process)

  > 如果硬要说是防止过拟合，可以这样理解：BN每次的mini-batch的数据都不一样，但是每次的mini-batch的数据都会对moving mean和moving variance产生作用，可以认为是引入了噪声，这就可以认为是进行了data augmentation，而data augmentation被认为是防止过拟合的一种方法。因此，可以认为用BN可以防止过拟合。

#### Gradient Descent and Backpropagation

##### Gradient Descent

[Gradient descent](https://en.wikipedia.org/wiki/Gradient_descent) is a very important idea in deep learning and machine learning, which boosts the development of AI. Of course, some researches without gradient descent were proposed, such as Newton Method, however, GD is still mainstream algorithm in training models. 

> **Gradient descent** is a [first-order](https://en.wikipedia.org/wiki/Category:First_order_methods) [iterative](https://en.wikipedia.org/wiki/Iterative_algorithm) [optimization](https://en.wikipedia.org/wiki/Mathematical_optimization) [algorithm](https://en.wikipedia.org/wiki/Algorithm) for finding the minimum of a function. To find a [local minimum](https://en.wikipedia.org/wiki/Local_minimum) of a function using gradient descent, one takes steps proportional to the *negative* of the [gradient](https://en.wikipedia.org/wiki/Gradient) (or approximate gradient) of the function at the current point. 

What GD do is updating parameters of model with gradient so that make loss descent.

![Gradient Descent](https://hackernoon.com/hn-images/0*rBQI7uBhBKE8KT-X.png) 

![](https://hackernoon.com/hn-images/0*8yzvd7QZLn5T1XWg.jpg)

##### Backpropagation

[Backpropagation](https://skymind.ai/wiki/backpropagation) is an application of chain rule.

> A neural network propagates the signal of the input data forward through its parameters towards the moment of decision, and then *backpropagates* information about the error, in reverse through the network, so that it can alter the parameters. This happens step by step:
>
> - The network makes a guess about data, using its parameters
> - The network’s is measured with a loss function
> - The error is backpropagated to adjust the wrong-headed parameters

See this [post1](https://brilliant.org/wiki/backpropagation/) and [post2](http://galaxy.agh.edu.pl/~vlsi/AI/backp_t_en/backprop.html) for more details.

![backpropagation](https://pic2.zhimg.com/v2-8e30a45198d332dae959c57e04fdc267_r.jpg)

![](https://pic3.zhimg.com/v2-c96e284f4ab319a02fdd762367b58774_r.jpg)

#### Gradient vanishing and Gradient exploding

##### Gradient Vanishing

As more layers using certain activation functions are added to neural networks, the gradients of the loss function approaches zero, making the network hard to train. Certain activation functions, like the sigmoid function, squishes a large input space into a small input space between 0 and 1. Therefore, a large change in the input of the sigmoid function will cause a small change in the output. Hence, the derivative becomes small.

![sigmoid and its gradients](https://miro.medium.com/max/4384/1*6A3A_rt4YmumHusvTvVTxw.png)

What's worse, we have known that neural networks' training is based on back propagation and BP is based on chain rule, so if these values in BP are all small, such as 0.01, gradient of the first layer may be ${0.01}^n$ which is approaching to 0. According to Gradient Descent, the NNs won't be effectively trained due to 0 gradient.

Solutions: 

- Other activation function, such as ReLU 

  Rectifiers such as **ReLU** suffer less from Vanishing Gradient problem, because they only saturate in one direction, which maps $x$ into $max(0,x)$.

  However, there still dead ReLU problem, because negative value will be mapped to zero so that some neurons won't be activated. We can use small learning rate to partially solve this problem. Leaky ReLU and ELU are also solutions.

- LSTM 

  Long Short Term Memory Networks are generally used to tackle Vanishing Gradient problem when you are working on RNN. LTSMs help to solve long term dependencies and can memorize previous data easily.

- ~~Residual networks~~(according to paper of ResNet, it 's not for solving gradient vanishing)

  ![skip connection](https://miro.medium.com/max/770/1*mxJ5gBvZnYPVo0ISZE5XkA.png)

- Batch Normalization

  ![](https://miro.medium.com/max/1634/1*XCtAytGsbhRQnu-x7Ynr0Q.png)

  The nature of `Batch Normalization` is stablizing networks.

  Batch Normalization can normalize all data into same distribution (zero mean, one standard deviation Gaussian). From above image we can see that when $|x|$ is too large the gradient of output of sigmoid is approaching to zero. BN normalizes the input $|x|$ so that most of it can fall into center area (green region), which means it pull data from saturated region to non-saturated region.

  Let see BN from another perspective. For example, there is $f_2=f_1(w^T*x+b)$ in forward propagation, so  we can get $\frac{\partial f_2}{\partial x}=\frac{\partial f_2}{\partial f_1}w$ in back propagation and the value of w can lead to gradient vanishing problem. BN normalizes output of each layer into same mean and variance, which removes the effect of w's enlarging and reducing.

##### Gradient Exploding

> Exploding gradients are a problem when large error gradients accumulate and result in very large updates to neural network model weights during training. Gradients are used during training to update the network weights, but when the typically this process works best when these updates are small and controlled. When the magnitudes of the gradients accumulate,  an unstable network is likely to occur, which can cause poor prediction results or even a model that reports nothing useful what so ever. 
>
> Exploding gradients can cause problems in the training of artificial neural networks. When there are exploding gradients, an unstable network can result and the learning cannot be completed. The values of the weights can also become so large as to overflow and result in something called NaN values. NaN values, which stands for not a number, are values that represent an undefined or unrepresentable values. It is useful to know how to identify exploding gradients in order to correct the training.  - [Deep AI](https://deepai.org/machine-learning-glossary-and-terms/exploding-gradient-problem)

Solutions:

- Gradient clipping

  Gradient clipping places a predefined threshold on the gradients to prevent it from getting too large, and by doing this it doesn’t change the direction of the gradients it only change its length.

- LSTM

- Batch Normalization

- Regularization

  i.e. L2 regularization. $Loss=(y-W^Tx)^2+\alpha ||W||^2$. When exploding happens, norm of weights will be very large, but it can be partially limited by regularization term. What's more, we know that regularization can make weights small. 

#### Activation Function

The aim of using activation function is adding non-linearity into our models, which can boost their representation ability and solve problems that linear models fail to.

**Sigmoid**

![sigmoid](https://pic4.zhimg.com/80/v2-45975854a0d00d84e575851b278a88a9_hd.jpg)

![sigmoid and its gradients](https://pic4.zhimg.com/50/v2-c14e1eb9092aa343fdc89d5e244fac19_hd.jpg)

**tanh**

![tanh and its gradients](https://pic3.zhimg.com/80/v2-3528e66d0e12b35f778fe0ed21d2ced2_hd.jpg)

**ReLU**

![RELU](https://pic3.zhimg.com/80/v2-f6fb4bd3e9e4b343f87138b930792108_hd.jpg)

![RELU and its gradients](https://pic4.zhimg.com/50/v2-96c46de23a1f236918844dd490870c49_hd.jpg)

ReLU and its variants are based on $g(x;\alpha)=max(0,z)+\alpha * min(0,z)$. 

$\alpha = 0$, it is ReLU whose gradient is computed very fast because it is either 0 or 1;

$\alpha=-1$, it is absolute function $g(z)=|z|$; 

$\alpha$ is a small value, i.e. 0.01, it is **Leaky ReLU**;

$\alpha$ is a trainable parameter, it is **PReLU (parametric ReLU)**.

ReLU 会造成的低维数据的坍塌（collapse）。顾名思义，即是说，低维度的 feature 在通过 ReLU 的时候，这个 feature 会像塌方了一样，有一部分被毁掉了，或者说失去了。能恢复吗？能，但是基本无法百分百还原了。ReLU 这个东西，其实就是一个滤波器，只不过这个滤波器的作用域不是信号处理中的频域，而是特征域。那么滤波器又有什么作用呢？维度压缩，俗话说就是降维啦：如果我们有 m 个 feature 被送入 ReLU 层，过滤剩下 n 个（n<m），这不就是相当于对 feature 的维度进行了压缩，使其从 m 维变为 n 维嘛。那么，为什么低维数据流经非线性激活层会发生坍塌（信息丢失），而高维数据就不会呢？维度低的 feature，分布到 ReLU 的激活带上的概率小，因此经过后信息丢失严重，甚至可能完全丢失。而维度高的 feature，分布到 ReLU 的激活带上的概率大，虽然可能也会有信息的部分丢失，但是无伤大雅，大部分的信息仍然得以保留。所谓留得青山在，不愁没柴烧嘛。更何况被 ReLU 截杀的信息，可能只是一些无用游民（冗余信息）。当信息无法流过 ReLU 时，该神经元的输出就会变为 0。而在反向传播的过程中，ReLU 对 0 值的梯度为 0，即发生了梯度消失，这将导致神经元的权重无法再通过梯度下降法进行更新，这种现象被称为特征退化。所以这个神经元相当于死掉了，丧失了学习能力。我们说，一旦神经元的输出陷入 0 值，就无法恢复了。

对于一个数据，利用非线性激活层对其进行激活，其实是从该数据的信息中提取出其潜在的稀疏性，但是这种提取的结果是否正确，就要分情况讨论了。对于一个 M 维的数据，我们可以将其看成是在 M 维空间中的一个 M 维流形（manifold）。而其中的有用信息，就是在该 M 维空间中的一个子空间（子空间的维度记为 N 维，N<=M）中的一个 N 维流形。非线性激活层相当于压缩了这个 M 维空间的维度（还记得前面提过的维度压缩吗？）。若是该 M 维空间中的 M 维流形本来就不含有冗余信息（M=N），那么再对其进行维度压缩，必然导致信息的丢失。而维度低的数据其实就是这么一种情况：其信息的冗余度高的可能性本来就低，如果强行对其进行非线性激活（维度压缩），则很有可能丢失掉有用信息，甚至丢失掉全部信息（输出为全 0）。与非线性激活层不同的是，线性激活层并不压缩特征空间的维度。于是，我们得到了一条使用激活层的原则：对含有冗余信息的数据使用非线性激活（如 ReLU），对不含冗余信息的数据使用线性激活（如一些线性变换）。

由于冗余信息和非冗余信息所携带的有用信息是一样多的，因此在设计网络时，对内存消耗大的结构最好是用在非冗余信息上。根据以上的原则设计出来的结构，聪明的你想到了什么？ResNet。不得不赞叹 Kaiming He 的天才，ResNet 这东西，描述起来固然简单，但是对它的理解每深一层，就会愈发发现它的精妙及优雅，从数学上解释起来非常简洁，非常令人信服，而且直切传统痛点。ResNet 本质上就干了一件事：**降低数据中信息的冗余度**。具体说来，就是对非冗余信息采用了线性激活（通过 skip connection 获得无冗余的 identity 部分），然后对冗余信息采用了非线性激活（通过 ReLU 对 identity 之外的其余部分进行信息提取 / 过滤，提取出的有用信息即是残差）。由于 identity 之外的其余部分的信息冗余度较高，因此在对其使用 ReLU 进行非线性激活时，丢失的有用信息也会较少，ReLU 层输出为 0 的可能性也会较低。这就降低了在反向传播时 ReLU 的梯度消失的概率，从而便于网络的加深，以大大地发挥深度网络的潜能。

**Softmax** 

Softmax is always used as output layer of multi-classification models, which can be naturally represented as probability distribution of $n$ different values.
$$
g(z) = \frac{e^{z_i}}{\sum_{i=0}^n e^{z_i}}
$$
Softmax can solve overflow and underflow problems. The value can't be changed if input vectors minus or plus a scalar. Thus, a simple method is $x = x - \max_i x_i$ then do softmax.

An interesting Chinese post: https://zhuanlan.zhihu.com/p/45014864

**Softplus**

It is a smooth version of ReLu. However, its performance is not better than ReLu.

**Maxout**

Actually Maxout is not a fixed function, however, it's a piecewise linear function which can fit any convex function. We can regard Maxout as an layer which is similar to pooling layer and conv layer. $k$ is a hyper-parameter in Maxout,which is like $p$ in dropout.

i.e. $k=5$ 

![maxout](Study-Notes-of-Machine-Learning-V1/20160103165828920.png)

The Maxout layer has 5 neurons so that parameters increase $k=5$ folds, and the output is $max(z_1,z_2,z_3,z_4,z_5)$.

**Radial Basis Function (RBF)**

$h_i=e^{-\frac{1}{\sigma_i ^ 2}||W_{:,i}-x||}$ It is rarely used in NNs because most x can be mapped to zero, which is difficult to optimize in training.

#### Regularization

在一定程度上限制了梯度的波动，从而降低了参数的搜索空间，因此相当于降低了模型的复杂度，所以提高了泛化能力.

##### L1/L2 Regularization

L1/L2 regularization is like a priori which limits distribution of parameters, so it can reduce model complexity. Meanwhile, if one feature has a very high weight and prediction is very close to ground truth, the loss would be huge due to L1/L2 term, which avoids high weights of one or several features. 

When priori is Laplace distribution, the regularization term is L1 from; When priori is Gaussian distribution, the regularization term is L2 term. We know that $p(w=0)$ is highest in Laplace distribution and $p(w \rightarrow 0)$ is highest in Gaussian distribution. More interesting ideas in this Chinese [post](https://mp.weixin.qq.com/s?__biz=MzU0MDQ1NjAzNg==&mid=2247484454&idx=1&sn=b3404eefaa68a78b770adb092d2fb48f&chksm=fb39a12dcc4e283b132138583ecd9848aa175b4cffd4036be07a2e132cb616e18ce8fe8c9adb&token=1151312247&lang=zh_CN&scene=21#wechat_redirect)!

L1 regularization is sum of absolute values of all weights, which is marked as $||w||_1=\sum|w_i|$. L1 regularization can make some $w_i$ zero which leads to sparse matrix of weights. 

{% raw %}
$$
\begin{align}
L &= L_0 + \frac{\lambda}{n} \sum_{w}|w|\\
\frac{\partial L}{\partial w}  &= \frac{\partial L_0}{\partial w} + \frac{\lambda}{n}sgn(w) \\
w &:= w - \alpha*\frac{\partial L}{\partial w} \ (where \ \alpha>0)\\
  &:= w - \alpha * \frac{\partial L_0}{\partial w} - \alpha * \frac{\lambda}{n}sgn(w) \\
\end{align}
$$

{% endraw %}

From above equation, when $w$ is negative, updated $w$ will be greater; when $w$ is positive, updated $w$ will be smaller. Obviously, $w$ will move to zero. L1 regularization leads weights sparsity, which is important for online learning because sparsity can reduce memory and time of prediction.

L2 regularization is sum of square values of all weights, which is marked as $||w||_2=\sum w_i^2$. L2 regularization can make some $w_i$ approach to zero but not zero, which can prevent models from overfitting.

{% raw %}
$$
\begin{align}
L &= L_0 + \frac{\lambda}{2n} \sum_{w}w^2\\
\frac{\partial L}{\partial w}  &= \frac{\partial L_0}{\partial w} + \frac{\lambda}{n}w \\
w &:= w - \alpha*\frac{\partial L}{\partial w} \ (where \ \alpha>0)\\
  &:= w - \alpha * \frac{\partial L_0}{\partial w} - \alpha * \frac{\lambda}{n}w \\
  &:= w(1-\alpha * \frac{\lambda}{n}) - \alpha * \frac{\partial L_0}{\partial w}
\end{align}
$$
{% endraw %}

From above equation, $(1-\alpha * \frac{\lambda}{n})<1$, so $w$ will be reduced dramatically.

##### Batch Normalization

With deep layers of NNs, the input data distribution before each layer will change, which is "Internal Covariate Shift". That leads to slow convergence and gradient vanishing (because the distribution will be approaching to boundary of activation function).

A detail Chinese post : https://zhuanlan.zhihu.com/p/43200897

BN solves **Internal Covariate Shift** and makes all input data feeding in each layer same distribution.

In training phase, BN uses mini-batch data to compute mean and variance of $x_i$, so it's normalization of single neuron.

![Batch Normalization](Study-Notes-of-Machine-Learning-V1/image-20191115210001924.png)

Re-sacling invariant: normalization doesn't change outputs of activation function.

> 为了解决神经元输入分布偏移的情况，对每个神经元的带权输入（一个mini-batch上）进行标准化，把发生偏移的带权输入拉回到同一分布上。首先这样做解决了上述中第一个分布发生变化的问题，使得每个神经元都会适当的激活，同时标准化使得带权输入分布在0附近更容易激活神经元解决梯度消失问题。但只做标准化容易把之前学习到的特征丢弃掉，这时候就引入了伸缩因子和平移因子（这两个参数是需要学习的），相当于对标准化后的带权输入又做了个线性变换。这样使得所有神经元带权输入都有一个同一的分布，但又保持了相对差异。

这就是Batch Normalization强大的地方，如果只做normalize在某些情况下会出现问题，比如对象是Sigmoid函数的output，而且output是分布在Sigmoid函数的两侧，normalize会强制把output分布在Sigmoid函数的中间的非饱和区域，这样会导致这层网络所学习到的特征分布被normalize破坏。而上面算法的最后一步，scale and shift可以令零均值单位方差的分布（normalize之后的分布）通过调节gamma和beta变成任意更好的分布（对于喂给下一层网络来说）。因为这个gamma和beta是在训练过程中可以学习得到参数。最极端的情况就是当gamma = sqrt(var(x)) 和 beta = mean(x)的时候，就是题主所说的“打回原形”了，这种原来的论文中，Sergey说的是至少能够使特征分布回到normalize之前的分布，并不是每一层学习到的gamma和beta都会抵消之前的normalize的操作。我的理解是完整的BN通过normalize和scale & shift两步的操作提供更高的flexibility，对于每层的output既可以是零均值单位方差的分布，也可以是分布于Sigmoid两端饱和区域的分布，或者其他任意的分布。

More normalization: Layer Normalization/Group Normalization/Weight Normalization/Cosine Normalization/ $SELU$<sup>[1](https://arxiv.org/abs/1706.02515)  [2](https://zhuanlan.zhihu.com/p/27362891)</sup>

[Why is BN so powerful?](https://www.zhihu.com/question/38102762)

那么BN有效的真正原因到底是什么呢？这还要从深度网络的损失曲面（Loss Surface）说起，在深度网络叠加大量非线性函数方式来解决非凸复杂问题时，损失曲面形态异常复杂，大量空间坑坑洼洼相当不平整，也有很多空间是由平坦的大量充满鞍点的曲面构成，训练过程就是利用SGD在这个复杂平面上一步一步游走，期望找到全局最小值，也就是曲面里最深的那个坑。所以在SGD寻优时，在如此复杂曲面上寻找全局最小值而不是落入局部最小值或者被困在鞍点动弹不得，可想而知难度有多高。

有了损失曲面的基本概念，我们回头来看为何BN是有效的。研究表明，BN真正的用处在于：通过上文所述的Normalization操作，使得网络参数重整（Reparametrize），它对于非线性非凸问题复杂的损失曲面有很好的平滑作用，参数重整后的损失曲面比未重整前的参数损失曲面平滑许多。

An interesting Chinese [post](https://www.jiqizhixin.com/articles/2018-08-29-7) of normalization!

Why can't BN be used with dropout or other regularization methods?

> **我们从二者的基本出发点看，batch norm本质是控制相空间的体积，使得在映射过程中保证信号可以不衰减不爆炸，就是Jacobian的特征值的分布要合理，让系统处于一个接近临界点的状态；dropout的作用是保证系统在扰动下稳定或鲁棒，对输入信号或者系统参数系统结构的扰动不带来性能的显著恶化。定性的看，batch norm要保证信息流深，dropout要保证信息流阔。从这个角度看，二者匹配的就分别是深的网络和宽的网络，或者说解决的是这两类结构可能存在的缺陷，深的网络需要信息流足够深从而可以穿透网络，宽的网络需要信息流在相当的一个参数空间体积下都能有一定流量，否则好的解空间就容易被淹没或者错过。实际上二者的作用有相似和互补，但又有差异，这种差异使得二者分别和现有CNN网络的两部分结构匹配起来更合理一些，但并不是说不能混合起来用，BN对于全连接的部分也同样有效，dropout对CNN也一样有改善，甚至我觉得dropout应该持续作用于CNN部分，这种约束对于保证系统的鲁棒性防止过拟合会很有效的，对防范对抗攻击也会起作用，直观上使用了dropout的系统应该landscape更平缓一些。**
>
> source：https://www.zhihu.com/question/294560934/answer/511857321

A Chinese post: https://zhuanlan.zhihu.com/p/61725100 

[Understanding the Disharmony between Dropout and Batch Normalization by Variance Shift]( https://arxiv.org/abs/1801.05134 )

The key idea is variance shift. Dropout will randomly drop neurons, which makes variance change. However, BN is trying to keep variance stable. Obviously, that paradox leads variance shift and it will be increasingly serious with deeper and deeper layers.

In the other hand, dropout makes we cannot compute mean and variance due to we don't have integrated data and some of that will be randomly dropped.

One solution is put Dropout behind BN and another is Gaussian Dropout. But combination of BN and Dropout is not far better than only BN.

**Advantages**

- Speed up convergence
- Larger learning rate
- Lower requirement of parameter initialization
- Avoid gradient vanishing
- Introduce random noise as regularization to get better generalization

**Disadvantages**

- Poor performance on small batch size

  - batch size 设置得较小训练出来的模型相对大batch size训练出的模型泛化能力更强，在测试集上的表现更好，而太大的batch size往往不太Work，而且泛化能力较差。但是背后是什么原因造成的，目前还未有定论，持不同看法者各持己见。
  - can't apply on **online-learning**, because online models update parameters in single instance without mini-batch structure

- no application on dynamical networks, such as RNN, because length of their inputs is variant

  - 对于RNN来说，尽管其结构看上去是个静态网络，但在实际运行展开时是个动态网络结构，因为输入的Sequence序列是不定长的，这源自同一个Mini-Batch中的训练实例有长有短。对于类似RNN这种动态网络结构，BN使用起来不方便，因为要应用BN，那么RNN的每个时间步需要维护各自的统计量，而Mini-Batch中的训练实例长短不一，这意味着RNN不同时间步的隐层会看到不同数量的输入数据，而这会给BN的正确使用带来问题。假设Mini-Batch中只有个别特别长的例子，那么对较深时间步深度的RNN网络隐层来说，其统计量不方便统计而且其统计有效性也非常值得怀疑。另外，如果在推理阶段遇到长度特别长的例子，也许根本在训练阶段都无法获得深层网络的统计量。综上，在RNN这种动态网络中使用BN很不方便，而且很多改进版本的BN应用在RNN效果也一般。

- 对于有些像素级图片生成任务来说，BN效果不佳.对于图片分类等任务，只要能够找出关键特征，就能正确分类，这算是一种粗粒度的任务，在这种情形下通常BN是有积极效果的。但是对于有些输入输出都是图片的像素级别图片生成任务，比如图片风格转换等应用场景，使用BN会带来负面效果，这很可能是因为在Mini-Batch内多张无关的图片之间计算统计量，弱化了单张图片本身特有的一些细节信息。

- 训练时和推理时统计量不一致

  - 对于BN来说，采用Mini-Batch内实例来计算统计量，这在训练时没有问题，但是在模型训练好之后，在线推理的时候会有麻烦。因为在线推理或预测的时候，是单实例的，不存在Mini-Batch，所以就无法获得BN计算所需的均值和方差，一般解决方法是采用训练时刻记录的各个Mini-Batch的统计量的数学期望，以此来推算全局的均值和方差，在线推理时采用这样推导出的统计量。虽说实际使用并没大问题，但是确实存在训练和推理时刻统计量计算方法不一致的问题。

  - 对于BN，在训练时，是对每一批的训练数据进行归一化，也即用每一批数据的均值和方差。而在测试时，比如进行一个样本的预测，就并没有batch的概念，因此，这个时候用的均值和方差是全量训练数据的均值和方差，这个可以通过移动平均法求得。

    对于BN，当一个模型训练完成之后，它的所有参数都确定了，包括均值和方差，gamma和beta。BN训练时为什么不用全量训练集的均值和方差呢？因为用全量训练集的均值和方差容易过拟合，对于BN，其实就是对每一批数据进行归一化到一个相同的分布，而每一批数据的均值和方差会有一定的差别，而不是用固定的值，这个差别实际上能够增加模型的鲁棒性，也会在一定程度上减少过拟合。也正是因此，BN一般要求将训练集完全打乱，并用一个较大的batch值，否则，一个batch的数据无法较好得代表训练集的分布，会影响模型训练的效果。

BN can be put before activation function (original paper) or behind the function (some researches show that models get better performance).

**Variants of BN**

- Layer Normalization
  - all neurons in the same layer as statistical area
- Instance Normalization
  - CNN中将同一卷积层中每个卷积核对应的输出通道单独作为自己的统计范围
- Group Normalization
  - 对CNN中某一层卷积层的输出或者输入通道进行分组，在分组范围内进行统计

##### Dropout

Dropout is a popular regularization in deep learning. It can be viewed as dropping partial neurons with probability $p$ in training phase and outputs of those dropped neurons are zero. **It's ensemble of some sub-nets so that can reduce overfitting and increase generalization.**

Dropout is similar with average of multiple DNNs, so it has effect of vote.

When some neurons in hidden layers are dropped, fully connected networks get sparsity so that reducing collaborative effect of different features. In other words, some features may be dependent on other features (nodes), so NNs can be more robust by dropout which effectively organizes that some features are available only when some specific features survive.

#### Normalization and Standardization

**Normalization**

Mapping data into a specific interval, usually $[-1,1]$, $[0,1]$.

- Min-Max Normalization

$$
x'=\frac{x-min(x)}{max(x)-min(x)}
$$



**Standardization**

Mapping data into normal distribution.
$$
x'=\frac{x-\mu}{\sigma}
$$
Normalization and Standardization can improve model precision(normalization) and speed up model convergence(standardization).

#### Metrics

##### Confusion Matrix

|       | Positive | Negative |
| :---: | :------: | :------: |
| True  |    TP    |    TN    |
| False |    FP    |    FN    |

##### Accuracy / Precision / Recall / F1

$ACC=\frac{TP+TN}{TP+TN+FP+FN}$

$Precision=\frac{TP}{TP+FP}$

$Recall=\frac{TP}{TP+FN}$

$\frac{2}{F1}=\frac{1}{P}+\frac{1}{R}$

##### AUC (**Area Under the** Receiver Operating Characteristics)

![AUC](https://miro.medium.com/max/722/1*pk05QGzoWhCgRiiFbz-oKQ.png)

Randomly choosing one sample, s1, from class A and one sample, s2, from class B, then predicting them by your classifier. P1 is the probability of your model predicting s1 as class A; P2 is the probability of model predicting s2 as class B. The probability of P1 > P2 is AUC.

它和Wilcoxon-Mann-Witney Test是等价的。这个等价关系的证明留在下篇帖子中给出。而Wilcoxon-Mann-Witney Test就是测试任意给一个正类样本和一个负类样本，正类样本的score有多大的概率大于负类样本的score。有了这个定义，我们就得到了另外一中计 算AUC的办法：得到这个概率。
![](Study-Notes-of-Machine-Learning-V1/image-20191116013843143.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116013910117.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116013921388.png)

#### Search of Hyperparameters

- Grid Search

- Random Search

- Bayesian Optimization

More details in this [post](http://neupy.com/2016/12/17/hyperparameter_optimization_for_neural_networks.html)!

#### Information Theory

Self-information: $I(x)=-logP(x)$

Information-entropy: $H(X)=E_{X \in P}[I(x)]=-\sum_{x \in X} P(x)logP(x)$

Kullback-Leibler divergence: $D_P(Q)=E_{X \in P}[log\frac{P(x)}{Q(x)}]=\sum_{x \in X}P(x)log \frac{P(x)}{Q(x)}$

Cross-entropy: $H_P(Q)=-E_{X \in P}logQ(x)=-\sum_{x \in X}P(x)log Q(x)$

$H_P(Q)=H(P)+D_P(Q)$

### Cost Function, Loss Function and Objective Function

Loss function is defined in single sample. Cost function is defined in whole dataset. In fact, you can use both loss function and cost function because they are same. Objective function is cost function plus regularization terms.

$loss=\frac{1}{N}\sum_{i=1}^NL(y_i,f(x_i))$ is also called empirical risk; $RegularizationTerm=\lambda R(f)$ is also called structural risk. Objective function is $Obj=\frac{1}{N}\sum_{i=1}^NL(y_i,f(x_i))+\lambda R(f)$.

Common loss functions:

- Classification

  - Log loss \ cross entropy

    $J(\theta)=-\frac{1}{m}\sum_{i=1}^m[y^{(i)} log \ h_\theta(x^{(i)})+(1-y^{(i)})log \ (1-h_\theta(x^{(i)}))]$

  - Focal loss

    ![Focal Loss](https://img-blog.csdnimg.cn/20190719190148967.png)

  - KL divergence / Relative entropy

    $L=D_{KL}(p|q)=\sum_x{p(x)}{\log \frac{p(x)}{q(x)}}$

  - Exponential loss (AdaBoost)

    $L=e^{-y_if(x_i;w)}$

  - Hinge loss (SVM)

    $L=max(0, 1-yf(x;w))$

- Regression

  - MSE / Quadratic loss

    ![MSE](Study-Notes-of-Machine-Learning-V1/1529558773906.png)

  - MAE

    ![MAE](Study-Notes-of-Machine-Learning-V1/1529558773392.png)

  - Huber loss / Smooth Mean Absolute Error

    ![Huber Loss](Study-Notes-of-Machine-Learning-V1/1529558774147.png)

  - Smooth L1 Loss

    ![Smooth L1 Loss](Study-Notes-of-Machine-Learning-V1/image-20191117220354211.png)

  - Log cosh loss

    ![Log cosh loss](Study-Notes-of-Machine-Learning-V1/1529558774452.png)

  - Quantile loss

    ![Quantile Loss](Study-Notes-of-Machine-Learning-V1/1529558775749.png)

    

### Optimizers

During the training process, we tweak and change the parameters (weights) of our model to try and minimize that loss function, and make our predictions as correct as possible. Optimizers tie together the loss function and model parameters by updating the model in response to the output of the loss function.

[post1](https://leovan.me/cn/2018/02/optimization-methods-for-deeplearning/) [post2](http://ruder.io/optimizing-gradient-descent/index.html#fn3)

#### Gradient Descent (GD)

Gradient Descent is a way to minimize an objective function $J(\theta)$ parameterized by a model's parameters $\theta \in R^d$ by updating the parameters in the opposite direction of the gradient of the objective function $\triangledown_\theta J(\theta)$ w.r.t. to the parameters. The learning rate $\eta$ determines the size of the steps we take to reach a local minimum.

$\theta:=\theta-\eta \frac{\partial J(\theta)}{\partial \theta}=\theta-\eta {\triangledown_\theta J(\theta)}$

##### Batch Gradient Descent

It's also called vanilla gradient descent, which computes the gradient of the cost function for the entire training dataset: $\theta:=\theta-\eta \frac{\partial J(\theta)}{\partial \theta}$. As we need to calculate the gradients for whole dataset to update parameters once, batch gradient descent can be very slow and is intractable for datasets that don't fit in memory. Batch gradient descent also doesn't allow us to update models online, aka online learning.

##### Stochastic Gradient Descent (SGD)

SGD updates a parameter for each training example: $\theta := \theta-\eta \triangledown_\theta J(\theta;x^{(i)};y^{(i)})$, so it is much faster and can be used to learn online. SGD performs frequent updates with a high variance that cause the objective function to fluctuate heavily. While batch gradient descent converges to the minimum of the basin the parameters are placed in, SGD's fluctuation, on the one hand, enables it to jump to new and potentially better local minima. On the other hand, this ultimately complicates convergence to the exact minimum, as SGD will keep overshooting. However, it has been shown that when we slowly decrease the learning rate, SGD shows the same convergence behaviour as batch gradient descent, almost certainly converging to a local or the global minimum for non-convex and convex optimization respectively.

##### Mini-Batch Gradient Descent

Mini-Batch Gradient Descent is a trade-off of batch GD and SGD, which updates a parameter for every mini-batch of n training examples: 

$\theta := \theta-\eta \triangledown_\theta J(\theta;x^{(i:i+n)};y^{(i:i+n)})$. 

##### Mini-Batch Stochastic Gradient Descent

It's easy to think of this method. Mini-Batch SGD updates a parameter for one example in a mini-batch of n training examples. In fact, what we said SGD nowadays is Mini-Batch SGD which is much faster and performs very well. Mini-Batch SGD is absolutely one of most popular optimization algorithms of ML/DL, especially for large scale dataset.

##### Challenges

Choosing a proper learning rate can be difficult. A learning rate that is too small leads to painfully slow convergence, while a learning rate that is too large can hinder convergence and cause the loss function to fluctuate around the minimum or even to diverge.

Learning rate schedules try to adjust the learning rate during training by e.g. annealing, i.e. reducing the learning rate according to a pre-defined schedule or when the change in objective between epochs falls below a threshold. These schedules and thresholds, however, have to be defined in advance and are thus unable to adapt to a dataset's characteristics.

Additionally, the same learning rate applies to all parameter updates. If our data is sparse and our features have very different frequencies, we might not want to update all of them to the same extent, but perform a larger update for rarely occurring features.

Another key challenge of minimizing highly non-convex error functions common for neural networks is avoiding getting trapped in their numerous suboptimal local minima. The difficulty arises in fact not from local minima but from saddle points, i.e. points where one dimension slopes up and another slopes down. These saddle points are usually surrounded by a plateau of the same error, which makes it notoriously hard for SGD to escape, as the gradient is close to zero in all dimensions.

#### Momentum

SGD has trouble navigating ravines, i.e. areas where the surface curves much more steeply in one dimension than in another, which are common around local optima. In these scenarios, SGD oscillates across the slopes of the ravine while only making hesitant progress along the bottom towards the local optimum. Momentum is a method that helps accelerate SGD in the relevant direction and dampens oscillations. It does this by adding a fraction $\gamma$ of the update vector of the past time step to the current update vector: 

$v_t=\gamma v_{t-1} + \eta \triangledown_\theta J(\theta)$

$\theta := \theta - v_t$

The momentum term $\gamma$ is usually set to 0.9. The momentum term increases for dimensions whose gradients point in the same directions and reduces updates for dimensions whose gradients change directions. As a result, we gain faster convergence and reduced oscillation.

####  Nesterov accelerated gradient (NAG)

as we reach the minima i.e the lowest point on the curve ,the momentum is pretty high and it doesn’t knows to slow down at that point due to the high momentum which could cause it to miss the minima entirely and continue movie up. 

NAG is a way to give our momentum term this kind of prescience. We know that we will use our momentum term $\gamma v_{t-1}$ to move the parameters $\theta$. Computing $\theta-\gamma v_{t-1}$ thus gives us an approximation of the next position of the parameters which gives us a rough idea where our parameters are going to be. We can now effectively look ahead by calculating the gradient not w.r.t. to our current parameters $\theta$ but w.r.t. the approximate future position of our parameters:

$v_t=\gamma v_{t-1} + \eta \triangledown_\theta J(\theta - \gamma v_{t-1})$

$\theta := \theta - v_t$

#### Adagrad

Adagrad is an algorithm for gradient-based optimization that does just this: It adapts the learning rate to the parameters, performing smaller updates (i.e. low learning rates) for parameters associated with frequently occurring features, and larger updates (i.e. high learning rates) for parameters associated with infrequent features. For this reason, it is well-suited for dealing with sparse data. 

$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{\epsilon+\sum_{i=0}^t g_t^2}}\cdot g_t $

One of Adagrad's main benefits is that it eliminates the need to manually tune the learning rate. Most implementations use a default value of 0.01 and leave it at that.

Adagrad's main weakness is its accumulation of the squared gradients in the denominator: Since every added term is positive, the accumulated sum keeps growing during training. This in turn causes the learning rate to shrink and eventually become infinitesimally small, at which point the algorithm is no longer able to acquire additional knowledge.

#### AdaDelta 

Adadelta is an extension of Adagrad that seeks to reduce its aggressive, monotonically decreasing learning rate. Instead of accumulating all past squared gradients, Adadelta restricts the window of accumulated past gradients to some fixed size w.

$v_t=\gamma *v_{t-1}+(1-\gamma)*g_t^2$

$\triangle \theta_t = \frac{\eta}{\sqrt{v_t+\epsilon}}*g_t$

$\eta_t=\gamma \eta_{t-1}+(1-\gamma) \triangle \theta_t^2$

$\theta_{t+1}=\theta_{t}-\frac{\eta_{t}}{\sqrt{v_t+\epsilon}}*g_t$

#### RMSprop

RMSprop and Adadelta have both been developed independently around the same time stemming from the need to resolve Adagrad's radically diminishing learning rates. RMSprop in fact is identical to the first update vector of Adadelta that we derived above:

$v_t=\gamma *v_{t-1}+(1-\gamma)*g_t^2$

$\theta_{t+1}=\theta_{t}-\frac{\eta}{\sqrt{v_t+\epsilon}}*g_t$

RMSprop as well divides the learning rate by an exponentially decaying average of squared gradients. Hinton suggests $\gamma$ to be set to 0.9, while a good default value for the learning rate $\eta$ is 0.001.

#### Adam

Adaptive Moment Estimation (Adam) is another method that computes adaptive learning rates for each parameter. In addition to storing an exponentially decaying average of past squared gradients $v_t$ like Adadelta and RMSprop, Adam also keeps an exponentially decaying average of past gradients $m_t$, similar to momentum. Whereas momentum can be seen as a ball running down a slope, Adam behaves like a heavy ball with friction, which thus prefers flat minima in the error surface.

$m_t=\beta_1 m_{t-1} + (1-\beta_1)g_t$

$v_t=\beta_2 v_{t-1}+(1-\beta_2)g_t^2$

$\hat{m_t}=\frac{m_t}{1-\beta_1^t}$

$\hat{v_t}=\frac{v_t}{1-\beta_2^t}$

$\theta_{t+1}=\theta_t - \frac{\eta}{\sqrt{\hat{v_t}}+\epsilon}\hat{m_t}=\theta_t - \frac{\eta}{\sqrt{\hat{v_t}}+\epsilon}(\beta_1\hat{m_{t-1}}+\frac{(1-\beta_1)g_t}{1-\beta_1^t})$

The authors propose default values of 0.9 for $\beta_1$, 0.999 for $\beta_2$, and $10^{-8}$ for $\epsilon$. Adam can be viewed as a combination of RMSprop and momentum.

Adam is good, but there are still some problems. Some researches are [Improving Generalization Performance by Switching from Adam to SGD](https://arxiv.org/abs/1712.07628).

#### AdaMax

$u_t=max(\beta_2 \cdot v_{t-1}, |g_t|)$

$\theta_{t+1}=\theta_t - \frac{\eta}{u_t} \hat{m_t}$

Good default values are given $\eta=0.002$, $\beta_1=0.9$, and $\beta_2=0.999$. 

#### Nadam

Nadam (Nesterov-accelerated Adaptive Moment Estimation) thus combines Adam and NAG. Nadam = Adam + Nesterov.

$m_t=\beta_1 m_{t-1} + (1-\beta_1)g_t$

$v_t=\beta_2 v_{t-1}+(1-\beta_2)g_t^2$

$\hat{m_t}=\frac{m_t}{1-\beta_1^t}$

$\hat{v_t}=\frac{v_t}{1-\beta_2^t}$

$\theta_{t+1}=\theta_t - \frac{\eta}{\sqrt{\hat{v_t}}+\epsilon}(\beta_1\hat{m_t}+\frac{(1-\beta_1)g_t}{1-\beta_1^t})$

#### AMSGrad

The exponential moving average of past squared gradients as a reason for the poor generalization behaviour of adaptive learning rate methods. Recall that the introduction of the exponential average was well-motivated: It should prevent the learning rates to become infinitesimally small as training progresses, the key flaw of the Adagrad algorithm. However, this short-term memory of the gradients becomes an obstacle in other scenarios.

$m_t=\beta_1 m_{t-1} + (1-\beta_1)g_t$

$v_t=\beta_2 v_{t-1}+(1-\beta_2)g_t^2$

$\hat{v_t}=max(\hat{v}_{t-1},v_t)$

$\theta_{t+1}=\theta_t - \frac{\eta}{\sqrt{\hat{v_t}}+\epsilon}m_t$

#### Follow the Regularized Leader (FTRL)

[http://vividfree.github.io/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0/2015/12/05/understanding-FTRL-algorithm](http://vividfree.github.io/机器学习/2015/12/05/understanding-FTRL-algorithm)

在工业界，越来越多的业务需要大规模机器学习，不单参与训练的数据量大，模型特征量的规模也大。例如点击率预估，训练数据量在TB量级，特征量在亿这个量级，业内常用LR（Logistic Regression）和FM（Factorization Machines）为点击率预估建模。对LR、FM这类模型的参数学习，传统的学习算法是batch learning算法，它无法有效地处理大规模的数据集，也无法有效地处理大规模的在线数据流。这时，有效且高效的online learning算法显得尤为重要。

SGD算法是常用的online learning算法，它能学习出不错的模型，但学出的模型不是稀疏的。为此，学术界和工业界都在研究这样一种online learning算法，它能学习出有效的且稀疏的模型。FTRL（Follow the Regularized Leader）算法正是这样一种算法，它由Google的H. Brendan McMahan在2010年提出的，作者后来在2011年发表了一篇关于FTRL和AOGD、FOBOS、RDA比较的论文，2013年又和Gary Holt, D. Sculley, Michael Young等人发表了一篇关于FTRL工程化实现的论文。如论文的内容所述，FTRL算法融合了RDA算法能产生稀疏模型的特性和SGD算法能产生更有效模型的特性。它在处理诸如LR之类的带非光滑正则化项（例如1范数，做模型复杂度控制和稀疏化）的凸优化问题上性能非常出色，国内各大互联网公司都已将该算法应用到实际产品中。

<img src="Study-Notes-of-Machine-Learning-V1/image-20191116190711218.png" style="zoom:150%;" />

![](Study-Notes-of-Machine-Learning-V1/image-20191116190725165.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116190735241.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116190745096.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116190820127.png)

Equation above can be equation below which is SGD equation:

![](Study-Notes-of-Machine-Learning-V1/image-20191116190900745.png)

In conclusion, in equation of FTRL left side is equal to SGD and right side is L1 norm which can make sparse solutions.

SGD引入L1实际上不太容易产生稀疏解，但是按照上面的推导来看ftrl就是在SGD的基础上引入了L1.在GD算法下，L1正则化通常能得到更加稀疏的解；可是在SGD算法下模型迭代并不是沿着全局梯度下降，而是沿着某个样本的梯度进行下降，这样即使是L1正则也不一定能得到稀疏解。在后面的优化算法中，稀疏性是一个主要追求的目标。

https://courses.cs.washington.edu/courses/cse599s/12sp/scribes/Lecture8.pdf

http://www.eecs.tufts.edu/~dsculley/papers/ad-click-prediction.pdf

不论怎样，简单截断、TG、FOBOS都还是建立在SGD的基础之上的，属于梯度下降类型的方法，这类型方法的优点就是精度比较高，并且TG、FOBOS也都能在稀疏性上得到提升。但是有些其它类型的算法，例如RDA从另一个方面来求解Online Optimization并且更有效地提升了特征权重的稀疏性。RDA（Regularized Dual Averaging）是微软十年的研究成果。

![FOBOS, RDA and FTRL](Study-Notes-of-Machine-Learning-V1/image-20191116191751785.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116191804483.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116191913836.png)

有效稀疏：所谓有效稀疏，就是要避免某个权重还没训练充分就被稀疏成0，这时候，累计梯度的作用就出来了，这保证了某权重被训练到一定程度了，才开始稀疏。SGD为啥不行？稀疏手段还是次要的，主要还是没有做有效稀疏，而有效稀疏的关键是累计梯度。

#### Lookahead

https://arxiv.org/abs/1907.08610v1

#### Newton's method and Quasi-Newton methods

##### Newton's Method

[Newton's method](https://en.wikipedia.org/wiki/Newton's_method) finds next position by tangent line of current position.

$x_{n+1}=x_{n}-\frac{f(x_{n})}{f'(x_n)}$

Newton's method is faster than gradient descent because it is second convergence. However, Newton's method needs to compute inverse matrix of Hessian matrix of objective function, so the computation is very complex.

$\theta := \theta - H^{-1}J(\theta)$

##### Quasi-Newton methods

Fitting inverse matrix of Hessian matrix by positive matrix

- BFGS
- L-BFGS

### Models

#### Linear Regression

The most basic regression model, $y=w^Tx+b$.

#### Logistic Regression

LR is initially proposed for binary classification, i.e. $y_1$ and $y_2$.

{% raw %}
$$
\begin{align}
p(y=y_1|x) &= \frac{p(x|y_1)p(y_1)}{p(x)} \\
&= \frac{p(x|y_1)p(y_1)}{p(x|y_1)p(y_1)+p(x|y_2)p(y_2)} \\
&= \frac{1}{1+\frac{p(x|y_2)p(y_2)}{p(x|y_1)p(y_1)}} \\
\end{align}
\\ Assumed \ ln\frac{p(x|y_1)p(y_1)}{p(x|y_2)p(y_2)}=a, \ so \ p(y=y_1|x)=\frac{1}{1+e^{-a}}
$$
{% endraw %}

We know that LR formulation is as following:

$h_{\theta}=\frac{1}{1+e^{-z}}$

$z=\theta^Tx+b$

The likelihood function is $L(\theta)=\prod_{i=1}^N h_\theta(x)^{y_i}(1-h_\theta(x))^{1-y_i}$.

Thus, loss function is $J(\theta)=-L(\theta)$.

#### KNN

[KNN](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm) is a non-parametric method used for classification and regression. The lower the k is, the more sensitive the model is.

####  SVM

The key idea of SVM is maximizing margins. Margin is the distance between hyper-plane and support vectors. Support vectors are those nearest points to hyper-plane.

Hyper-plane: $w^Tx+b=0$

Functional margin: $\hat{\gamma} = |w^Tx+b| = y(w^Tx+b)$

Minimum of functional margin in all samples: $\hat{\gamma}=min \hat{\gamma_i}$

Geometric margin: $\gamma = \frac{\hat{\gamma}}{||w||}$

In SVM, we hope maximize the margin, so

{% raw %}
$$
\max_{w,b} \ \gamma \\
s.t. y_i(\frac{w^Tx+b}{||w||}) \ge \gamma, i=1,2,\cdots,N \\
\Rightarrow \max_{w,b} \frac{\hat{\gamma}}{||w||} \\
s.t. y_i(w^Tx_i+b) \ge \hat{\gamma}, i=1,2,\cdots,N
$$
{% endraw %}

Functional margin is minimum distance between sample point and hyper-plane. If making functional margin be $1$, distance between other points and hyper-plane is more than $1$. So,

{% raw %}
$$
\max_{w,b} \frac{1}{||w||} \\
\Rightarrow \min_{w,b} \frac{1}{2}||w||^2 \\
s.t. y_i(w^Tx_i+b)-1 \ge 0, i=1,2,\cdots,N \\
L(w,b,\alpha) = \frac{1}{2}||w||^2+\alpha_i \sum_{i=1}^N[-y_i(w^Tx+b)+1]=\frac{1}{2}||w||^2-\sum_{i=1}^N \alpha_iy_i(w^Tx_i+b)+\sum_{i=1}^N \alpha_i \\
\because \alpha_i \ge 0 \\
\therefore \max_\alpha L(w,b,\alpha)=\frac{1}{2}||w||^2 \\
\therefore \min_{w,b} \frac{1}{2}||w||^2 \Rightarrow \min_{w,b}\max_\alpha L(w,b,a) \\
According \ to \ Lagrange \ duality \ problem, 
\min_{w,b}\max_\alpha L(w,b,\alpha) \Rightarrow \max_\alpha \min_{w,b} L(w,b,\alpha)\\
\min_{w,b}L(w,b,\alpha) \Rightarrow \triangledown_wL(w,b,\alpha) = w-\sum_{i=1}^N\alpha_iy_ix_i = 0 \ and \ \triangledown_bL(w,b,\alpha)=\sum_{i=1}^N\alpha_iy_i=0 \\
w = \sum_{i=1}^N \alpha_iy_ix_i \\
\sum_{i=1}^N\alpha_iy_i=0 \\
\therefore \min_{w,b}L(w,b,\alpha) = -\frac{1}{2}\sum_{i=1}^N \sum_{j=1}^N \alpha_i\alpha_jy_iy_jx_i^Tx_j + \sum_{i=1}^N \alpha_i \\
Then, \ \max_\alpha -\frac{1}{2}\sum_{i=1}^N \sum_{j=1}^N \alpha_i\alpha_jy_iy_jx_i^Tx_j + \sum_{i=1}^N \alpha_i, s.t. \sum_{i=1}^N \alpha_iy_i=0 \ and \ \alpha_i \ge 0\\
\Rightarrow \min_\alpha \frac{1}{2}\sum_{i=1}^N \sum_{j=1}^N \alpha_i\alpha_jy_iy_jx_i^Tx_j - \sum_{i=1}^N \alpha_i, s.t. \sum_{i=1}^N \alpha_iy_i=0 \ and \ \alpha_i \ge 0 \\
Computing \ \alpha^* \ by \ SMO \ algorithm \\
b^* = y_j - \sum_{i=1}^N \alpha_i^*y_ix_i^Tx_j \\
hyper-plane \Rightarrow \sum_{i=1}^N \alpha_i^*y_ix^Tx_i + b^* = 0 \\
f(x) = sign(\sum_{i=1}^N \alpha_i^*y_ix^Tx_i + b^*)
$$
{% endraw %}

Above content is soft margin SVM which is used in linear classification. However, there're usually some outliers in dataset, so the problem is about near-linear classification. We should use hard margin SVM which introduces a slack variable $\xi$ ($\xi \ge 0$), then constraint condition becomes $y_i(w^Tx_i+b) \ge 1-\xi_i$. The objective function is then modified to $\frac{1}{2}||w||^2+C \sum_{i=1}^N \xi_i$, where $C > 0$ (penalty parameter). When $C$ is large, the penalty of misclassification becomes large; when $C$ is small, the penalty becomes small. $C$ Is like $\lambda$ in regularization and controls margins of model and importance of misclassified points. Hard margin SVM is computed in same way of soft margin SVM.

However, there are still some non-linear classification problem. It's time to introduce kernel trick, which maybe one of coolest invitations in ML. 

Kernel: linear kernel, polynomial kernel, Gaussian kernel, etc

[SMO](https://mp.weixin.qq.com/s?__biz=MzU0MDQ1NjAzNg==&mid=2247484795&idx=1&sn=895b56d7ad79e4dcbf73cea448167a55&chksm=fb39a070cc4e29661f72e2ccb78081dffff2c581963026a12b961276d6d9fe8a2bd8ce61cd24&token=1838236212&lang=zh_CN&scene=21#wechat_redirect)

#### Decision Tree

https://en.wikipedia.org/wiki/Decision_tree

##### ID3

Information gain is the metric of choosing and split features.

$Ent(D)=-\sum_{k=1}^{y}p_k \log_2p_k$

$Gain(D,a)=Ent(D)-\sum_{v=1}^V \frac{|D^v|}{|D|}Ent(D^v)$ 

ID3 chooses highest $Gain(D,a)$. 

##### C4.5

$GainRatio(D,a)=\frac{Gain(D,a)}{IV(a)}$

$IV(a)=-\sum_{v=1}^V \frac{|D^v|}{|D|} \log_2 \frac{|D^v|}{|D|}$

##### CART

$Gini(D)=1-\sum_{k=1}^Np_k^2$

$Gini_a=\sum_{v=1}^V \frac{|D^v|}{|D|}Gini(D^v)$

CART chooses lowest $Gini_a$.

#### Random Forest

RF is a bagging algorithm (bootstrapping) which is based on decision tree. RF concludes some trees and each tree can give prediction. Every model in RF has similar bias and variance, 

{% raw %}
$$
E[\frac{\sum X_i}{K}] = E[X_i] \\
\frac{Var(\sum X_i)}{K} \le Var(\frac{\sum X_i}{K}) \le Var(X_i) \\
Equation \ is \ valid \ when \ trees \ are \ independent \ or \ same.
$$
{% endraw %}

We can find that RF can reduce variance, so RF is an effective way to solve overfitting problem. What's more, the relavance of trees is associated with that ability, thus reducing the relavance is important. Fortunately, input data of each tree is subsampled in whole dataset, so we can try to choose different features with low overlap. Obviously, number of features is an important hyper-parameters to RF.

Important parameters: **n_estimators**; **max_features**; max_depth; min_samples_leaf; min_samples_split; max_leaf_nodes; min_samples_leaf

#### AdaBoost

AdaBoost is belonging to Boosting algorithm which literately combines weak learners to get the final strong learner. In Boosting, data are all given weights and misclassified data can get higher weights responding to higher importance. Meanwhile, those series of learners also have weights: low weights are given to high error learners, and high weights are given to low error leaners.

It's time to talk about AdaBoost. It can be used in both classification and regression.

Assuming that is a binary classification problem with {-1, 1} as output and $k_{th}$ learner is denoted to $G_k(x)$.

{% raw %}
$$
e_k = P(G_k(x_i) \neq y_i) = \sum_{i=1}^m w_{ki}I(G_k(x_i) \neq y_i) \\
\alpha_k = \frac{1}{2} \log \frac{1-e_k}{e_k} \\ large \ \alpha \ is \ followed \ by \ small \ e \\
w_{k+1,i} = \frac{w_{k,i}}{Z_i} \exp (-\alpha_i G_k(x_i) y_i) \\
Z_k = \sum_{i=1}^m w_{k,i} \exp (-\alpha_i G_k(x_i)y_i) \\
G(x) = sign(\sum_{m=1}^K \alpha_m G_m(x))
$$
{% endraw %}

If it is misclassification, $G_k(x_i)y_i=-1$, $w_{k+1,i} > w_{k,i}$ which means that weights of misclassification get higher; otherwise,  $G_k(x_i)y_i=1$, $w_{k+1,i}<w_{k,i}$.

Can we do regression by AdaBoost? Of course!

{% raw %}
$$
E_k = \max |y_i - G_k(x_i)|, i = 1,2,\cdots,m \\
e_{k,i} = \begin{cases}
\frac{|y_i - G_k(x_i)|}{E_k} & Linear \ Error \\
\frac{(y_i - G_k(x_i))^2}{E_k^2} & MSE \\
1 - \exp (\frac{-|y_i-G_k(x_i)|}{E_k}) & Exponential \ Error
\end{cases} \\
e_k = \sum_{i=1}^m w_{k,i} e_{k,i} \\
\alpha_k = \frac{e_k}{1-e_k} \\
w_{k+1,i} = \frac{w_{k,i}}{Z_k} \alpha_k^{1-e_{k,i}} \\
Z_k = \sum_{i=1}^m w_{k,i} \alpha_k^{1-e_{k,i}} \\
f(x)=\sum_{k=1}^K(\ln \frac{1}{\alpha_k})g(x) \\
g(x) \ is \ median \ of \ all \ \alpha_kG_k(x)
$$

{% endraw %}

#### Gradient Boost Decision Tree (GBDT)

In GBDT, we are using minus gradient of loss function to fit residual.

{% raw%}
$$
r_{m,i} = - [\frac{\partial L(y_i,f(x_i)}{\partial f(x_i)}]_{f(x)=f_{m-1}(x)} \\
\Rightarrow R_{m,j} \\
T_{m,j}=argmin_T \sum_{x_i \in R_{m,j}} L(y_i,f_{m-1}(x_i)+T) \\
f_m(x) = f_{m-1}(x)+\sum_{j=1}^J c_{m,j}I(x \in R_{m,j})) \\
\hat{f(x)} = f_M(x)=f_0(x)+\sum_{m=1}^M \sum_{j=1}^J T_{m,j}I(x \in R_{m,j})
$$

{% endraw %}

#### XGBOOST

$\Omega(f_t)= \gamma T + \frac{1}{2}\lambda \sum_{j=1}^Tw_j^2$

$T$ is number of leaf nodes; $w$ is leaf node scores.

{% raw %}
$$
\begin{align}
Obj^{(t)} &= \sum_{i=1}^n L(y_i, \hat{y_i}^{(t)})+\sum_{k=1}^t \Omega(f_k) \\
&= \sum_{i=1}^n L(y_i, \hat{y_i}^{(t-1)}+f_t(x_i)) + \sum_{k=1}^{t-1} \Omega(f_k) + \Omega(f_t) \\
&= \ \sum_{i=1}^n L(y_i, \hat{y_i}^{(t-1)}+f_t(x_i)) + \Omega(f_t) + Constant \\
\because & f(x_0+\triangle x) \approx f(x_0)+f'(x_0) \cdot \triangle x + \frac{f''(x_0)}{2} \cdot (\triangle x)^2 \\
\therefore &Obj^{(t)} \approx \sum_{i=1}^n [L(y_i, \hat{y_i}^{(t-1)})+g_if_t(x_i)+\frac{1}{2}h_if_t(x_i)^2] + \Omega(f_t) + Constant \\
\because &L(y_i, \hat{y_i}^{(t-1)}) \ is \ constant \\
\therefore & Obj^{(t)} = \sum_{i=1}^n [g_if_t(x_i)+\frac{1}{2}h_if_t(x_i)^2] + \Omega(f_t) + Constant \\
&g_i =\partial_{\hat{y}^{(t-1)}}L(y_i, \hat{y}^{(t-1)});h_i=\partial_{\hat{y}^{(t-1)}}^2L(y_i, \hat{y}^{(t-1)}) \\
\because &\Omega(f_t)= \gamma T + \frac{1}{2}\lambda \sum_{j=1}^Tw_j^2 \\
\therefore Obj^{(t)} &= \sum_{i=1}^n[g_i \cdot w_{q(x_i)}+\frac{1}{2}h_i \cdot w_{q(x_i)}^2]+\gamma T + \frac{1}{2}\lambda \sum_{j=1}^Tw_j^2 \\
&= \sum_{j=1}^T[(\sum_{i \in I_j}g_i)w_j+\frac{1}{2}(\sum_{i \in I_j}h_i+\lambda)w_j^2]+\gamma T \\
&= \sum_{j=1}^T[G_jw_j+\frac{1}{2}(H_j+\lambda)w_j^2]+\gamma T \\
\Rightarrow &w_j^*=-\frac{G_j}{H_j+\lambda} \\
\Rightarrow &Obj^*=-\frac{1}{2}\sum_{j=1}^T \frac{G_j^2}{H_j+\lambda}+\gamma T \ (scoring-function)
\end{align}
$$
{% endraw %}

Scoring function measures tree structures, so the low the value, the better structure the better. Choosing best split point by scoring function to build CART.
$$
Gain = \frac{1}{2}[\frac{G_L^2}{H_L+\lambda}+\frac{G_R^2}{H_R+\lambda}-\frac{(G_L+G_R)^2}{H_L+H_R+\lambda}]-\gamma
$$
$Gain$ represents difference between single node $obj^*$ and two node $obj^*$. Traversing all features' splitting points to find splitting point with max $Gain$. $G_L, G_R$ Is sum of first order gradient of left nodes and right nodes. $H_L, H_R$ is sum of second order gradient of left nodes and right nodes. $\lambda$ is regularization coefficient. $\gamma$ is difficulty of splitting node. $\lambda$ and $\gamma$ define complexity of trees.

If $\gamma$ is too large, $Gain$ is negative, which represents not splitting; The higher the $\gamma$, the more strict the obj's dropping.

Difference with GBDT:

-  XGBoost would consider complexity of CART in building process, but GBDT wouldn't.
-  XGBoost fits last time loss function's second order gradient, but GBDT just considers first order gradient.
-  XGBoost can compute parallelly (not in building tree, but in feature processing)
   - xgboost的并行不是tree粒度的并行，xgboost也是一次迭代完才能进行下一次迭代的（第t次迭代的代价函数里包含了前面t-1次迭代的预测值）。xgboost的并行是在特征粒度上的。我们知道，决策树的学习最耗时的一个步骤就是对特征的值进行排序（因为要确定最佳分割点），xgboost在训练之前，预先对数据进行了排序，然后保存为block结构，后面的迭代中重复地使用这个结构，大大减小计算量。这个block结构也使得并行成为了可能，在进行节点的分裂时，需要计算每个特征的增益，最终选增益最大的那个特征去做分裂，那么各个特征的增益计算就可以开多线程进行。
-  XGBoost can deal with missing data
-  XGBoost can customize loss function if only it has second order gradient
-  XGBoost have column subsampling
   - xgboost借鉴了随机森林的做法，支持列抽样，不仅能降低过拟合，还能减少计算.

Details of splitting algorithm can be seen in this [post](https://mp.weixin.qq.com/s?__biz=MzU0MDQ1NjAzNg==&mid=2247485414&idx=1&sn=faa60d6b4653b9a73e6c5b1ae5f9fd22&chksm=fb39a2edcc4e2bfbe4d4b64c1d7c78b08ec6a4a07e03dd21c634add55bff1ced9693b75ecf88&token=309753865&lang=zh_CN&scene=21#wechat_redirect).

Details of tuning parameters can be seen in this [post](https://sunjunee.github.io/2018/01/05/xgboost-tuning/).

#### Light GBM

 https://zhuanlan.zhihu.com/p/35155992 

提升树是利用加模型与前向分布算法实现学习的优化过程，它有一些高效实现，如XGBoost, pGBRT，GBDT等。其中GBDT采用负梯度作为划分的指标（信息增益），XGBoost则利用到二阶导数。他们共同的不足是，计算信息增益需要扫描所有样本，从而找到最优划分点。在面对大量数据或者特征维度很高时，他们的效率和扩展性很难使人满意。微软开源的LightGBM（基于GBDT的）则很好的解决这些问题，它主要包含两个算法：

- GOSS（从减少样本角度）：排除大部分小梯度的样本，仅用剩下的样本计算信息增益。
- EFB（从减少特征角度）：捆绑互斥特征，也就是他们很少同时取非零值（也就是用一个合成特征代替）。

GBDT是基于决策树的集成算法，采用前向分布算法，在每次迭代中，都是通过负梯度拟合残差，从而学习一颗决策树，最耗时的步骤就是找最优划分点。一种流行的方法就是预排序，核心是在已经排好序的特征值上枚举所有可能的特征点。另一种改进则是直方图算法，他把连续特征值划分到k个桶中取，划分点则在这k个点中选取。k<<d，所以在内存消耗和训练速度都更佳，且在实际的数据集上表明，离散化的分裂点对最终的精度影响并不大，甚至会好一些。原因在于决策树本身就是一个弱学习器，采用Histogram算法会起到正则化的效果，有效地防止模型的过拟合。LightGBM也是基于直方图的。

为了减少训练数据，最直接的方法就是欠采样(down sample)，比如说过滤掉权重低于阈值的样本。SGD(随机梯度下降)采用的是在每轮迭代中选取随机子集进行训练弱分类器，AdaBoost则采用的是动态调整采样率。SGD可以应用到GBDT，但会影响精度，其他的则不能直接引入，因为GBDT中没有这种内在的权重。

为了减少特征，通常做的是PCA降维，但是这些方法都假设特征是冗余的，这并不一直正确。

一般大型数据集都是稀疏的，基于pre-sorted的GBDT可以通过忽略零值特征，从而减少训练代价。但是，基于histogram的则没有针对稀疏特性的优化方案，它只是计算累加值，不管你是0还是非0。所以，利用稀疏性的GBDT是很必要的。

**GOSS**

在AdaBoost中采用权重很好诠释了样本的重要性，GBDT没有这种权重，但是我们注意到每个数据样本的梯度可以被用来做采样的信息。也就是，如果一个样本的梯度小，那么表明这个样本已经训练好了，它的训练误差很小了，我们可以丢弃这些数据。当然，改变数据分布会造成模型的精度损失。GOSS则通过保存大梯度样本，随机选取小梯度样本，并为其弥补上一个常数权重。这样，GOSS更关注训练不足的样本，同时也不会改变原始数据太多。 

 ![GOSS](https://pic3.zhimg.com/80/v2-1608d8b79f3d605e111878b715254d3e_hd.jpg)

先根据梯度对样本进行排序，选取 a * 100% 的top样本，再从剩余数据中随机选取 b * 100% 的样本，并乘以 的系数放大。

以前计算特征j在d值点的信息增益是这样的：

 ![](https://pic2.zhimg.com/80/v2-4b9a8ede817e0e68d889707b452fd921_hd.jpg)

 现在是这样的：

 ![](https://pic2.zhimg.com/80/v2-ea968fb24797e33e1ed60d9178814be5_hd.jpg) 

通过证明，近似误差很好，很贴近使用所有数据的模型。这也解释了LightGBM的 leaf-wise 生成策略。

**Leaf-wise (Best-first)的决策树生长策略**

大部分决策树的学习算法通过 level(depth)-wise 策略生长树，如下图一样：

 ![Level-wise](https://pic2.zhimg.com/80/v2-255136baf2eaeec7841db3a2e45ca5a1_hd.jpg)

LightGBM 通过 leaf-wise (best-first)策略来生长树。它将选取具有最大 delta loss 的叶节点来生长。 当生长相同的 #leaf，leaf-wise 算法可以比 level-wise 算法减少更多的损失。当 #data 较小的时候，leaf-wise 可能会造成过拟合。 所以，LightGBM 可以利用额外的参数 max_depth 来限制树的深度并避免过拟合（树的生长仍然通过 leaf-wise 策略）。

 ![Leaf-wise](https://pic3.zhimg.com/80/v2-3f5b87c2c32efdfd33184be1e182b492_hd.jpg) 

**EFB**

高维数据一般是稀疏的，可以设计一种损失最小的特征减少方法。并且，在稀疏特征空间中，许多特征都是互斥的，也就是它们几乎不同时取非0值。因此，我们可以安全的把这些互斥特征绑到一起形成一个特征，然后基于这些特征束构建直方图，这样又可以加速了。

有两个问题待解决，如何判断哪些特征该绑到一起，如何构建绑定。这是NP难的。

首先，转换到图着色问题。G=(V, E)，把关联矩阵G的每一行看成特征，从而得到|V|个特征，互斥束就图中颜色相同的顶点。图中点就是特征，边代表两个特征不互斥，也就是特征之间的冲突。如果算法允许小的冲突，可以得到更小的特征束数量，计算效率会更高。证明发现随机污染一小部分特征值，最多影响训练精度 ，是所有束中冲突最大的。通过选取合适的，我们可以很好的在效率和精度之间寻找平衡。

最后，排序就按照束的度来进行。当然，更一步优化是不够造图，直接**根据非零值的数量排序**，这个根据度排序很像，因为更多非0值意味着更高概率的冲突。更改了排序策略，可以避免重复。

第二个问题，合并特征，从而降低训练复杂度，关键是我们可以确保原先特征值可以从特征束中识别出来。因为直方图存储的是特征的离散桶，而不是连续值，我们可以通过把互斥特征放到不同桶，从而构造一个特征束。这可以通过添加偏移实现。如，假设我们有2个特征在一个特征束中，原先特征A的范围为[0,10)，特征B的范围为[0,20)，我们给特征B加上一个偏移10，它就变成[10,30)，这样我们就可以执行安全的合并了，用特征束[0,30)代替特征A和B。具体算法如下。

 ![EFB algorithm](https://pic1.zhimg.com/80/v2-4d7875a088e184c694474be2bec26698_hd.jpg) 

EFB算法可以把很多特征绑到一起，形成更少的稠密特征束，这样可以避免对0特征值的无用的计算。加速计算直方图还可以用一个表记录数据的非0值。 

--------------------

 https://www.zhihu.com/question/51644470 

GBDT 虽然是个强力的模型，但却有着一个致命的缺陷，不能用类似 mini batch 的方式来训练，需要对数据进行无数次的遍历。如果想要速度，就需要把数据都预加载在内存中，但这样数据就会受限于内存的大小；如果想要训练更多的数据，就要使用外存版本的决策树算法。虽然外存算法也有较多优化，SSD 也在普及，但在频繁的 IO 下，速度还是比较慢的。

为了能让 GBDT 高效地用上更多的数据，我们把思路转向了分布式 GBDT， 然后就有了 LightGBM。设计的思路主要是两点，1. 单个机器在不牺牲速度的情况下，尽可能多地用上更多的数据；2.多机并行的时候，通信的代价尽可能地低，并且在计算上可以做到线性加速。

基于这两个需求，LightGBM 选择了基于 histogram 的决策树算法。相比于另一个主流的算法 pre-sorted（如 xgboost 中的 exact 算法），histogram 在内存消耗和计算代价上都有不少优势。

- Pre-sorted 算法需要的内存约是训练数据的两倍(2 * #data * #features
  \* 4Bytes)，它需要用32位浮点来保存 feature value，并且对每一列特征，都需要一个额外的排好序的索引，这也需要32位的存储空间。对于 histogram 算法，则只需要(#data\* #features * 1Bytes)的内存消耗，仅为 pre-sorted算法的1/8。因为 histogram 算法仅需要存储 feature
  bin value (离散化后的数值)，不需要原始的 feature value，也不用排序，而 bin value 用 uint8_t (256 bins) 的类型一般也就足够了。
- 在计算上的优势则主要体现在“数据分割”。决策树算法有两个主要操作组成，一个是“寻找分割点”，另一个是“数据分割”。从算法时间复杂度来看，Histogram 算法和 pre-sorted 算法在“寻找分割点”的代价是一样的，都是O(#feature\*#data)。而在“数据分割”时，pre-sorted 算法需要O(#feature\*#data)，而 histogram 算法是O(#data)。因为 pre-sorted 算法的每一列特征的顺序都不一样，分割的时候需要对每个特征单独进行一次分割。Histogram算法不需要排序，所有特征共享同一个索引表，分割的时候仅需对这个索引表操作一次就可以。（更新: **这一点不完全正确，pre-sorted 与 level-wise 结合的时候，其实可以共用一个索引表(row_idx_to_tree_node_idx)。然后在寻找分割点的时候，同时操作同一层的节点，省去分割的步骤。但这样做的问题是会有非常多随机访问，有很大的cache miss，速度依然很慢。**）
- 另一个计算上的优势则是大幅减少了计算分割点增益的次数。对于一个特征，pre-sorted 需要对每一个不同特征值都计算一次分割增益，而 histogram 只需要计算 #bin (histogram 的横轴的数量) 次。
- 最后，在数据并行的时候，用 histgoram 可以大幅降低通信代价。用 pre-sorted 算法的话，通信代价是非常大的（几乎是没办法用的）。所以 xgoobst 在并行的时候也使用 histogram 进行通信。

当然， histogram 算法也有缺点，它不能找到很精确的分割点，训练误差没有 pre-sorted 好。但从实验结果来看， histogram 算法在测试集的误差和 pre-sorted 算法差异并不是很大，甚至有时候效果更好。实际上可能决策树对于分割点的精确程度并不太敏感，而且较“粗”的分割点也自带正则化的效果。

在 histogram 算法之上， LightGBM 进行进一步的优化。首先它抛弃了大多数 GBDT 工具使用的按层生长
(level-wise) 的决策树生长策略，而使用了带有深度限制的按叶子生长 (leaf-wise) 算法。 level-wise 过一次数据可以同时分裂同一层的叶子，容易进行多线程优化，不容易过拟合。但实际上level-wise是一种低效的算法，因为它不加区分的对待同一层的叶子，带来了很多没必要的开销。因为实际上很多叶子的分裂增益较低，没必要进行搜索和分裂。leaf-wise则是一种更为高效的策略，每次从当前所有叶子中，找到分裂增益最大(一般也是数据量最大)的一个叶子，然后分裂，如此循环。因此同 level-wise 相比，在分裂次数相同的情况下，leaf-wise 可以降低更多的误差，得到更好的精度。leaf-wise 的缺点是可能会长出比较深的决策树，产生过拟合。因此 LightGBM 在leaf-wise 之上增加了一个最大深度的限制，在保证高效率的同时防止过拟合。

另一个比较巧妙的优化是 histogram 做差加速。一个容易观察到的现象：一个叶子的直方图可以由它的父亲节点的直方图与它兄弟的直方图做差得到。通常构造直方图，需要遍历该叶子上的所有数据，但直方图做差仅需遍历直方图的 k 个桶。利用这个方法，LightGBM 可以在构造一个叶子的直方图后，可以用非常微小的代价得到它兄弟叶子的直方图，在速度上可以提升一倍。

-----------------------

非常赞的工作，实现了和XGBoost不一样的搜索策略，所以在算法效果上并不是完全一样。

\- XGBoost在单机默认是exact greedy，搜索所有的可能分割点。分布式是dynamic histogram，每一轮迭代重新estimate 潜在split candidate。
\- LightGBM和最近的FastBDT都采取了提前histogram binning再在bin好的数据上面进行搜索。在限定好candidate splits,
\- 主要的速度提升似乎来自于两点。 一个是搜索的时候选取delta比较大的叶子扩展。第二个是pre-bin之后的histogram的求和用了一个非常巧妙的减法trick，省了一半的时间。

在算法和效果上面

最近比较多的工作都开始基于提前限定分割点的近似算法然后快速求histogram。 这一类算法的潜在问题是限制了分割点只能是一开始的定下来的潜在这些。不知道这一点对于实际应用的影响会有多大。理论上数据越多，树越深的时候，需要的潜在分割点越多，可能需要根据训练来动态更新潜在的分割点。

在算法上面采用delta比较大的扩展方向可以集中搜索提高比较重要的区域。一个潜在的问题是可能会忽略掉一些未来有潜力的节点。

当然这些讨论都和具体的应用场景有关。个人觉得exact greedy和histogram方法的还会共存一段时间。或许可以比较好的在系统上面对这两个一起支持

在系统上面

因为集中做针对叶子的分割，似乎会对于数据集的random access有一定的要求。如果不shuffle data的情况下可能会有cache 的问题。这个数据结构是一个有趣的问题

非常值得学习，机器学习系统优化是非常重要的方向。希望可以看到越来越多这样实际的工作

#### Factorization Machine and Field Factorization Machine

##### FM

旨在解决稀疏数据下的特征组合问题，具有线性的计算复杂度；（矩阵分解方式处理参数，不仅能减少参数数量，还能处理由于稀疏性带来的参数不好训练的问题）一般的线性模型压根没有考虑特征间的关联(组合).

![FM](Study-Notes-of-Machine-Learning-V1/image-20191116022611357.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116022630055.png)

##### FFM

通过引入field的概念，FFM把相同性质的特征归于同一个field。在FFM中，每一维特征 xi，针对其它特征的每一种field fj，都会学习一个隐向量 vi,fj。因此，隐向量不仅与特征相关，也与field相关。如果隐向量的长度为 k，那么FFM的二次参数有 nfk 个，远多于FM模型的 nk个。此外，由于隐向量与field相关，FFM二次项并不能够化简，其预测复杂度是 $O(kn^2)$。

 ![FFM](Study-Notes-of-Machine-Learning-V1/20170824140534619) 

![](Study-Notes-of-Machine-Learning-V1/image-20191116023256527.png)

![](Study-Notes-of-Machine-Learning-V1/image-20191116023321114.png)

![FFM using SGD](Study-Notes-of-Machine-Learning-V1/image-20191116023414278.png)

FFM相对于FM加入了field的概念，每个特征的隐向量不再是只有一个，而是针对每个field学习一个独立的隐向量，防止互相影响。因此理论上来说，在理解业务的基础上，设计合理field之后的FFM模型应该是不会比FM差的。关键在于计算复杂度，FM通过合适的推导，训练/预测复杂度是线性的。而FFM的复杂度是二次方的。在工业界的业务中，如果FFM相对于FM提升不是很明显，一般还是选用FM模型，毕竟计算复杂度差的太多了。

#### K-Means and EM

Choosing $k$ data points as cluster centers, then combining each data into those $k$ clusters by closest distance as metric. Finally, iteratively updating cluster centers by mean of all data of each cluster until no change of those centers.

[EM]([https://en.wikipedia.org/wiki/Expectation%E2%80%93maximization_algorithm](https://en.wikipedia.org/wiki/Expectation–maximization_algorithm)) is a powerful algorithm. 

Details can be seen in these [post](http://cs229.stanford.edu/notes/cs229-notes8.pdf), [post](http://www.columbia.edu/~mh2078/MachineLearningORFE/EM_Algorithm.pdf) and [post](http://bjlkeng.github.io/posts/the-expectation-maximization-algorithm/).

#### PCA and LDA

http://www.svcl.ucsd.edu/courses/ece271B-F09/handouts/Dimensionality2.pdf

#### t-SNE

#### Spectral Clustering

https://github.com/zhangleiszu/machineLearning/blob/master/A%20Tutorial%20on%20Spectral%20Clustering.pdf

### CNN

http://cs231n.stanford.edu/

#### Characteristic

- Local feature 
- Sparse connectivity
- Parameter sharing
- Shift invariant 

#### Different Structures

- Standard Convolution
- Transposed Convolution (Deconvolution)
- Dilated Convolution (Atrous Convolution)
- Separable Convolution
- Gated Convolution

#### Problems

##### Pooling

Advantages:

- increasing respective fields
- shift invariant
- reducing number of parameters and difficulty of optimization

Pooling provides a sort of shift invariant, however it destroys information of position and space, etc. [Quantifying Translation-Invariance in Convolutional Neural Networks]( https://arxiv.org/pdf/1801.01450.pdf ) shows that CNN itself has no shift invariant which is learnt through data. 

Another research from DeepMind, [Pooling is neither necessary nor sufficient for appropriate deformation stability in CNNs](https://arxiv.org/pdf/1804.04438.pdf), shows that models can still get great performance without pooling. What's more, they think that smoother of  parameters of CNN kernels can make shift more stable.

##### Stochastic Deep

### RNN

#### Basic Structure

![RNN](Study-Notes-of-Machine-Learning-V1/image-20191115220219342.png)
$$
\Rightarrow a^{(t)} = W \cdot [h^{(t-1)};x^{(t)}]+b \\
\Rightarrow h^{(t)} = f(a^{(t)}) \\
\Rightarrow h^{(t)}=f(h^{(t-1)},x^{(t)};\theta) \\
$$
$Tanh$ is always the activation function.

[Why do we use tanh as activation function but ReLU?](https://www.zhihu.com/question/61265076)

#### LSTM

https://colah.github.io/posts/2015-08-Understanding-LSTMs/

![LSTM](Study-Notes-of-Machine-Learning-V1/image-20191115220304740.png)

![forget gate](Study-Notes-of-Machine-Learning-V1/image-20191118002139210.png)

![input gate](Study-Notes-of-Machine-Learning-V1/image-20191118002146556.png)

![cell status](Study-Notes-of-Machine-Learning-V1/image-20191118002155566.png)

![output gate and hidden status](Study-Notes-of-Machine-Learning-V1/image-20191118002203027.png)

#### GRU

https://medium.com/mlrecipies/deep-learning-basics-gated-recurrent-unit-gru-1d8e9fae7280

It combines the forget and input gates into a single “update gate.” It also merges the cell state and hidden state, and makes some other changes. 

![GRU](Study-Notes-of-Machine-Learning-V1/image-20191115220329260.png)

#### Comparison

https://towardsdatascience.com/animated-rnn-lstm-and-gru-ef124d06cf45

