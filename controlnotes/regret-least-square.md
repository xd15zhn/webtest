#! https://zhuanlan.zhihu.com/p/626322246
带遗忘因子的递推最小二乘法推导
摘要：最小二乘法的递推形式、直流信号的遗忘递推形式、遗忘递推最小二乘。

# 递推最小二乘法
对多组数据 $\vec{x}_i$ 和 $y_i$，满足
$$y_i = \vec{x}^\mathrm{T}_i\vec{\theta}$$
其中 $\vec{x}_i$ 是输入数据向量，$y_i$ 是输出数据标量。写成矩阵形式
$$\vec{y} = X\vec{\theta}$$
其中（以3组数据、2个未知参数为例）

$$
\vec{y} = \begin{bmatrix}
    y_1 \\ y_2 \\ y_3
\end{bmatrix},
X = \begin{bmatrix}
    \vec{x}^\mathrm{T}_1 \\
    \vec{x}^\mathrm{T}_2 \\
    \vec{x}^\mathrm{T}_3
\end{bmatrix},
\theta = \begin{bmatrix}
    \theta_1 \\ \theta_2
\end{bmatrix},
y_i=x_{i1}\theta_1+x_{i2}\theta_2
$$

完整地写作（以3组数据、2个未知参数为例）

$$
\begin{bmatrix}
    y_1 \\ y_2 \\ y_3
\end{bmatrix}
=\begin{bmatrix}
    x_{11} & x_{12} \\
    x_{21} & x_{22} \\
    x_{31} & x_{32} \\
\end{bmatrix}
\begin{bmatrix}
    \theta_1 \\ \theta_2
\end{bmatrix}
$$

有$k$组数据时记作

$$
    X^\mathrm{T}_k = \begin{bmatrix}
        \vec{x}_1 & \vec{x}_2 & \cdots & \vec{x}_k
    \end{bmatrix} \\
    \vec{y}_k=\begin{bmatrix} y_1 & y_2 & \cdots & y_k\end{bmatrix}
$$

(此时注意区分一些符号，例如 $\vec{y}_k$ 与 $y_k$，$X$ 与 $\vec{x}$ 等)  
已知最小二乘法的解为

$$
    \vec{\theta}
    =(X^\mathrm{T}X)^{-1}
    X^\mathrm{T}\vec{y} \tag{1}
$$

令 $P = (X^\mathrm{T}X)^{-1}$，
则加上递推后

$$\begin{aligned}
    P_k^{-1} =& X_k^\mathrm{T}X_k
    = \sum_{i=1}^k\vec{x}_i\vec{x}^\mathrm{T}_i\\
    =& \sum_{i=1}^{k-1}\vec{x}_i\vec{x}^\mathrm{T}_i
    +\vec{x}_k\vec{x}^\mathrm{T}_k \\
    =& P_{k-1}^{-1} + \vec{x}_k\vec{x}_k^\mathrm{T}
\end{aligned} \tag{2}$$

同理可得

$$
    X_k^\mathrm{T}\vec{y}_k
    =X_{k-1}^\mathrm{T}\vec{y}_{k-1}
    +\vec{x}_ky_k \tag{3}
$$

于是代入式(1)得到

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

其中倒数第3行到倒数第一行的过程

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
    P_{k-1}\vec{x}_k}\\
    P_k =& (I-K_k\vec{x}_k^\mathrm{T})P_{k-1} \\
    \vec{\theta}_k =& \vec{\theta}_{k-1}
    +K_k(y_k-\vec{x}_k^\mathrm{T}\theta_{k-1})
\end{aligned}$$

&emsp;&emsp;因为 $P=(X^\text{T}X)^{-1}$，$\vec{x}_1\vec{x}_1^\text{T}$ 一般很小，所以实际使用时一般把 $P_1$ 初始化为一个接近无穷大的单位阵。

# 引入遗忘因子
先考虑一个比较简单的情况

$$x_n=\theta+w_n$$

其中 $\theta$ 是要估计的参数，$w_n$ 是高斯白噪声，$x_n$ 是观测到的数据，总共有 $N$ 组数据，将 $N$ 组数据取平均得到 $\theta$ 的一个估计值为

$$\hat{\theta}=\frac{1}{N}\sum_{n=1}^Nx_n$$

写成递推形式为

$$\hat\theta_n=\hat\theta_{n-1}+\frac{1}{n}(x_n-\hat\theta_{n-1})$$

例如，

$$\begin{aligned}
    \hat\theta_1 =& x_1\\
    \hat\theta_2 =& \hat\theta_1+\frac{1}{2}(x_2-\hat\theta_1)
    =\frac{1}{2}(x_1+x_2) \\
    \hat\theta_3 =& \hat\theta_2+\frac{1}{3}(x_3-\hat\theta_2)
    =\frac{1}{3}(x_1+x_2+x_3) \\
\end{aligned}$$

