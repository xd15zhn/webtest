**摘要：** 首先推导了全系数和等于1的证明过程，分析了等效时间常数的概念，然后推导了递推最小二乘公式并用于参数辨识的方法，最后给几个仿真的例子。
# 全系数之和等于1
&emsp;&emsp;被控对象用微分方程

$$ y^{(n)}=a_{n-1}y^{(n-1)}+\cdots+a_0y+b_{n-1}u^{(n-1)}+\cdots+b_0u\qquad(1.1) $$

描述，其相应的差分方程为

$$ y(k)=\alpha_1y(k-1)+\cdots+\alpha_ny(k-n)
+\beta_1u(k-1)+\cdots+\beta_nu(k-n)\qquad(1.2) $$

对应的系统拉氏变换和z变换分别为

$$\begin{aligned}
G(s) =& \frac{b_{m-1}s^{m-1}+\cdots+b_1s+b_0}{1-a_{n-1}s^{n-1}-\cdots-a_1s-a_0} \\
G(z) =& \frac{\beta_{m-1}z^{m-1}+\cdots+\beta_1z+\beta_0}
{1-\alpha_{n-1}z^{n-1}-\cdots-\alpha_1z-\alpha_0} \\
\end{aligned}$$

式(2)中的 $\alpha$ 和 $\beta$ 参数在下面两种情况下等于或近似等于1。
(1) 系统静态增益 $D=-b_0/a_0$，则差分方程系数之间满足

$$ \sum_{i=1}^na_i-\frac{b_0}{a_0}\sum_{j=0}^{n-1}\beta_j=1 $$

且在 $-b_0/a_0=1$ 时，所有系数之和为1。
&emsp;&emsp;拉氏变换和z变换的终值定理分别为，当终值存在时，

$$\begin{aligned}
f_s(\infty) =& \lim_{s\rightarrow 0}sF(s) \\
f_z(\infty) =& \lim_{z\rightarrow 1}(z-1)F(z) \\
\end{aligned}$$

阶跃信号的拉氏变换和z变换分别为

$$ U(s) = \frac{1}{s},U(z) = \frac{z}{z-1} $$

所以连续系统和离散系统的终值分别为

$$\begin{aligned}
f_s(\infty) =& -\frac{b_0}{a_0} \\
f_z(\infty) =& \frac{\sum\beta_j}{1-\sum\alpha_i} \\
\end{aligned}$$

当终值 $f_s(\infty)=f_z(\infty)=1$ 时，$\sum\alpha_i+\sum\beta_j=1$。
(2) 采样周期 $\Delta t\rightarrow 0$，且 $a_0,b_0$ 为有限值，则所有参数的和

