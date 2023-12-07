# GtsamAndFactorGraph

之前学习GTSAM的时候忘记记录了，现在来补档。

GTSAM(Georgia Tech Smoothing and Mapping)是一个由佐治亚理工学院发布的用于解决SLAM和SFM问题的C++库，使用因子图的方式最大化后验概率，协方差矩阵和均值具有物理意义。关于GTSAM的使用方式后面说，先来看一下贝叶斯网络如何转换为因子图对SLAM问题进行建模的。下面还是有很多用语不是很专业，比如概率密度和概率分布，等说法其实是在表达同一个意思那就是概率密度函数或者是概率分布函数。



## 贝叶斯网络

基于贝叶斯理论，在图的基础上表示概率分布的模型称之为概率图模型，用点来表示随机变量，节点之间的边表示节点间的概率依赖关系。贝叶斯网络就是最基础的概率图模型，贝叶斯网络(Bayesian Network)是一种有向无环图(Direct Acyclic Model)，DAG，用于表示概率的传递。



## 马尔可夫链与隐马尔可夫模型

这里理解起来比较抽象，可以通过理解一些常见的随机过程来进行记忆。

**马尔可夫性质（Markov Property）：**

​	**随机过程中，未来状态的条件概率分布仅依赖当前状态，而与过去状态无关。**

**马尔科夫链（Markov Chain）：**

​	**状态对观测者可见，状态转移概率便是全部参数。**典型例子如随机游走，用尺子测量长度

**隐马尔科夫模型（Hidden Markov Model）：**

​	**状态不直接可见，仅某些量可见 。**典型例子如通过地面蚂蚁活动确定阴晴

注意，这里将随机过程建模为马尔科夫链和隐马尔科夫模型关键不在于状态方程的线性非线性，而是依据状态方程建模时存不存在未知的参数，这里通过蚂蚁活动预测阴晴是一个无法确定全部参数的系统，比如蚂蚁的活动种类不可尽数等等建模原因。

## 贝叶斯网络几种基本元素

**贝叶斯网络**为**链式网络**，也是**马尔可夫链**，是一个**有向无环图**。

网络形式由三种最基础的形状构成：

**head-to-head： A—>C<—B**

​		$p(A,B,C)=p(A)p(B)p(C{\mid}A,B)$

**tail-to-tail： A<—C—>B**

​		**C未知：**$p(A,B,C)=p(C)p(A{\mid}C)p(B{\mid}C)$

​		**C已知：**$p(A,B{\mid}C)=\frac{p(A,B,C)}{p(C)}$

**head-to-tail： A—>B—>C**

​		**C未知：**$p(A,B,C)=p(A)p(B{\mid}A)p(C{\mid}B)$，无法推出$p(A,B)=p(A)p(B)$，无法推出$AB$独立

​		**C已知：**可以推出$AB$独立

## 贝叶斯公式

存在同一时刻的两个事件为$A$，$B$。

$A$事件发生的概率为$p(A)$，称作**先验概率**

$B$事件发生的概率为$p(B)$

根据离散事件的全概率公式为：

$$
\begin{matrix}
p(A,B)&=&p(A)&p(B{\mid}A)&=&p(B)&p(A{\mid}B) \\
\mathbf{联合}&&\mathbf{先验}&\mathbf{似然}&&\mathbf{边缘}&\mathbf{后验}
\end{matrix}
$$

后验概率计算：

$$
\begin{matrix}
p(A{\mid}B)=\frac{p(A)p({B\mid}A)}{p(B)}\\
\mathbf{后验}=\frac{\mathbf{先验}{\times}\mathbf{似然}}{\mathbf{边缘}}
\end{matrix}
$$

$$
\begin{aligned}
p(A{\mid}B)&=\frac{p(A)p(B{\mid}A)}{p(B)} \\
&={\eta}p(A)p(B{\mid}A)
\end{aligned}
$$

因此最大后验概率$MAP$**（Maximum A Posteriori Estimate）**也可以等价于$MLE$**（Maximum Likelihood Estimate）**

在推到后面的问题的时候需要知道三个事件的后验概率，因此这里推导一下，存在同一时刻可能发生的事件$ABC$，其中后验概率密度的关系有：

