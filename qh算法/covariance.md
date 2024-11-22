# 协方差攻略

## 方差

方差是在概率论和统计方差衡量**随机变量**或**一组数据**时**离散程度**的度量。

给定一组一维数据$A={a_i}(i\in{Z},i<N)$​​，那么通过统计方法能够得知**样本方差**统计量：
$$
S^2=\frac{\sum(a_i-\overline{a})}{N-1}
$$
其对应的**无偏估计量**为$D(X)$​​​，设样本均值为$EX$​​​，那么可以计算样本方差：
$$
D=E(X^2)-EX^2=E({(X-EX)}^2)
$$
通过上面这个公式可以在**一次循环**内求出一维数据方差：

~~~c++
double sum = 0, sum2 = 0;
for(double val : Data)
{
    sum += val;
    sum2 += val * val;
}
sum2 = sum2 / n- (sum / n) * (sum / n);//方差
sum = sum / n;//均值
~~~

## 二维数据的协方差

设一个笛卡尔二维坐标系内存在多个点构成点集$A$，$A_i=(x_i,y_i),i\in{Z},i<N$。

$x$​坐标的**离散程度**$D(X)$​，$y$​坐标的**离散程度**$D(Y)$​​。

X，Y**协方差**表示$Cov(X,Y)$​​​：
$$
\begin{aligned}
Cov(X,Y)&=E{[(X-EX)(Y-EY)]}\\
&=E(XY)-EXEY
\end{aligned}
$$

$$
Cov(X,Y)=\frac{1}{n-1}\sum^N_{i=1}{(x_i-\overline{x})(y_i-\overline{y})}
$$

知道**协方差**后能求出**相关系数**：
$$
\rho_{XY}=Corr(X,Y)=\frac{Cov(X,Y)}{\sqrt{D(X)\cdot\sqrt{D(Y)}}}
$$
**一次循环**求出二维数据协方差：

~~~c++
double meanX = 0, meanY = 0;
double DXY = 0;
for(i = 0; i < n; i++)
{
    meanX = X[i];
    meanY = Y[i];
    DXY += X[i] * Y[i];
}
meanX /= n;
meanY /= n;
cov = cov / n - meanX * meanY;//协方差
~~~

两个维度数据之间的**协方差**代表了数据在两个维度上分布的**相关程度**，但很难代表**整体分布情况**。

## 协方差矩阵

如何才能代表多维度数据的整体分布情况呢，**协方差矩阵**。

设一个笛卡尔二维坐标系内存在多个点构成点集$A$，$A_i=(x_i,y_i),i\in{Z},i<N$。

对于二维数据，那么共有四组**协方差**需要计算：
$$
\mathbf{\Sigma} = 
\begin{bmatrix}
Cov(X,X)& Cov(X,Y)\\
Cov(Y,X)& Cov(Y,Y)
\end{bmatrix}
$$
其中${Cov(X,X)}$​​和${Cov(X,X)}$​​​​意义上和数值上等同于方差。

而${Cov(X,Y)}$和${Cov(Y,X)}$在意义上和数值上均相同。​

可以看出$\mathbf{\Sigma}$为**对阵矩阵**。

将二维数据拓展到$M$​​​维数据，**协方差矩阵**是一个$M\times{M}$​的**对称方阵**：
$$
\mathbf{\Sigma} = 
\begin{bmatrix}
Cov(X_1,X_1)& Cov(X_1,X_2)& ...& Cov(X_1,X_m)\\
Cov(X_2,X_1)& ...& & ...&\\
...& ...& & ...\\
Cov(X_m,X_1)& Cov(X_m,X_2)& ...& Cov(X_m,X_m)
\end{bmatrix}
$$
在这种情况下，每个维度**分布不独立**，而是有了某种联系，称作**多元正态分布(multi-variate Gaussian distribution)**。

设$M$​维向量$\mathbf{x}$​​在任何维度的**正态分布**的**概率密度函数**可表示为：
$$
p(\mathbf{x})=\mid2\pi\mathbf{\Sigma}\mid^{-\frac{1}{2}}e^{-\frac{1}{2}(\mathbf{x-\mathbf{\mu}})^T\Sigma^T(\mathbf{x}-\mathbf{\mu})}
$$
其中$\mathbf{\Sigma}$​​是协方差矩阵，$\mathbf{\mu}$​​为均值。对此**协方差矩阵**进行特征值分解，令：
$$
\Sigma=UAU^T
$$
其中$U$​​​​是特**征向量矩阵**可以看作**旋转矩阵**，表示**整体分布的方向**。

$A=SS^T$为**特征值矩阵**，可以看作**尺度变换矩阵**，表示**整体分布的缩放**。

$\mu$​​​则**表示整体中心**。

通过**特征值分解(EVD)**或者**奇异值分解(SVD)**来处理协方差矩阵能够得到分布信息。

## 协方差矩阵计算

给出大概的思路，比较常用的Eigen

~~~C++
#program by C++ relay on Eigen
//计算协方差矩阵
//模板参数分别为基本数据类型 数据维度
template <typename T, int N>
bool covMatrix(Eigen::Matrix<vector<T>, N, 1> data, Eigen::Matrix<T, N, N>& Cov)
{
  for (int i = 0; i < N; i++)
  {
    for (int j = 0; j <= i; j++)
    {
      vector<T> A(data(i, 0)), B(data(j, 0));
      if (A.size() != B.size()) return false;
      size_t size = A.size();
      T meanX = 0, meanY = 0, cov = 0;
      for (int k = 0; k < size; k++)
      {
        meanX += A[k];
        meanY += B[k];
        cov += A[k] * B[k];
      }
      cov = cov / size - meanX / size * meanY / size;
      Cov(i, j) = cov;
      Cov(j, i) = cov;
    }
  }
  return true;
}
~~~

## 高维数据之间的协方差

之前说的是高维数据每个维度之间的协方差，是数据本身不同维度之间的的协方差。

如果存在多组高维空间上的数据，那么他们的协方差应该叫做高维数据之间的协方差。

存在两组$N$维向量$A\{\mathbf{a_i},i\in{N},i<k\}$，$B\{\mathbf{b_i},i\in{N},i<k$，在同样的维度上两者的数据独立同分布，那么$cov{(A,B)}$应该是$N\times{N}$的矩阵，代表着$N$个数据维度上的协方差。此时：
$$
Cov{(A,B)}=E(AB)-EAEB
$$
展开就是：
$$
Cov(A,B)=\frac{1}{k}\sum^k_{i=0}(\mathbf{a_i}-\overline{\mathbf{a}})(\mathbf{b_i}-\overline{\mathbf{b}})^T (N\times{N})
$$
也就是数据去中心后进行$\mathbf{a}\mathbf{b}^T$​。​

## 多元正态分布随机向量生成

在Matlab和R语言中，可以轻松使用rmvnorm()来从多元正太分布中抽样，在C++中就比较复杂了，要么调用Armadillo库中的rmvnorm，要么自己实现一下，参考资料有。

[Generating multivariate normal distribution samples using C++11 and Eigen library. - Robotics with ROS (ros-developer.com)](https://ros-developer.com/2017/11/15/generating-multivariate-normal-distribution-samples-using-c11-eigen-library/)

[beniz/eigenmvn: C++11 multivariate normal distribution sampling using Eigen matrices (github.com)](https://github.com/beniz/eigenmvn)

~~~C++
random
~~~















