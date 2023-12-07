## 直线、平面、圆的拟合

一般情况下使用矩阵分解就可以解决拟合的最小二乘问题，而不是用优化方法。

给定一组点$p=\{[x_i,p_i,z_i]^T\},i\in[1,k]$，有些使用时三维点有些时候时二维点。

### 平面&&直线

在直线拟合的过程中，一般将方程表示为$Ax+By+C=0$而不是$y=kx+b$，主要原因是与x轴垂直的直线，斜率法是无法表现的。同理，平面使用$Ax+By+Cz+D=0$来表示，下面的拟合思路同时适用于平面和直线：

对于平面的拟合问题，是一个最小二乘的问题：
$$
\arg_{A,B,C}\min{\sum(\frac{Ax_i+By_i+Cz_i+D}{\sqrt{A^2+B^2+C^2}})^2}
$$
其中$[A,B,C]^T$是平面的法向量，假设这个法向量是一个模长为1的向量，那么简化一下将目标函数对于$D$求导，求解$D$：
$$
\frac{\partial{f}}{\partial{D}}=0=2\sum(Ax_i+By_i+Cz_i+D)=0\\
D=-\frac{\sum(Ax_i+By_i+Cz_i)}{n}
$$
那个整个目标函数就变成了如下最小的形式：
$$
\sum(Ax_i+By_i+Cz_i)^2
$$
写成矩阵的形式就是：
$$
f=\sum(Ax_i+By_i+Cz_i)^2\\
=
\begin{bmatrix}
A,B,C
\end{bmatrix}
\begin{bmatrix}
\sum{x^2}&\sum{xy}&\sum{xz}\\
\sum{xy}&\sum{y^2}&\sum{yz}\\
\sum{xz}&\sum{yz}&\sum{z^2}\\
\end{bmatrix}
\begin{bmatrix}
A\\B\\C
\end{bmatrix}
$$

直线的情况就是：
$$
f=\sum(Ap+Bq)^2\\
p_i=x_i-\bar{x},q_i=y_i-\bar{y}\\
f=
\begin{bmatrix}
A&B
\end{bmatrix}
\begin{bmatrix}
\sum{p^2}&\sum{pq}\\
\sum{pq}&\sum{q^2}\\
\end{bmatrix}
\begin{bmatrix}
A\\B
\end{bmatrix}
$$

要求这个方程的最小值，$[A,B,C]^T$是单位向量，中间的矩阵式实对称矩阵，这个式子最小的时候，$[A,B,C]^T$对应的是中间矩阵的特征值最小的特征向量，于是对中间的矩阵进行分解，取最小的特征值对应的特征向量就是平面的法向向量。



**三维空间中的直线**

使用点+向量的形式表示直线$l=A+dD$，A为直线上一个点，D为直线的方向向量。

点$X$到直线$l$的距离就变成$X$到$A$的向量$Y=X-A$与$Y$在直线上的投影$(Y^TD)D$作差求得，目标方程就变成：
$$
f=\sum(Y_i^2-(D^T\cdot{}Y_i)^2)
$$
先求$A,\frac{\partial{f}}{\partial{A}}=\frac{\partial{f}}{\partial{Y}}{\cdot}\frac{\partial{Y}}{\partial{A}}=-\frac{\partial{f}}{\partial{Y}}=0$，得：
$$
\frac{\partial{f}}{\partial{Y}}=\sum(2Y_i-2DD^TY_i)=2(I-DD^T)\sum{Y_i}=0\\
\sum{Y_i}=\sum(X_i-A)=0\\
A=\frac{\sum{X_i}}{n}=\bar{X}
$$
点A就是所有点的终点，直线经过所有点的中心点。将目标函数改写：
$$
f=\sum(Y_i^2-(Y_i^TD)^2)=\sum(D^TY_i^TY_iD)=D^T\sum((Y_i^TY_i)I-Y_iY_i^T)D
$$
这里方向向量是一个单位向量，所以$D^TD=1$，所以$Y_i^2=D^TY_i^TY_iD^T$。

令$S=\sum((Y_i^TY_i)I-Y_iY_i^T)$，化简：
$$
f=D^TSD,\|D\|=1
$$
这个形式取的最小值是$S$最小的特征值对应的特征向量.





### 圆的拟合

源的拟合比较复杂， 首先目标函数是一个点到圆的弧线的距离，是一个二次型：
$$
f=\sum{(\sqrt{((x_i-a)^2+(y_i-b)^2)}-r)^2}\\
$$
这个方程不是线性的，根据两种方法来进行求解可已将其进行进一步处理：

有两种方法，一种kasa方法，一种pratt方法，通常pratt方法更加优秀。

**kasa方法：**

将$(d-r)^2$的问题转换为$(d^2-r^2)^2$的问题，看起来$(d-r)$取最小值的时候，$(d^2-r^2)$是同样取最小值的，于是目标函数就变成：
$$
g=\sum((x_i-a)^2+(y_i-b)^2-r^2)
$$

$$
g=\sum(x_i^2+y_i^2+Bx_i+Cy_i+D)^2\\
B=-2a,C=-2b,D=a^2+b^2-r^2
$$

对这个目标函数分别求$A,B,C$的偏导数，列出方程：
$$
\begin{cases}
\frac{\partial{g}}{\partial{D}}=2(\sum{z}+\sum{xB}+\sum{yC}+nD)=0\\
\frac{\partial{g}}{\partial{B}}=2(\sum{xz}+\sum{x^2B}+\sum{xyC}+\sum{xD})=0\\
\frac{\partial{g}}{\partial{C}}=2(\sum{yz}+\sum{xyB}+\sum{y^2C}+\sum{yD})=0
\end{cases}
$$
写成矩阵形式就是：
$$
\begin{bmatrix}
\sum{x}&\sum{y}&n\\
\sum{x^2}&\sum{xy}&\sum{x}\\
\sum{xy}&\sum{y^2}&\sum{y}\\
\end{bmatrix}
\begin{bmatrix}
B\\C\\D
\end{bmatrix}
=
\begin{bmatrix}
-\sum{z}\\
-\sum{xz}\\
-\sum{yz}
\end{bmatrix}
$$
使用求解线性方程的形式求解$[B,C,D]$三个参数即可。