$$
\begin{aligned}
p(A{\mid}B,C)&=\frac{p(B,C{\mid}A)p(A)}{p(B,C)} \\
&=\frac{\frac{p(A,B,C)}{p(A)}p(A)}{p(B{\mid}C)p(C)} \\
&=\frac{\frac{p(A,B,C)}{p(A,C)}\frac{p(A,C)}{p(A)}p(A)}{p(B{\mid}C)p(C)} \\
&=\frac{p(B{\mid}A,C)p(C{\mid}A)p(A)}{p(B{\mid}C)p(C)} \\
&=\frac{p(B{\mid}A,C)p(A{\mid}C)p(C)}{p(B{\mid}C)p(C)} \\
&=\frac{p(B{\mid}A,C)p(A{\mid}C)}{p(B{\mid}C)} \\
&= {\eta}p(B{\mid}A,C)p(A{\mid}C)
\end{aligned}
$$

后验概率并不好算，因此上面这个公式可有效解决问题。

后面需要将连续时间的全概率公式，因此这里推导一下：

传统意义上的全概率公式为：

$$
p(B)=\sum_{i=1}^{n}p(B{\mid}A_i)P(A_i)
$$

积分形式为：

$$
p(Y)=\int_{-\infty}^{+\infty}{p(Y{\mid}x)p(x)dx}
$$

**贝叶斯网络**由一个**有向无环图（DAG）**和一个**条件概率表（CPT）**组成。并具有**马尔可夫性**。

注意这里推导的是同一时刻的可能事件，如果要应用在不同时刻下的状态和观测输入中，需要假设系统具有马尔科夫性并稍加转换。



## 用贝叶斯角度描述SLAM问题

在**SLAM问题**中：

给定**初始时刻**到$t$时刻的所有传感器信息，即$u$和$z$。

现要求条件概率$p(x_k{\mid}z_{1:k},u_{1:k})$，为了计算方便使用迭代的方法计算每一时刻的概率，后面的优化方法需要优化多个时刻的状态$x_k$所以形式上会有所区别。

**状态：**$x_i(i=1,2,...,k)$，实际上是**6DoF位姿**，为$6{\times}1$的向量。

**输入：**${u}_i(i=1,2,...,k)$，是输入数据，在SLAM问题中是运动测量单元如IMU和里程计的粗略结果。

**观测：**$z_i(i=1,2,...,k)$，是来自外部的观测数据，可能是激光点云，相机图像，GPS等。

这里的输入来源于测量运动的传感器比如IMU，轮式里程计等。在使用优化方法SLAM问题、SFM可问题以及类似的问题中基本不会出现这个量，因此系统方程可以更加简化一点，在部分方法中可以将由传感器信息估计出的粗略位子看做输入$u$但是一般在推导中忽略输入$u$而将粗略的位姿作为算法中的初值对待。这个如何划分见仁见智。

这里推导较为全面的公式以支持后面的滤波器方法。

**利用观测与输入估计当前的状态**，称为**状态估计问题**，利用贝叶斯的思想转换为求**最大后验概率问题**。

**最大化后验概率（MAp，Maximizes A Posterior）**，给定所有历史观测下的当前状态的概率密度可以看走是一个后验概率：

$$
X^*_{MAp}=argmaxp(x_{k}{\mid}z_{1:k},u_{1:k})
$$

为了摆脱使用所有时刻的数据，需要展开，利用三事件的贝叶斯条件概率公式：

$$
\begin{aligned}
p(x_k{\mid}z_{1:k},u_{1:k})&=p(x_k{\mid}z_k,z_{1:k-1},u_{1:k})
\end{aligned}
$$

上式较原式加入了上一时刻观测$z_{k1-}$是依据马尔科夫性质建立两个相邻时刻的关系。

将$z_{1:k-1},u_{1:k}$看做整体，使用贝叶斯公式得

$$
\begin{aligned}
p(x_k{\mid}z_{1:k},u_{1:k})
&=p(x_k{\mid}z_k,z_{1:k-1},u_{1:k}) \\
&=\frac{p(z_k{\mid}x_k,z_{1:k-1},u_{1:k})p(x_k{\mid}z_{1:k-1},u_{1:k})}
{p(z_k{\mid}z_{1:k-1},u_{1:k})} \\
&={\eta}p(z_k{\mid}x_k,z_{1:k-1},u_{1:k})p(x_k{\mid}z_{1:k-1},u_{1:k})
\end{aligned}
$$

