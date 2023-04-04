例题出自郑大钟《线性系统理论》第二版课后题6.20。课本上只会堆公式并没有解释为什么要这样做，我结合这个例子直观解释一下降维状态观测器的设计过程和原因。

$$
\begin{array}{l}
\dot{\boldsymbol{x}}=\left[\begin{array}{rrr}
-1 & -2 & -2 \\
0 & -1 & 1 \\
1 & 0 & -1
\end{array}\right] \boldsymbol{x}+\left[\begin{array}{l}
2 \\ 0 \\ 1
\end{array}\right] u \\
y=\left[\begin{array}{lll}
1 & 1 & 0
\end{array}\right] \boldsymbol{x}
\end{array}
$$

降维状态观测器的目的是，假如有个3阶系统，包含3个状态，1个输出$y$，那么让$y$直接输出一个状态，剩下的状态进行重构，也就是设计一个2阶参考模型，通过误差反馈的方式使参考模型中的两个状态与原系统中其余的两个状态相等。
但在上面这个问题中，$y=x_1+x_2$，并没有输出单个状态，因此想办法设计一个线性变换，比如令$\overline{x}_1=x_1+x_2$，然后按照这个思路直接设计一个线性变换矩阵$P$，设计方法是令

$$P=\left[\begin{array}{r}
C \\ R
\end{array}\right]$$

其中 $C=\left[\begin{matrix}1 & 1 & 0\end{matrix}\right]$，R任意取只要$P$是$3\times 3$矩阵且非奇异就行(以上面的题为例，其他的方法类似)。这样构造线性变换使 $\boldsymbol{\overline{x}}=P\boldsymbol{x}$，可以推出线性变换后的状态空间方程

$$\begin{array}{l}
\dot{\overline{\boldsymbol{x}}}=PAP^{-1}\boldsymbol{x}+PBu \\
y=CP^{-1}\overline{\boldsymbol{x}}
\end{array}$$

这样变换后，输出方程就变成了 $y=\left[\begin{matrix}
1 & 0 & 0
\end{matrix}\right]\overline{\boldsymbol{x}}$。另外举个例子，如果原本的输出方程为

$$\boldsymbol{y}=\left[\begin{matrix}
4 & 3 & 2 & 1 \\
8& 7 & 6 & 5 \\
\end{matrix}\right]{\boldsymbol{x}}$$

线性变换后输出方程变成