这种方法的适用性比较窄：$d^2-r^2=(d-r)(d+r)$，当$d-r$比较小的时候，所有的采样点都在同一个圆的时候，算法效果良好，当$d-r$变大，噪声较大的时候，$\min{(d^2-r^2)}$会试图减小$d+r$项，使得算法出来的半径比较小。效果还和采样点的分布有关，如果采样点均匀分布到圆上，效果比较好，如果是1/4圆或者1/8Z圆，效果比较差。



**pratt方法：**

同kasa方法，在整个目标函数中除以$(2R)^2$，根据kasa结果$a=-\frac{B}{2A},b=-\frac{C}{2A}，R=\frac{\sqrt{B^2+C^2-4AD}}{2A}$，将圆的方程变换做下列的形式，
$$
g=(\frac{\sum{A(x_i^2+y_i^2)+Bx_i+Cy_i+D}}{B^2+C^2-4AD})^2
$$
其中令$A=1$，其他参数根据A做反归一化，目标函数就变成：
$$
g={\sum{A(x_i^2+y_i^2)+Bx_i+Cy_i+D}})^2
$$
 并且带有约束：
$$
B^2+C^2-4AD=1
$$
将上面的目标函数求导并写成矩阵形式：
$$
g=Z^TMZ(Z=[A,B,C,D]^T)\\
M=
\begin{bmatrix}
\sum{z^2}&\sum{xz}&\sum{yz}&\sum{z}\\
\sum{xz}&\sum{x^2}&\sum{xy}&\sum{x}\\
\sum{yz}&\sum{xy}&\sum{y^2}&\sum{y}\\
\sum{z}&\sum{x}&\sum{y}&n\\
\end{bmatrix}\\
z_i=x_i^2+y_i^2
$$

$$
Z^TBZ=[A,B,C,D]
\begin{bmatrix}
0&0&0&-2\\
0&0&0&0\\
0&0&0&0\\
-2&0&0&0\\
\end{bmatrix}
\begin{bmatrix}
A\\B\\C\\D
\end{bmatrix}
=1
$$

通过引入拉格朗日因子来求解最小值问题，	
$$
g(Z,\eta)=Z^TMZ-\eta(Z^TBZ-1)
$$
对$Z$求导，$\frac{\partial{g(Z,\eta)}}{\partial{Z}}=0=MZ-\eta{}BZ$，因为$B$可逆，可以变换为$B^{-1}MZ=\eta{}Z$，可知，$\eta$是矩阵$B^{-1}M$的特征值，$Z$是其特征向量。

由$MZ-\eta{}BZ=0$得，$Z^TMZ-\eta{}Z^TBZ=Z^TMZ-\eta{}=0$，即$Z^TMZ$的极值是$\eta$，那么矩阵$B^{-1}M$有四个特征值，取一个其中最小的非负特征值对应的特征向量就是$Z$.从而拟合出圆的参数。

pratt方法优点在于，消除了半径的影响，使得计算曲率很大，噪音很大也能正常工作，并且效率也比较高。





**三维情况下的圆柱**

使用中心点$A$，柱面中轴线方向向量$D$，半径$r$来表示一个柱面，目标函数是非常直观的：
$$
f=\sum{(dist(P,A,D)-r)^2}\\
dist(P,A,D)=(P-A)^T(P-A)-(D^T(P-A))^2=X^TX-(D^TX)^2
$$
将上面这个式子的主要部分展开以获取对参数的雅可比:
$$
f=\sum{(X^TX-(}
\begin{matrix}
x_1^2d_1^2&+x_1x_2d_1d_2&+x_1x_3d_1d_3\\
+x_1x_2d_1d_2&+x_2^2d_2^2&+x_2x_3d_2d_3\\
+x_1x_3d_1d_3&+x_2x_3d_2d_3&+x_3^2d_3^2
\end{matrix}
)-r)^2
$$

$$
\frac{\partial{f}}{\partial{r}}=\sum{2(X^TX-(D^TX)^2-r)\cdot(-r)}\\
\frac{\partial{f}}{\partial{d_1}}=\sum{2(X^TX-(D^TX)^2-r)\cdot(2x_1^2d_1+2x_1x_2d_2+2x_1x_3d_3)}\\
\frac{\partial{f}}{\partial{a_1}}=\sum{2(X^TX-(D^TX)^2-r)\cdot(2d_1^2a1-2p_1-2x_2d_1d_2-2x_3d_1d_3)}
$$

然后用一个迭代过程来求。值得注意的是参数$A$在这里实际上是不唯一的，上面$dist$中表述的为$P$到$AD$的距离，$A$在中轴线上的任一点这个值都是一样的，目前迭代求解没啥问题，但方程对$A$的约束不满秩，可以考虑将A固定在某个平面上，加快收敛速度，因为$A$的初值是均值给的，所以就先没尝试。



**点到平面、直线、柱面的残差**
$$
f(P,Plane)=\frac{Ap_1+Bp_2+Cp_3+D}{\sqrt{A^2+B^2+C^2}}
$$

$$
f(P,Line)=(P-A)^2-(D^T\cdot{}(P-A))^2
$$

$$
f(P,Cylinder)=(P-A)^2-(D^T(P-A))^2-r
$$