这个式子建立了$z_{k}$与$z_{1:k-1}$的联系，使用马尔科夫性质知道各个观测之间是独立的，当前观测$z_k$仅和当前状态$x_k$有关，而与输入$u_{1:k}$和之前的状态$z_{a:k-1}$无关，因此:

$$
p(z_k{\mid}x_k,z_{1:k-1},u_{1:k})=p(z_k{\mid}x_k)
$$

此时，所要求的概率分布(概率密度)就变成了

$$
p(x_k{\mid}z_{1:k},u_{1:k})={\eta}p(z_k{\mid}x_k)p(x_k{\mid}z_{1:k-1},u_{1:k})
$$

**从要求，在观测$z$和输入$u$的前提下，求当前状态$x$概率密度。**

**变成了，在这个状态$x$下，求观测$z$的概率密度。**

从这里出发，SLAM中共有两种方法，**滤波方法**和**非线性优化**。



## 滤波方法

使用**贝叶斯滤波**可以进行求解，噪声项满足高斯分布的贝叶斯滤波就是**卡尔曼滤波**

在滤波器中一般情况下只关心**当前状态**：$p(x_k{\mid}z_{1:k},u_{1:k})$.

滤波器方法使用$p(x_{k-1}{\mid}z_{1:k-1},u_{1:k-1})$来计算$p(x_k{\mid}z_{1:k},u_{1:k})$

之前计算到

$$
p(x_k|z_{1:k-1},u_{1:k})={\eta}p(z_k{\mid}x_k,z_{1:k-1},u_{1:k})p(x_k{\mid}z_{1:k-1},u_{1:k}) \\
$$

将等式右边的右半侧使用全概率公式展开，就是需要求得预测的步骤：

$$
\begin{aligned}
p(x_k|z_{1:k-1},u_{1:k})
&={\eta}p(z_k{\mid}x_k,z_{1:k-1},u_{1:k})p(x_k{\mid}z_{1:k-1},u_{1:k}) \\
&=\int{p(x_k{\mid}x_{k-1},z_{1:k-1},u_{1:k}) \\
p(x_{k-1}{\mid}z_{1:k-1},u_{1:k})dx_{t-1}} \\
&=\int{p(x_k{\mid}x_{k-1},u_k)p(x_{k-1}{\mid}z_{1:k-1},u_{1:k})dx_{k-1}} \\
&=\int{p(x_k{\mid}x_{k-1},u_k)p(x_{k-1}{\mid}z_{1:k-1},u_{1:k-1})dx_{k-1}} \\
\end{aligned}
$$

根据马尔科夫性质，第三行化简掉了对$x_k$没有影响的$z_{1:k-1}$与$u_{1:k-1}$，只剩下了对当前状态有影响的当前时刻的输入$x_k$。

第四行中，化简掉了不可能对上一时刻状态$x_{k-1}$产生影响的$u_k$。

此时要求的后验概率就变成了：

$$
p(x_k{\mid}z_{1:k},u_{1:k})
=
{\eta}p(z_k{\mid}x_k)
\int{p(x_k{\mid}x_{k-1},u_k)
p(x_{k-1}{\mid}z_{1:k-1},u_{1:k-1})
dx_{k-1}}
$$

设$p(x_k{\mid}z_{1:k},u_{1:k})$为$bel(x_k)$，设$p(x_k{\mid}z_{1:k-1},u_{1:k})$为$\bar{bel}(x_k)$。

则：

$$
\begin{aligned}
\bar{bel}(x_k)
&=\int{p(x_k{\mid}x_{k-1},u_k)
p(x_{k-1}{\mid}z_{1:k-1},u_{1:k-1})dx_{k-1}} \\
&=\int{p(x_k{\mid}x_{k-1},u_k)bel(x_{k-1})dx_{k-1}}
\end{aligned}
$$

最终要求的是$bel(x_k)$

$$
bel(x_k)={\eta}p(z_k{\mid}x_k)\bar{bel}(x_k)
$$

自此将贝叶斯公式分成了两部方便后续的滤波器操作。

卡尔曼滤波中，设系统状态方程和观测方程满足线性关系：

$$
\begin{cases}
x_k=A_kx_{k-1}+B_ku_k+\epsilon_k \\
z_k=C_kx_k+\delta_k
\end{cases}\tag{1}
$$

其中${\epsilon}_k{\sim}N(0,R_k)$，代表状态方程中的误差。