$$\boldsymbol{y}=\left[\begin{matrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0
\end{matrix}\right]\overline{\boldsymbol{x}}$$

也就是说，有几个输出端口就让这几个输出端口分别输出 $\boldsymbol{\overline{x}}$ 的前几个状态。为什么这样可以的证明见课本，不多解释了。设

$$
P=\left[\begin{matrix}
1 & 1 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{matrix}\right]
$$

则可计算出

$$\begin{array}{l}
\left[\begin{matrix}
\overline{x}_1' \\ \hdashline\overline{x}_2' \\ \overline{x}_3'
\end{matrix}\right]=\left[\begin{array}{r:rr}
-1 & -2 & -1 \\
\hdashline 0 & -1 & 1 \\
1 & -1 & -1
\end{array}\right]\left[\begin{matrix}
\overline{x}_1 \\ \hdashline\overline{x}_2 \\ \overline{x}_3
\end{matrix}\right]+\left[\begin{array}{r}
2 \\ \hdashline 0 \\ 1
\end{array}\right]u \\
y=\left[\begin{matrix}
1 & 0 & 0
\end{matrix}\right]\left[\begin{matrix}
\overline{x}_1 \\ \hdashline\overline{x}_2 \\ \overline{x}_3
\end{matrix}\right]
\end{array}$$

分别用向量表示，可直接输出向量$\boldsymbol{x}_a=\overline{x}_1$，待重构向量$\boldsymbol{x}_b=[\overline{x}_1,\overline{x}_2]^{\text{T}}$，输出$y=\boldsymbol{x}_a$。
从式中可明显看出$\overline{x}_1$是直接连到输出端口的，$\overline{x}_2'$，$\overline{x}_3'$是待重构的状态。把上面式子拆开，并忽略输出方程(没什么用了)
$$\begin{array}{l}
\left[\begin{matrix}
\overline{x}_2' \\ \overline{x}_3'
\end{matrix}\right]=\left[\begin{array}{rr}
-1 & 1 \\
-1 & -1
\end{array}\right]\left[\begin{matrix}
\overline{x}_2 \\ \overline{x}_3
\end{matrix}\right]+\left[\begin{array}{r}
0 & 0 \\ 1 & 1
\end{array}\right]\left[\begin{matrix}
\overline{x}_1 \\ u
\end{matrix}\right] \\
\overline{x}_1'+\overline{x}_1-2u=\left[\begin{matrix}
-2 & -1
\end{matrix}\right]\left[\begin{matrix}
\overline{x}_2 \\ \overline{x}_3
\end{matrix}\right]
\end{array}$$
可以看出这两个式子具有与状态方程和输出方程类似的形式。添加反馈并写成与全维观测器类似的形式如下
$$
\left[\begin{matrix}
\hat{\overline{x}}_2' \\ \hat{\overline{x}}_3'
\end{matrix}\right]=\left[\begin{array}{rr}
-1 & 1 \\
-1 & -1
\end{array}\right]\left[\begin{matrix}
\hat{\overline{x}}_2 \\ \hat{\overline{x}}_3
\end{matrix}\right]+\left[\begin{array}{r}
0 & 0 \\ 1 & 1
\end{array}\right]\left[\begin{matrix}
\overline{x}_1 \\ u
\end{matrix}\right]+K\left((\overline{x}_1'+\overline{x}_1-2u)-
\left[\begin{matrix}
-2 & -1
\end{matrix}\right]\left[\begin{matrix}
\hat{\overline{x}}_2 \\ \hat{\overline{x}}_3
\end{matrix}\right]\right)
$$
上式用符号表示
$$
\boldsymbol{x}_b'
=(\overline{A}_{22}-K\overline{A}_{12})\boldsymbol{x}_b
+\overline{A}_{21}y+\overline{B}_2u
+K(y'-\overline{A}_{11}y-\overline{B}_1u)
$$
然后就可以根据给定的观测器极点计算$K=[k_1,k_2]^{\text{T}}$了。设要求的极点为$-3$，$-4$，计算得到$K=[-1,-3]^{\text{T}}$。但此时新的$y=\overline{x}_1'+\overline{x}_1-2u$因为包含$\overline{x}_1'$而无法在实际中实现，因此另外定义
$$\boldsymbol{z}=\boldsymbol{x}_b-Ky$$
整理得到
$$\begin{aligned}
\boldsymbol{z}'=&\boldsymbol{x}_b'-Ky' \\
=&(\overline{A}_{22}-K\overline{A}_{12})(\boldsymbol{x}_b-Ky)
+(\overline{A}_{22}-K\overline{A}_{12})Ky+(\overline{B}_2-K\overline{B}_1)u
+(\overline{A}_{21}-K\overline{A}_{11})y \\
=&(\overline{A}_{22}-K\overline{A}_{12})z
+(\overline{A}_{22}K-K\overline{A}_{12}K+\overline{A}_{21}-K\overline{A}_{11})y+(\overline{B}_2-K\overline{B}_1)u
\end{aligned}$$
代入数字得到
$$
\left[\begin{matrix}
z_1' \\z_2'
\end{matrix}\right]
=\left[\begin{matrix}
-3 & 0 \\ -7 & -4
\end{matrix}\right]\left[\begin{matrix}
z_1 \\ z_2
\end{matrix}\right]
+\left[\begin{matrix}
2 \\ 17
\end{matrix}\right]y
+\left[\begin{matrix}
2 \\ 7
\end{matrix}\right]u
$$
根据$\overline{x}_1$、$z_1$、$z_2$重构出所有状态$\hat{\boldsymbol{x}}$
$$
\hat{\boldsymbol{x}}=P^{-1}
\left[\begin{matrix}
\boldsymbol{x}_a \\ \boldsymbol{x}_b
\end{matrix}\right]
=\left[\begin{matrix}
1 & -1 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{matrix}\right]
\left[\begin{matrix}
y \\ z_1-y \\ z_2-3y
\end{matrix}\right]
=\left[\begin{matrix}
2 & -1 & 0 \\
-1 & 1 & 0 \\
-3 & 0 & 1
\end{matrix}\right]
\left[\begin{matrix}
y \\ z_1 \\ z_2
\end{matrix}\right]
$$
总体框图如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/964b61cda64d470d8f43c925d41734eb.png#pic_center)
下面以 [simucpp](https://blog.csdn.net/qq_34288751/category_11453352.html) 为例设计仿真过程。原系统的阶跃响应如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/f35c2471265d46758946d3a2bcf20709.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_11,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
将原系统封装成组合模块，输入为$u$，输出的第0个端口为$y$，第1到3个端口分别为$x_1$至$x_3$。直接拆开写成积分器的形式太过复杂，可以使用状态空间组合模块`StateTransFcn`，但是输出有4个，C矩阵的最后三行为3阶单位阵。定义A、B、C矩阵和原系统
```cpp
    Mat A(3, 3, vecdble{-1, -2, -2, 0, -1, 1, 1, 0, -1});
    Mat B(3, 1, vecdble{2, 0, 1});
    Mat C1(1, 3, vecdble{1, 1, 0});
    Mat C2 = eye(3);
    Mat C = VConcat(C1, C2);
    auto Model = new StateTransFcn(&sim1, A, B, C);  // 原系统
```
定义降维状态观测器
```cpp
    A = Mat(2, 2, vecdble{-3, 0, -7, -4});
    B = Mat(2, 2, vecdble{2, 2, 17, 7});
    C = eye(2);
    auto Observer = new StateTransFcn(&sim1, A, B, C);  // 降维状态观测器
```
降维状态观测器的输入为$[y,u]^{\text{T}}$，输出为$[z_1,z_2]^{\text{T}}$。
定义状态重构器
```cpp
    A = Mat(3, 3, vecdble{2, -1, 0, -1, 1, 0, -3, 0, 1});
    auto Restructer = new MGain(&sim1, A);  // 状态重构器
```
原系统和降维状态观测器为组合模块，状态重构器为矩阵模块，输入输出均使用总线连接，因此需要加入复用/解复用模块。
为了展示状态跟踪效果，将原系统的初始状态全部设置为1
```cpp
    Model->Set_InitialValue(vecdble{1, 1, 1});  // 将原系统的初始状态全部设置为1
```
状态$x_1$的跟踪效果如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/c72c10724743493bbc5f05b89f3ec3a8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_11,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
稍微计算一下$x_1$在$t=0$时的预测值$\hat{x}_1(0)=2y(0)-z_1(0)=2(x_1(0)+x_2(0))=4$。另外也可得到$\hat{x}_1+\hat{x}_2=2y-z_1-y+z_1=y$，也就是说
$$\hat{x}_1+\hat{x}_2\equiv x_1+x_2$$
这也是降维状态观测器中"降维"的意义。
完整的 [simucpp](https://blog.csdn.net/qq_34288751/category_11453352.html) 代码如下
```cpp
#include <cmath>
#include "simucpp.hpp"
using namespace simucpp;
using namespace zhnmat;
using namespace std;
int main()
{
    Simulator sim1(10);
    FUInput(uin1, &sim1);  // 输入信号u
    Mat A(3, 3, vecdble{-1, -2, -2, 0, -1, 1, 1, 0, -1});
    Mat B(3, 1, vecdble{2, 0, 1});
    Mat C1(1, 3, vecdble{1, 1, 0});
    Mat C2 = eye(3);
    Mat C = VConcat(C1, C2);
    auto Model = new StateTransFcn(&sim1, A, B, C, true, "stfmdl");  // 原系统
    A = Mat(2, 2, vecdble{-3, 0, -7, -4});
    B = Mat(2, 2, vecdble{2, 2, 17, 7});
    C = eye(2);
    auto Observer = new StateTransFcn(&sim1, A, B, C, true, "stfobserver");  // 降维状态观测器
    A = Mat(3, 3, vecdble{2, -1, 0, -1, 1, 0, -3, 0, 1});
    auto Restructer = new MGain(&sim1, A, true, "mgnrestructer");  // 状态重构器
    auto mxModel = new Mux(&sim1, BusSize(1, 1), "mxModel");  // 原系统的输入复用器
    auto dmxModel = new DeMux(&sim1, BusSize(4, 1), "dmxModel");  // 原系统的输出解复用器
    auto mxObserver = new Mux(&sim1, BusSize(2, 1), "mxObserver");  // 状态观测器的输入复用器
    auto dmxObserver = new DeMux(&sim1, BusSize(2, 1), "dmxObserver");  // 状态观测器的输出解复用器
    auto mxRestructer = new Mux(&sim1, BusSize(3, 1), "mxRestructer");  // 状态重构器的输入复用器
    auto dmxRestructer = new DeMux(&sim1, BusSize(3, 1), "dmxRestructer");  // 状态重构器的输出解复用器

    sim1.connectU(uin1, mxModel, BusSize(0, 0));  // 输入信号u单线接入原系统的输入复用器
    sim1.connectM(mxModel, Model, 0);  // 原系统的输入复用器以总线接入原系统
    sim1.connectM(Model, 0, dmxModel);  // 原系统通过输出解复用器引出单线
    sim1.connectU(dmxModel, BusSize(0, 0), mxObserver, BusSize(0, 0));  // 连接原系统的输出y与状态观测器的第1个输入端口
    sim1.connectU(uin1, mxObserver, BusSize(1, 0));  // 连接输入信号u与状态观测器的第1个输入端口
    sim1.connectM(mxObserver, Observer, 0);  // 状态观测器的输入复用器以总线接入状态观测器
    sim1.connectM(Observer, 0, dmxObserver);  // 状态观测器通过输出解复用器引出两个状态接入状态重构器
    sim1.connectU(dmxModel, BusSize(0, 0), mxRestructer, BusSize(0, 0));  // 连接原系统的输出y与状态重构器的第1个输入端口
    sim1.connectU(dmxObserver, BusSize(0, 0), mxRestructer, BusSize(1, 0));  // 连接状态观测器的输出z1与状态重构器的第2个输入端口
    sim1.connectU(dmxObserver, BusSize(1, 0), mxRestructer, BusSize(2, 0));  // 连接状态观测器的输出z2与状态重构器的第3个输入端口
    sim1.connectM(mxRestructer, Restructer);  // 连接状态重构器及其输入复用器
    sim1.connectM(Restructer, dmxRestructer);  // 状态重构器通过输出解复用器引出所有状态
    // FMOutput(my, &sim1);  // 原系统输出y
    // FMOutput(myp, &sim1);  // 预测输出yp
    FUOutput(ux1, &sim1);  // 原系统输出x1
    FUOutput(ux1p, &sim1);  // 预测输出x1p
    // FMOutput(mx2, &sim1);  // 原系统输出x2
    // FMOutput(mx2p, &sim1);  // 预测输出x2p
    // FMOutput(mx3, &sim1);  // 原系统输出x3
    // FMOutput(mx3p, &sim1);  // 预测输出x3p
    sim1.connectU(dmxModel, BusSize(1, 0), ux1);
    sim1.connectU(dmxRestructer, BusSize(0, 0), ux1p);
    uin1->Set_Function([](double t){return sin(t)+1;});  // 设置输入信号u的函数
    Model->Set_InitialValue(vecdble{1, 1, 1});  // 将原系统的初始状态全部设置为1
    sim1.Initialize();
    sim1.Simulate();
    sim1.Plot();
    return 0;
}
```
原系统阶跃响应的 [simucpp](https://blog.csdn.net/qq_34288751/category_11453352.html) 代码
```cpp
# include "simucpp.hpp"
using namespace simucpp;
using namespace zhnmat;
using namespace std;
int main()
{
    Mat A(3, 3, vecdble{-1, -2, -2, 0, -1, 1, 1, 0, -1});
    Mat B(3, 1, vecdble{2, 0, 1});
    Mat C(1, 3, vecdble{1, 1, 0});
    Simulator sim1(10);
    FUInput(min1, &sim1);
    FUOutput(mout1, &sim1);
    Mux *mxin1 = new Mux(&sim1, BusSize(1, 1));
    DeMux *dmxout1 = new DeMux(&sim1, BusSize(1, 1));
    auto *stf1 = new StateTransFcn(&sim1, A, B, C);
    sim1.connectU(min1, mxin1, BusSize(0, 0));
    sim1.connectM(mxin1, stf1, 0);
    sim1.connectM(stf1, 0, dmxout1);
    sim1.connectU(dmxout1, BusSize(0, 0), mout1);
    sim1.Initialize();
    sim1.Simulate();
    sim1.Plot();
    return 0;
}
```