现在对之前的值乘一个衰减系数 $\lambda\in(0,1)$，也就是

$$\begin{aligned}
    \hat\theta_2 =& \frac{1}{\lambda+1}(\lambda x_1+x_2) \\
    \hat\theta_3 =& \frac{1}{\lambda^2+\lambda+1}(\lambda^2x_1+\lambda x_2+x_3) \\
    \vdots
\end{aligned}$$

写成递推形式为

$$\begin{aligned}
    \hat\theta_2=&\frac{1}{\lambda+1}(\lambda x_1+x_2) \\
    \hat\theta_3=&\frac{1}{\lambda^2+\lambda+1}(\lambda(\lambda+1)\hat\theta_2+x_3) \\
    =&\hat\theta_2+\frac{1}{\lambda^2+\lambda+1}(x_3-\hat\theta_2)
\end{aligned}$$

当 $n\rightarrow\infty$ 时，递推形式可以近似为

$$\begin{aligned}
    \hat{\theta}_n =& \hat\theta_{n-1}+\frac{1}{\sum_{n=1}^N\lambda^{n}}(x_n-\hat\theta_{n-1}) \\
    =& \hat\theta_{n-1}+(1-\lambda)(x_n-\hat\theta_{n-1})
\end{aligned}$$

# 递推最小二乘中的遗忘因子
类似地，在最小二乘的误差 $S_i=||y_i-\vec{x}_i^\text{T}\vec\theta||^2$ 中加入遗忘因子，得到总误差（以3组数据为例）

$$ J_3=\lambda^2S_1+\lambda S_2+S_3 $$

完整的方程可以写作

$$
\vec{y}_3=X_3\theta \\
\begin{bmatrix}
    \lambda y_1 \\ \sqrt{\lambda}y_2 \\ y_3
\end{bmatrix}
=\begin{bmatrix}
    \lambda x_{11} & \lambda x_{12} \\
    \sqrt{\lambda}x_{21} & \sqrt{\lambda}x_{22} \\
    x_{31} & x_{32} \\
\end{bmatrix}
\begin{bmatrix}
    \theta_1 \\ \theta_2
\end{bmatrix}
$$

类似地代入最小二乘的解式(1)，并求式(2)(3)的递推形式

$$\begin{aligned}
X_2^\text{T}X_2 =& \begin{bmatrix}
\sqrt{\lambda}x_{11} & x_{21} \\
\sqrt{\lambda}x_{12} & x_{22} \\
\end{bmatrix}
\begin{bmatrix}
\sqrt{\lambda}x_{11} & \sqrt{\lambda} x_{12} \\
x_{21} & x_{22}
\end{bmatrix} \\
X_3^\text{T}X_3 =& \begin{bmatrix}
\lambda x_{11} & \sqrt{\lambda}x_{21} & x_{31} \\
\lambda x_{12} & \sqrt{\lambda}x_{22} & x_{32} \\
\end{bmatrix}
\begin{bmatrix}
\lambda x_{11} & \lambda x_{12} \\
\sqrt{\lambda}x_{21} & \sqrt{\lambda} x_{22} \\
x_{31} & x_{32}
\end{bmatrix} \\
=& \lambda X_2^\text{T}X_2 + \vec{x}_3\vec{x}_3^\text{T} \\
X_2^\text{T}\vec{y}_2 =& \begin{bmatrix}
\sqrt{\lambda}x_{11} & x_{21} \\
\sqrt{\lambda}x_{12} & x_{22} \\
\end{bmatrix}
\begin{bmatrix}
\sqrt{\lambda}y_1 \\ y_2
\end{bmatrix} \\
X_3^\text{T}\vec{y}_3 =& \begin{bmatrix}
\lambda x_{11} & \sqrt{\lambda}x_{21} & x_{31} \\
\lambda x_{12} & \sqrt{\lambda}x_{22} & x_{32} \\
\end{bmatrix}
\begin{bmatrix}
\lambda y_1 \\ \sqrt{\lambda}y_2 \\ y_3
\end{bmatrix} \\
=& \lambda X_2^\text{T}\vec{y}_2 + \vec{x}_3y_3
\end{aligned}$$

即

$$\begin{aligned}
P_k^{-1} =& \lambda P_{k-1}^{-1} + \vec{x}_k\vec{x}_k^\text{T} \\
X_k^\text{T}\vec{y}_k =& \lambda X_{k-1}^\text{T}\vec{y}_{k-1} + \vec{x}_ky_k
\end{aligned}$$

代入式(1)得到