其中${\delta}_k{\sim}N(0,Q_k)$，代表观测方程中的误差。

因此从上一时刻状态$x_{k-1}$计算当前时刻状态$x_k$，需要对积分项的第一项$p(x_k{\mid}x_{k-1},u_k)$和$p(z_k{\mid}x_k)$进行计算。

这两者分别对应状态方程和观测方程：

那么全概率积分中的第一项可以表示为，对应状态方程：

$$
p(x_k{\mid}x_{k-1},u_k)=
\frac{1}{
\sqrt{|2{\pi}R_k}|}
e^{-\frac{1}{2}(x_k-A_kx_{k-1}-B_ku_k)^TR_k^{-1}(x_k-A_kx_{k-1}-B_ku_k)
}
$$

那么$p(z_k{\mid}x_k)$就可以表示为，对应观测方程：

$$
p(z_k{\mid}x_k)=
\frac{1}{\sqrt{|2{\pi}Q_k}|}e^{
-\frac{1}{2}(z_k-C_kx_k)^TQ_k^{-1}(z_k-C_kx_k)
}
$$

对于条件概率来讲，已知的条件已经不是随机变量，因此第一个式子中的$x_{k-1},u_k$第二个式子中的$x_k$都是已经发生了的事件，应该按照常数来对待。只有误差项${\epsilon}_k$和${\delta}_k$是服从高斯分布的随机变量，因此，状态方程和观测方程得到的结果依然是高斯分布的。

顺便，我们假设上一时刻状态$p(x_{k-1})$具有$\Sigma_{k-1}$的方差，与$\mu_{k-1}$的均值：

$$
bel(x_k)=
\frac{1}{
\sqrt{|2{\pi}\Sigma_k}|}
e^{-\frac{1}{2}(x_k-\mu_k)^T\Sigma_k^{-1}(x_k-\mu_k)
}
$$

$$
bel(x_{k-1})=
\frac{1}{
\sqrt{|2{\pi}\Sigma_{k-1}}|}
e^{-\frac{1}{2}(x_{k-1}-\mu_{k-1})^T\Sigma_{k-1}^{-1}(x_{k-1}-\mu_{k-1})
}
$$

根据状态方程，其中$p(x_k{\mid}x_{k-1},u_k)$的均值为$A_kx_{k-1}+B_ku_k$，方差为$R_k$。

根据观测方程，其中$p(z_k{\mid}x_k)$的均值为$C_kx_k$，方差为$Q_k$。

根据前面的公式，做全概率公式的积分得预测：

$$
\begin{aligned}
&\bar{bel}(x_k) \\
&=\int{p(x_k{\mid}x_{k-1},u_k)bel(x_{k-1})dx_{k-1}} \\
&=\int{
\frac{1}{
\sqrt{|2{\pi}R_k}|}
e^{-\frac{1}{2}(x_k-A_kx_{k-1}-B_ku_k)^TR_k^{-1}(x_k-A_kx_{k-1}-B_ku_k)
}
\frac{1}{
\sqrt{|2{\pi}\Sigma_{k-1}}|}
e^{-\frac{1}{2}(x_{k-1}-\mu_{k-1})^T\Sigma_{k-1}^{-1}(x_{k-1}-\mu_{k-1})
}dx_{k-1}
} \\
&=\eta\int{
e^{-\frac{1}{2}(x_k-A_kx_{k-1}-B_ku_k)^TR_k^{-1}(x_k-A_kx_{k-1}-B_ku_k)
}
e^{-\frac{1}{2}(x_{k-1}-\mu_{k-1})^T\Sigma_{k-1}^{-1}(x_{k-1}-\mu_{k-1})
}
} \\
&=\eta\int{
e^{-\frac{1}{2}
\Big[(x_k-A_kx_{k-1}-B_ku_k)^TR_k^{-1}(x_k-A_kx_{k-1}-B_ku_k)
+(x_{k-1}-\mu_{k-1})^T\Sigma_{k-1}^{-1}(x_{k-1}-\mu_{k-1})\Big]}
} \\
&=\eta\int{
e^{-\frac{1}{2}L_k}dx_{k-1}
}
\end{aligned}
$$

其中

$$
L_k=
(x_k-A_kx_{k-1}-B_ku_k)^TR_k^{-1}(x_k-A_kx_{k-1}-B_ku_k)
+
(x_{k-1}-\mu_{k-1})^T\Sigma_{k-1}^{-1}(x_{k-1}-\mu_{k-1})
$$