$$
\lim_{\Delta t\rightarrow 0}\sum\alpha_i+\sum\beta_j
=\lim_{\Delta t\rightarrow 0}\left[1+(D-1)
\left(\frac{\Delta t}{T'}\right)^n\right]=1
$$

其中 $T'$ 为等效时间常数，且 $-a_0=(\frac{1}{T'})^n$。
&emsp;&emsp;证明：当 $\Delta t\rightarrow 0$ 时，

$$\begin{aligned}
\dot{y} =& \frac{y(k)-y(k-1)}{\Delta t} \\
\ddot{y} =& \frac{y(k)-2y(k-1)+y(k-2)}{(\Delta t)^2} \\
y^{(3)} =& \frac{y(k)-3y(k-1)+3y(k-2)-y(k-3)}{(\Delta t)^3} \\
\vdots& \\
y^{(n)} =& \frac{\displaystyle\sum_{i=0}^n(-1)^iC_n^iy(k-i)}
{(\Delta t)^n} \\
\end{aligned}$$

分子的各系数呈杨辉三角。将上述关系代入式(1)得（先以二阶系统为例）

$$\begin{aligned}
& \ddot{y} = a_1\dot{y}+a_0y+b_1\dot{u}+b_0u \\
& \frac{y(k)-2y(k-1)+y(k-2)}{(\Delta t)^2} = a_1\frac{y(k-1)-y(k-2)}{\Delta t}
+a_0y(k-2)+b_1\frac{u(k-1)-u(k-2)}{\Delta t}+b_0u(k-2) \\
\end{aligned}$$
$$\begin{aligned}
y(k) =& 2y(k-1)-y(k-2)+a_1[y(k-1)-y(k-2)]\Delta t
+b_1[u(k-1)-u(k-2)]\Delta t+[a_0y(k-2)+b_0u(k-2)](\Delta t)^2 \\
=& (a_1\Delta t+2)y(k-1) +(a_0\Delta^2t-a_1\Delta t-1)y(k-2)
+b_1\Delta tu(k-1) +(b_0\Delta^2t-b_1\Delta t)u(k-2) \\
=& \alpha_1y(k-1)+\alpha_2y(k-2)+\beta_1u(k-1)+\beta_2u(k-2)
\end{aligned}$$
$$\begin{aligned}
\sum\alpha_i+\sum\beta_j
=& (a_1\Delta t+2)+(a_0\Delta^2t-a_1\Delta t-1)+b_1\Delta t
+(b_0\Delta^2t-b_1\Delta t) \\
=& (a_0+b_0)\Delta^2t+2-1 \\
\lim_{\Delta t\rightarrow 0}\sum\alpha_i+\sum\beta_j =& 1
\end{aligned}$$


可见不包含 $\Delta t$ 的一阶项。再推广到n阶，省略包含 $\Delta t$ 的高阶项
$$\begin{aligned}
y(k) =& \sum_{i=1}^n(-1)^iC_n^iy(k-i)
+a_{n-1}\Delta t\left(\sum_{i=0}^{n-1}(-1)^iC_n^iy(k-i)\right)
+a_{n-2}\Delta^2t\left(\sum_{i=0}^{n-2}(-1)^iC_n^iy(k-i)\right) \\
&+\cdots+b_{n-1}\Delta t\left(\sum_{i=0}^{n-1}(-1)^iC_n^iu(k-i)\right)
+b_{n-2}\Delta^2t\left(\sum_{i=0}^{n-2}(-1)^iC_n^iu(k-i)\right) \\
\end{aligned}$$

进一步观察得到杨辉三角各行元素的和为0，而第一个元素为1，所以等号右边第一项的系数和 $\displaystyle\sum_{i=1}^n(-1)^iC_n=1$，剩下的每一项中的系数都是完整的杨辉三角中的一行，系数和都是0，只剩下杨辉三角第一行的 $a_0y(k-n)\Delta^nt$ 和  $b_0u(k-n)\Delta^nt$，都是 $\Delta t$ 的高阶小量，可以忽略。
&emsp;&emsp;这种推导方法用的是欧拉离散化方法，文末的第一个参考文献证明了用其他方法离散化的系数和也是1。
&emsp;&emsp;根据微分方程系数 $a_i$ 与极点 $p_i$、时间常数 $T_i$ 的关系可以知道，当 $a_0 \neq 0$ 时
$$ -a_0 = \prod_{i=1}^n p_i=\prod_{i=1}^n \frac{1}{T_i}>0 $$
可以定义
$$ T'=\sqrt{\prod_{i=1}^n T_i} $$
为等效时间常数。于是有 $a_0=-\displaystyle\left(\frac{1}{T'}\right)^n$，又因 $(-b_0/a_0)=D$ 为等效静态增益，所以如果不忽略 $\Delta t$ 的两个高阶小量，则
$$\begin{aligned}
\sum_{i=1}^n \alpha_i+\sum_{j=0}^{n-1} \beta_j
=& 1+a_0\Delta^nt+b_0\Delta^nt \\
=& 1+a_0(1-D)\Delta^nt \\
=& 1+(D-1)\left(\frac{\Delta t}{T^{\prime}}\right)^n \\
\end{aligned}$$

# 递推最小二乘法
对多组数据$\vec{x}$和$y$，满足
\[y_i = \vec{x}^\mathrm{T}_i\vec{\theta}\]
其中$\vec{x}$是输入数据向量，$y$是输出数据标量。写成矩阵形式
\[\vec{y} = X\vec{\theta}\]
其中(以3组数据为例)
$$
    X = \begin{bmatrix}
        \vec{x}^\mathrm{T}_1 \\
        \vec{x}^\mathrm{T}_2 \\
        \vec{x}^\mathrm{T}_3
    \end{bmatrix}
$$
有$k$组数据时记作
$$
    X^\mathrm{T}_k = \begin{bmatrix}
        \vec{x}_1 & \vec{x}_2 & \vec{x}_3 &
        \cdots & \vec{x}_k
    \end{bmatrix}
$$
已知最小二乘法的解为
$$
    \vec{\theta}
    =(X^\mathrm{T}X)^{-1}
    X^\mathrm{T}\vec{y}\qquad(2.1)
$$
令$P = (X^\mathrm{T}X)^{-1}$，
则加上递推后
$$\begin{aligned}
    P_k^{-1} =& X_k^\mathrm{T}X_k
    = \sum_{i=1}^k\vec{x}_i\vec{x}^\mathrm{T}_i \\
    =& \sum_{i=1}^{k-1}\vec{x}_i\vec{x}^\mathrm{T}_i
    +\vec{x}_k\vec{x}^\mathrm{T}_k \\
    =& P_{k-1}^{-1} + \vec{x}_k\vec{x}_k^\mathrm{T}
\end{aligned}$$
同理可得
$$
    X_k^\mathrm{T}\vec{y}_k
    =X_{k-1}^\mathrm{T}\vec{y}_{k-1}
    +\vec{x}_ky_k
$$
于是代入式(2.1)得到
$$\begin{aligned}
    \vec{\theta}_k =& P_kX_k^\mathrm{T}\vec{y}_k \\
    =& P_k(X_{k-1}^\mathrm{T}\vec{y}_{k-1}
    +\vec{x}_ky_k) \\
    =& P_k(P_{k-1}^{-1}\vec{\theta}_{k-1}
    +\vec{x}_ky_k) \\
    =& P_k(P_k^{-1}\vec{\theta}_{k-1}
    -\vec{x}_k\vec{x}_k^\mathrm{T}\theta_{k-1}+\vec{x}_ky_k) \\
    =& \vec{\theta}_{k-1} + P_k\vec{x}_k
    (y_k-\vec{x}_k^\mathrm{T}\theta_{k-1}) \\
    =& \vec{\theta}_{k-1}
    +(P_{k-1}^{-1} + \vec{x}_k\vec{x}_k^\mathrm{T})^{-1}
    \vec{x}_k(y_k-\vec{x}_k^\mathrm{T}\theta_{k-1}) \\
    =& \vec{\theta}_{k-1}
    +(P_{k-1}-\frac{P_{k-1}\vec{x}_k\vec{x}_k^\mathrm{T}
    P_{k-1}}{1+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k})
    \vec{x}_k(y_k-\vec{x}_k^\mathrm{T}\theta_{k-1}) \\
\end{aligned}$$
其中
$$
    P_k = P_{k-1}-\frac{P_{k-1}\vec{x}_k\vec{x}_k^\mathrm{T}
    P_{k-1}}{1+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k}
$$
用到了下面的矩阵求逆公式及其引理
$$\begin{aligned}
    (A+BCD)^{-1} =& A^{-1}-A^{-1} B
    (DA^{-1}B+C^{-1})^{-1}DA^{-1} \\
    (A+\vec{u}\vec{u}^\text{T})^{-1} =& A^{-1}
    -\frac{A^{-1}\vec{u}\vec{u}^\text{T} A^{-1}}
    {1+\vec{u}^\text{T}A^{-1}\vec{u}}
\end{aligned}$$
令
$$
    K_k = \frac{P_{k-1}\vec{x}_k}
    {1+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k}
$$
则
$$\begin{aligned}
    P_k =& (I-K_k\vec{x}_k^\mathrm{T})P_{k-1}\\
    P_k\vec{x}_k =& P_{k-1}\vec{x}_k
    -K_k\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k \\
    =& \frac{P_{k-1}\vec{x}_k
    (1+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k)
    -P_{k-1}\vec{x}_k\vec{x}_k^\mathrm{T}
    P_{k-1}\vec{x}_k}
    {1+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k} \\
    =& \frac{P_{k-1}\vec{x}_k}
    {1+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k} \\
    =& K_k \\
\end{aligned}$$
总结成递推公式得到
$$\begin{aligned}
    K_k =& \frac{P_{k-1}\vec{x}_k}{1+\vec{x}_k^\mathrm{T}
    P_{k-1}\vec{x}_k} \qquad(2.2)\\
    P_k =& (I-K_k\vec{x}_k^\mathrm{T})P_{k-1} \\
    \vec{\theta}_k =& \vec{\theta}_{k-1}
    +K_k(y_k-\vec{x}_k^\mathrm{T}\theta_{k-1}) 
\end{aligned}$$
若考虑遗忘因子，式(2.2)改为
$$
    K_k = \frac{P_{k-1}\vec{x}_k}
    {\lambda+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k}
$$
其中$\lambda\in(0,1]$为遗忘因子。

# 带约束的最小二乘参数辨识

# 仿真

# 参考
- 吴宏鑫, 全系数自适应控制理论及其应用, 国防工业出版社, 1990

这本书详细推导了各种离散化方法下全系数之和等于1的过程，包括欧拉法和带传输延迟的零阶保持器方法等，主要针对线性系统。
- 吴宏鑫, 胡军, 解永春, 基于特征模型的智能自适应控制, 中国科学技术出版社, 2009