$$\begin{aligned}
\vec{\theta}_k =& P_kX_k^\text{T}\vec{y}_k \\
=& P_k(\lambda X_{k-1}^\text{T}\vec{y}_{k-1} + \vec{x}_ky_k) \\
=& P_k(\lambda P_{k-1}^{-1}\vec{\theta}_{k-1} + \vec{x}_ky_k) \\
=& P_k(P_k^{-1}\vec{\theta}_{k-1}
-\vec{x}_k\vec{x}_k^\text{T}\theta_{k-1}+\vec{x}_ky_k) \\
=& \vec\theta_{k-1} + P_k\vec{x}_k(y_k-\vec{x}_k^\text{T}\theta_{k-1}) \\
=& \vec\theta_{k-1} + (\lambda P_{k-1}^{-1} + \vec{x}_k\vec{x}_k^\text{T})^{-1}
\vec{x}_k(y_k-\vec{x}_k^\text{T}\theta_{k-1}) \\
=& \vec\theta_{k-1}+(\frac{P_{k-1}}{\lambda}
-\frac{\frac{P_{k-1}}{\lambda}\vec{x}_k\vec{x}_k^\text{T}
\frac{P_{k-1}}{\lambda}}{1+\vec{x}_k^\text{T}\frac{P_{k-1}}{\lambda}\vec{x}_k})
\vec{x}_k(y_k-\vec{x}_k^\text{T}\theta_{k-1}) \\
=& \vec\theta_{k-1}+(\frac{P_{k-1}}{\lambda}
-\frac{1}{\lambda}\frac{P_{k-1}\vec{x}_k\vec{x}_k^\text{T}P_{k-1}}
{\lambda + \vec{x}_k^\text{T}P_{k-1}\vec{x}_k})
\vec{x}_k(y_k-\vec{x}_k^\text{T}\theta_{k-1}) \\
\end{aligned}$$

令

$$
K_k = \frac{P_{k-1}\vec{x}_k}
{\lambda+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k}
$$

则

$$\begin{aligned}
P_k =& \frac{1}{\lambda}(I-K_k\vec{x}_k^\mathrm{T})P_{k-1}\\
P_k\vec{x}_k =& \frac{1}{\lambda}(P_{k-1}\vec{x}_k
-K_k\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k) \\
=& \frac{1}{\lambda}\frac{P_{k-1}\vec{x}_k
(\lambda+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k)
-P_{k-1}\vec{x}_k\vec{x}_k^\mathrm{T}
P_{k-1}\vec{x}_k}
{\lambda+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k} \\
=& \frac{1}{\lambda}\frac{P_{k-1}\vec{x}_k\lambda}
{\lambda+\vec{x}_k^\mathrm{T}P_{k-1}\vec{x}_k} \\
=& K_k \\
\end{aligned}$$

总结成递推公式得到

$$\begin{aligned}
K_k =& \frac{P_{k-1}\vec{x}_k}{\lambda+\vec{x}_k^\mathrm{T}
P_{k-1}\vec{x}_k}\\
P_k =& \frac{1}{\lambda}(I-K_k\vec{x}_k^\mathrm{T})P_{k-1} \\
\vec{\theta}_k =& \vec{\theta}_{k-1}
+K_k(y_k-\vec{x}_k^\mathrm{T}\theta_{k-1})
\end{aligned}$$

与不带遗忘因子的公式相比，第3个式子 $\vec{\theta}_k$ 不变，前两个稍微有变化。

# 一个例子与代码
&emsp;&emsp;对方程 $y=ax^2+bx+c$，输入多组数据，$x$ 取一些高斯噪声，以4组数据为例，

$$
\begin{bmatrix}
    y_1 \\ y_2 \\ y_3 \\ y_4
\end{bmatrix}
=\begin{bmatrix}
    x_1^2 & x_1 & 1 \\
    x_2^2 & x_2 & 1 \\
    x_3^2 & x_3 & 1 \\
    x_4^2 & x_4 & 1 \\
\end{bmatrix}
\begin{bmatrix}
    a \\ b \\ c
\end{bmatrix}
$$

```cpp
#include <iostream>
#include <cmath>
#include "zhnmat.hpp"
using namespace zhnmat;
using namespace std;
int main() {
    constexpr double a = 0.5;
    constexpr double b = 1.1;
    constexpr double c = 2.1;
    double U, V, randx;
    double y, lambda=0.5;
    Mat K;
    Mat vx(3, 1);
    Mat P = eye(3)*1e6;
    Mat theta(3, 1);
    for (int i = 0; i < 10; i++) {
        U = (rand()+1.0) / (RAND_MAX+1.0);
        V = (rand()+1.0) / (RAND_MAX+1.0);
        randx = sqrt(-2.0 * log(U))* cos(6.283185307179586477 * V);  // `randx`服从标准正态分布
        randx *= 2;
        vx.set(0, 0, randx*randx);
        vx.set(1, 0, randx);
        vx.set(2, 0, 1);
        y = a*randx*randx + b*randx + c;
        K = P * vx / (lambda + (vx.T() * P * vx).at(0, 0));
        P = (eye(3) - K * vx.T()) * P / lambda;
        theta = theta + K * (y - (vx.T() * theta).at(0, 0));
        cout << "theta" << theta << endl;
    }
    return 0;
}
```