上面的积分比较难求，转而使用其它方式，式子中是对指数函数求积分，而且指数部分是积分变量的二次型。高斯分布也具有相同的特点，如果上面式子内的被积函数能够转换成高斯分布的概率密度函数，高斯概率密度函数积分为1那么就能得到积分结果了。高斯概率密度函数：

$$
p(x)=\frac{1}{\sqrt{|2\pi\Sigma|}}e^{-\frac{1}{2}(x-\mu)^T\Sigma^{-1}(x-\mu)}
$$

因此需要保证指数部分能够写成类似$(x-\mu)^T\Sigma^{-1}(x-\mu)$的形式：

$$
L_t=(x-\mu)^T\Sigma^{-1}(x-\mu)+b
$$

其中b为常数项，不影响指数结果。

后面可以通过配平等式两边来求出转换后的高斯分布的均值、方差以及表达形式。这部分就是凑一次二次项配平，这里公式比较杂就先不推了，使用常数项配平后消去积分项。

通过原理我们也能了解到，$\bar{bel}(x_k)$实际上是两个高斯分布的线性的组合，其实这个结论也来源于假设中，前面已经假设了当前状态$x_k$是符合高斯分布的。经过新一轮的输入后，状态$x_k$一直都是呈现高斯分布的特性的：

$$
\bar{bel}(x_k)\sim{N(A_k\mu_{k-1}+B_ku_k,A_k\Sigma_{k-1}A_k^T+R_k)}
$$

也就是预测部分：

$$
\bar{\mu}_k=A_k\mu_{k-1}+B_ku_k\\
\bar{\Sigma}_k=A_k\Sigma_{k-1}A_k^T+R_k
$$

调整部分：

$$
\mu_k=\bar{\mu}_k+D_k(z_k-C_k\bar{\mu}_k) \\
\Sigma_k=(I-D_kC_k)\bar{\Sigma}_k \\
D_k=\bar\Sigma_kC_k^T(C_k\bar\Sigma_kC_k^T+Q_k)^-1
$$

这样看来，共分两步：

**1.预测：从上一时刻状态分布预测下一时刻**

$$
\begin{matrix}
p(x_k{\mid}z_{1:k-1},u_{1:k})&=&{\int}p(x_k{\mid}x_{k-1},u_k)&p(x_{k-1}{\mid}z_{1:k-1},u_{1:k-1})dx_{k-1}\\
\mathbf{预测值}&&\mathbf{预测模型}&\mathbf{上一时刻分布}
\end{matrix}
$$

**2.更新：使用当前时刻预测值更新后验概率**

$$
\begin{matrix}
p(x_k{\mid}z_{1:k},u_{1:k})&{\propto}&p(z_k{\mid}x_k)&p(x_k{\mid}z_{1:k-1},u_{1:k})\\
\mathbf{后验概率}&&\mathbf{观测模型}&\mathbf{预测值}
\end{matrix}
$$

在每一帧数据到来时候，使用**贝叶斯滤波进行一次迭代**，从而获取对当前状态的估计。

其中$p(x_k{\mid}z_{1:k-1},u_{1:k})$是指在历史观测情况($z_{1:k-1}$)和当前输入$(u_{1:k})$的情况下状态($x_k$)的先验概率，这一步通过预测模型实现，这个预测模型对应这系统的状态方程。

上面是线性系统中卡尔曼滤波的推导，关于SLAM系统多为非线性系统，将卡尔曼滤波拓展到非线性系统成为拓展卡尔曼滤波，这里就不推导了。



## 非线性优化方法

**滤波一次观测仅仅迭代一次从而得出当前状态的估计**，而在实际应用中，希望通过更新的数据调整整体的状态，调整包含当前状态在内的多个历史状态，因此使用**迭代求解的非线性优化方法**。

然鹅这就要涉及到一个比较简单的问题，是否使用并优化全部时刻下的系统状态，下面先推导会使用所有时刻的观测和输入：

已知所有时刻的输入$u_{1:k}$和观测$z_{1:k}$，求所有时刻面的状态$x_{1:k}$因此要求$p(x_{1:k}{\mid}z_{1:k},u_{1:k})$，前面提到输入$u_{1:k}$此时的作用为初始值，因此这里不再使用输入$u_{1:k}$进行推导，对这个式子展开：

$$
p(x_{1:k}{\mid}z_{1:k})
={\eta}p(z_{1:k}{\mid}x_{1:k})p(x_{1:k})
$$

就从在输入和观测下，状态的概率分布$p(x_{1:k}{\mid}z_{1:k})$

变成求状态下，观测的概率分布$p(z_{1:k}{\mid}x_{1:k})$

从而求**最大后验概率**为：

$$
x^*_{MAP}=\arg\max_{x_{1:k}}p(x_{1:k}{\mid}z_{1:k})=\arg\max_{x_{1:k}}p(z_{1:k}{\mid}x_{1:k})p(x_{1:k})
$$

或者转变为求**极大似然概率**：

$$
=\arg\max_{x_{1:k}}p(z_{1:k}{\mid}x_{1:k})p(x_{1:k})
$$

取负对数最小：

$$
x^*_{MLE}=\arg\min_{x_{1:k}}\bigg[-\ln{p(z_{1:k}{\mid}x_{1:k})}-\ln{p(x_{1:k})}\bigg]
$$

假设状态$x_k$满足高斯分布：

$$
\begin{aligned}
&x_k\sim{N(\mu_{Xk},\Sigma_{Xk})} \\
p(x_k)&=\frac{1}{\sqrt{2\pi\Sigma_{Xk}}}e^{-\frac{1}{2}(x_k-\mu_{Xk})^T\Sigma_{Xk}^{-1}(x_k-\mu_{Xk})} \\
p(x_{1:k})&=\prod_{i=1}^{k}p(x_i) \\
&=\prod_{i=1}^{k}\frac{1}{\sqrt{2\pi\Sigma_{Xi}}}e^{-\frac{1}{2}(x_i-\mu_{Xi})^T\Sigma_i^{-1}(x_i-\mu_{Xi})} \\
\ln{p(x_{1:k})}&=\sum_{i=1}^{k}\ln(\frac{1}{\sqrt{2\pi\Sigma_{Xi}}})-
\frac{1}{2}\sum_{i=1}^{k}\Big[{(x_i-\mu_{Xi})^T\Sigma_{Xi}^{-1}(x_i-\mu_{Xi})}\Big] \\
&\propto{
-\sum_{i=1}^{k}\Big[{(x_i-\mu_{Xi})^T\Sigma_{Xi}^{-1}(x_i-\mu_{Xi})}\Big] 
}
\end{aligned}
$$

假设观测方程中噪声项满足高斯分布，那么似然也满足高斯分布，根据**多次独立观测**有全概率公式：

$$
\begin{aligned}
&p(z_i{\mid}x_{1:k})\sim{N(\mu_{Zk},\Sigma_{Zk})} \\
p(z_i{\mid}x_{1:k})&=\frac{1}{\sqrt{2\pi\Sigma_{Zk}}}e^{-\frac{1}{2}(z_k-\mu_{Zk})^T\Sigma_{Zk}^{-1}(z_k-\mu_{Zk})} \\
p(z_{1:k}{\mid}x_{1:k})&=\prod_{i=1}^{k}p(z_i{\mid}x_{1:k}) \\
&=\prod_{i=1}^{k}\frac{1}{\sqrt{2\pi\Sigma_{Zi}}}e^{-\frac{1}{2}(z_i-\mu_{Zi})^T\Sigma_{Zi}^{-1}(z_i-\mu_{Zi})} \\
\ln{p(z_{1:k}{\mid}x_{1:k})}&=\sum_{i=1}^{k}\ln(\frac{1}{\sqrt{2\pi\Sigma_{Zi}}})-
\frac{1}{2}\sum_{i=1}^{k}\Big[{(z_i-\mu_{Zi})^T\Sigma_{Zi}^{-1}(z_i-\mu_{Zi})}\Big] \\
&\propto{
-\sum_{i=1}^{k}\Big[{(z_i-\mu_{Zi})^T\Sigma_{Zi}^{-1}(z_i-\mu_{Zi})}\Big] 
}
\end{aligned}
$$

之前的推导中使用积分形式的全概率公式是为了消去积分项，这里使用离散形式的全概率公式是为了将积分项目展开。

将上面两个式子带入到$P$中去：

$$
\begin{aligned}
x^*_{MLE}&=\arg\min_{x_{1:k}}\bigg[-\ln{p(z_{1:k}{\mid}x_{1:k})}-\ln{p(x_{1:k})}\bigg] \\
&=\arg\min_{x_{1:k}}\bigg[{
\sum_{i=1}^{k}\Big[{(x_i-\mu_{Xi})^T\Sigma_{Xi}^{-1}(x_i-\mu_{Xi})}\Big]
+
\sum_{i=1}^{k}\Big[{(z_i-\mu_{Zi})^T\Sigma_{Zi}^{-1}(z_i-\mu_{Zi})}\Big] 
}\bigg] \\
&\triangleq\arg\min_{x_{1:k}}\sum_{i=1}^k\Big[{
\|z_i-\mu_{Zi}\|_{\Sigma_{Zi}}^2
+
\|x_i-\mu_{Xi}\|_{\Sigma_{Xi}}^2
}\Big]
\end{aligned}
$$

如果优化的状态从$x_{1:k}$变成$x_k$，那么上面的式子就简单不少。

转变成一个**最小二乘问题**：

$$
x^*_{MAP}=\arg\min_{x_{1:k}}
\sum_{i=1}^k\Big[{
\|z_i-\mu_{Zi}\|_{\Sigma_{Zi}}^2
+
\|x_i-\mu_{Xi}\|_{\Sigma_{Xi}}^2
}\Big]
$$

如果仅优化当前状态的位姿，这个式子就变成，这个式子的意义不大仅用来形象性参考：

$$
x^*_{MAP}=\arg\min_{x_{k}}
\sum_{i=1}^k\Big[{
\|z_i-\mu_{Zi}\|_{\Sigma_{Zi}}^2
\Big]
+
\|x_k-\mu_{Xk}\|_{\Sigma_{Xk}}^2
}
$$

实现最小二乘的优化还是比较困难的一件事情，多数情况使用现有的库，比如Ceres使用最速下降法优化程序提供的参数块并实现自动求导，亦或者是手动求导。GTSAM则使用因子图最大化后验概率的方式去做优化，G2O则是使用图优化的方法。

VSLAM中，在出现大回环或者对局部地图(滑窗)中的位姿进行优化时，求解方程组中的海塞矩阵实际上是一个非常大的矩阵导致方程难以求解因此SLAM后端优化的方法一直使用滤波器，因此很难优化历史时刻的位姿，直到一篇论文指出了SLAM过程中的海塞矩阵具有非常优秀的稀疏性，根本原因来自于位姿之于观测的稀疏性，通过舒尔补的方式可以边缘化历史帧的信息，从而支持SLAM方法使用优化方法求解。

再说上面的式子，等号左侧代表一个求解一个最大后验概率，而等式右侧是一个待优化的目标变量和目标函数。

式子右侧表示通过调整所有时刻的位姿，使上式到达一个最小值。在VSLAM中，每一帧观测$z$实际上是一堆特征点的二维坐标，其均值$\mu_Z$具有相同的物理意义，而其协方差矩阵$\Sigma_Z$则是一个对角阵，如果拍摄条件较好那么可以看做一个单位矩阵。

如何推导出来上式子对新手来说是一件非常困难的事情，同样地去实现上式的求导和优化也很困难。

但好在如何使用工具求解已知的问题并不困难。关于高斯牛顿法中如何推导求解最小二乘问题可以先不谈，先来谈谈GTSAM和G2O的基础模型是如何构建的，截下来就去使用他们就好。



## 因子图（Factor Graph）

在一个**函数**中，**存在若干可分解的多项式**，那么可以用**因子图**进行**图模型**表述：

$$
G(x_1,x_2,x_3,x_4)=F_A(x_1)F_B(x_1,x_2)F_C(x_2,x_3)F_D(x_3,x_4)
$$

可以表示为如下的图模型，其中方框表示因子，而圆圈为变量：

<img src="factor.png" alt="factor" style="zoom:10%;" />

或者表示为如下的图模型：



<img src="factor2.png" alt="img" style="zoom:10%;" />

在某些情况下因子图可以存在**环路**。


## 因子图描述SLAM问题

下图是一个**四步隐马尔科夫模型 **，用于描述SLAM中的观测问题，存在一个**先验概率**$p(X1)$，两个**转移概率**$p(X2)$和$p(X3)$，并有一组**测量值**$Z1,Z2,Z3$。

其中先验概率物理在初始位姿。

<img src="factorslam.png" alt="factorslam" style="zoom:10%;" />

则SLAM中的状态估计问题表述为：

$$
p(X_1,X_2,X_3,X_4{\mid}Z_1,Z_2,Z_3,Z_4)=p(\mathbf{X}{\mid}\mathbf{Z})
$$

$$
p(\mathbf{X}{\mid}\mathbf{Z})=\frac{p(\mathbf{Z}{\mid}\mathbf{X})p(\mathbf{X})}{p(\mathbf{Z})}{\propto}p(\mathbf{Z}{\mid}\mathbf{X})p(\mathbf{X})
$$

展开：

$$
\begin{aligned}
maxp(\mathbf{X}{\mid}\mathbf{Z})&=p(X_1)p(X_2|X_1)p(X_3|X_2)p(X_4|X_3)p(Z_1|X_1)p(Z_2|X_2)p(Z_3|X_3)p(Z_4|X_4)\\
&=F_1(X_1)F_2(X_1,X_2)F_3(X_2,X_3)F_4(X_3,X_4)F_5(X_1,Z_1)F_6(X_2,Z_2)F_7(X_3,Z_3)F_8(X_4,Z_4)
\end{aligned}
$$

这个式子就将SLAM中的位姿优化问题变成了一系列因子式的乘积，进而可以转为因子图。

从而构建因子图为：

<img src="factorslam2.png" alt="factorslam2" style="zoom:9%;" />

因此，在SLAM问题中，因子图呈现为**隐马尔科夫模型**的形式。



## 马尔科夫链问题与GTSAM

不讨论实际的SLAM系统，先考虑简单的**马尔可夫链**形成的**因子图**：

<img src="markovchainproblem.png" alt="markovchainproblem" style="zoom:10%;" />

上图中$X_1,X_2,X_3$均为**可直接观测的状态量**。

存在与一个变量相关的因子$F_1$称为**一元因子**。

存在与两个变量相关的因子$F_2,F_3$称为**二元因子**。


## 隐马尔科夫模型与GTSAM

在上个例子中，状态可**直接观测**（观测存在误差），传感器比如**GNSS**，**地磁计**等。

在真实世界中往往存在一些其他这种情况，比如**雷达里程计**，**视觉里程计**等，需要用到**隐马尔可夫模型**。

在上个问题的基础上，引入**测量因子**来处理**外部测量**。

不同于**一元因子**，**二元因子**的概念，**测量因子**通过传感器建模来返回**误差评价**与**观测模型的雅可比**，在变量数目范畴上属于**一元因子**。

在**最大化似然概率**的时候，将问题化简称为**最小二乘问题**：

$$
L(\mathbf{q};\mathbf{m})=exp(-\frac{1}{2}\|{h(\mathbf{q})-\mathbf{m}}\|^2_\Sigma)\propto{p(\mathbf{m}|\mathbf{q})}
$$

其中$\Sigma$为**噪声协方差**，$h(q)$为**测量函数**通过测量值$\mathbf{m}$与位姿变量$\mathbf{q}$的**映射**建立**误差函数（残差项）**。

在下面的例子中：

$$
\mathbf{m}=
\begin{bmatrix}
GpS_x\\GpS_y
\end{bmatrix}
$$

$$
\mathbf{q}=
\begin{bmatrix}
x\\y\\\theta
\end{bmatrix}
$$

其中$GpS_x，GpS_y$为GpS测量值，$x,y,\theta$为系统当前状态。

在此问题中，系统使用GpS测量当前位姿，GpS会测量当前位姿的$x,y$坐标。

在**三个时刻**产生的**因子图**如下：

<img src="problem2.png" alt="problem2" style="zoom:10%;" />

其中$F_4(X_1,Z_1),F_5,F_6$为一元测量因子，需要自定义如下的类来实现：



## 闭环约束

在**SLAM问题**中，常常存在**闭环**的情况，即系统在经过一段时间后到达已经经过的位置上来，与历史数据相比较，当前测量与历史测量**具有相同或者相似的外部观测**。

通过约束这种情况可以减少SLAM系统的**累计误差**。

而**因子图**是一个可以存在环路的**有向图**。因此可以利用因子图的这个特性来进行约束。

<img src="loopfactor.png" alt="loopfactor" style="zoom:10%;" />

同其他约束类似，环路约束：
