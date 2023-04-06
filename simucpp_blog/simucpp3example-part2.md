# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)  

@[TOC]  
&emsp;&emsp;(摘要)simucpp的第二部分例程解读，包含连续离散混合仿真、一些常用的控制器、矩阵微分方程等。引入了更复杂的矩阵模块。  

<!--########################################################-->
# demo1/2 连续离散混合仿真
&emsp;&emsp;例题出自孙增圻《计算机控制理论与应用》第二版2.3.1节。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/f8a79226cbac45afb8a4549cec94df7d.png#pic_center)  
被控对象传递函数和微分方程分别为

$$
G(s)=\frac{1}{s(10s+1)} \\
10\frac{\text{d}^2y}{\text{d}t^2}+\frac{\text{d}y}{\text{d}t}=u
$$

在每个采样周期内离散控制器$D(z)$输出的$u(t)$为常数，$G(s)$输出为$y(t)$，解得$y'(t)=u-C_1\text{e}^{-0.1t}$，$y(t)=ut+10C_1\text{e}^{-0.1t}+C_2$。若给定初值$y(0)=y_1$，$y'(0)=y_2$，则

$$
y(t)=ut+y_1+10(u-y_2)(\text{e}^{-0.1t}-1) \\
y'(t)=(1-\text{e}^{-0.1t})u+\text{e}^{-0.1t}y_2
$$

&emsp;&emsp;这两个例子使用3种方法分别计算，最后得出相同的结果但有微小误差。第一种方法是demo1中直接按上图中的结构搭建；第二种方法的思路是，仿真器初始化完成后，根据给定初始值$y_1,y_2$，在每个采样周期内运行一次仿真，一次仿真后将仿真器内部时间清零，并将当前两个积分器的初始值设置为下一次仿真的初始值，更新离散控制器之后，在下一个采样周期内开始下一次仿真；第三种方法与第二种方法类似，但在每个采样周期内求微分方程的解析解。前两种方法分别对应demo1和demo2，第三种方法的python代码在下文给出。  
&emsp;&emsp;设计连续控制器为
$$D(s)=\frac{10s+1}{s+1}$$
不同的采样周期下离散化得到不同的离散控制器，下面以$T=1$和$T=0.2$分别介绍仿真和验证的具体思路方法，具体关于控制器设计和离散化的理论分析见书上的内容。  

##$T=1$时
$T=1$时设计控制器的离散传递函数为

$$D(z)=\frac{6.64-6.008z^{-1}}{1-0.3679z^{-1}}$$

控制器的差分方程为

$$u(k)=0.3679u(k-1)+6.64e(k)-6.008e(k-1)$$

对微分方程，一个周期后

$$
y(1)=u+y_1-10(1-C)(u-y_2) \\
y'(1)=(1-C)u+Cy_2
$$

其中$C=\text{e}^{-0.1}$。

python的仿真结果为
$u(20)=0.12642514990385767$，$y(20)=0.9972632123750569$，
demo2的仿真结果为
$u(20)=0.1264251499038471$，$y(20)=0.9972632123750584$，
demo1的仿真结果为
$u(20)=0.1264251499038229$，$y(20)=0.9972632123750599$。

<!---------------------------->
<font color=#2E8B57 size=5 face="KaiTi">$T=0.2$时</font>  
$T=0.2$时，同样的方法设计控制器的离散传递函数为
$$D(z)=\frac{9.1565-8.9752z^{-1}}{1-0.8187z^{-1}}$$
控制器的差分方程为
$$u(k)=0.8187u(k-1)+9.1565e(k)-8.9752e(k-1)$$
被控对象的微分方程中
$$
y(0.2)=0.2u+y_1-10(1-C)(u-y_2) \\
y'(0.2)=(1-C)u+Cy_2
$$
其中$C=\text{e}^{-0.02}$。

python的仿真结果为
$u(5)=0.22111965473593398$，$y(5)=1.0796760390310833$，
demo2的仿真结果为
$u(5)=0.2211196547359040$，$y(5)=1.079676039031079$，
demo1的仿真结果为
$u(5)=0.2211196547359036$，$y(5)=1.079676039031079$。

<!---------------------------->
<font color=#2E8B57 size=5 face="KaiTi">python代码</font>  
python代码如下：
```python
import numpy as np
T = 1
C = np.exp(-0.1*T)
y1, y2 = 0, 0
u = 0
e, e1 = 0, 0
for n in range(21):
    e1 = e
    e = 1 - y1
    u = 0.3679*u + 6.64*e - 6.008*e1
    y1 = u*T + y1 - 10 * (1-C) * (u-y2)
    y2 = (1-C)*u + C*y2
    print(u, y1)
```
```python
import numpy as np
T = 0.2
C = np.exp(-0.1*T)
y1, y2 = 0, 0
u = 0
e, e1 = 0, 0
for n in range(26):
    e1 = e
    e = 1 - y1
    u = 0.8187*u + 9.1565*e - 8.9752*e1
    y1 = u*T + y1 - 10 * (1-C) * (u-y2)
    y2 = (1-C)*u + C*y2
    print(u, y1)
```

<!--########################################################-->
# demo3 连续离散混合仿真2
例题出自胡寿松《自动控制原理》第六版例7-26。令K=1.2。
![在这里插入图片描述](https://img-blog.csdnimg.cn/bc2db842432044a7ae71c9e5efabcea0.png#pic_center)
与demo1/2类似，
$$
y''(t)+y'(t)=Ku \\
y(t)=Kut+y_1-(Ku-y_2)(1-\text{e}^{-t}) \\
y'(t)=K(1-\text{e}^{-t})u+\text{e}^{-t}y_2
$$
```python
import numpy as np
T = 0.5
K = 1.2
C = np.exp(-T)
y1, y2 = 0, 0
for n in range(21):
    u = 1 - y1
    y1 = K*u*T + y1 - (K*u-y2)*(1-C)
    y2 = K*(1-C)*u + C*y2
    print(u, y1)
```
|$t=10$时刻的值| u(10) | y(10)|
|-|-|-|
python(T=1) | -0.5156644997261003 | -0.32340702935035215
demo3(T=1,step=$\frac{1}{1024}$) | -0.5156644997260939 | -0.3234070293503586
demo3(T=1,step=0.001) | -0.5156644997272863 | -0.323407029348769
python(T=0.5) | -0.03453280518366397  | -0.8045387238927885
demo3(T=0.5,step=0.001) | -0.03453280518450663 | -0.8045387238915487

# demo4 PID控制

# demo7 状态空间
例题出自郑大钟《线性系统理论》第二版课后题2.12。
$$
\dot{\boldsymbol{x}}=\left[\begin{matrix}
-5 & -1 \\ 3 & -1
\end{matrix}\right]\boldsymbol{x}+\left[\begin{matrix}
2 \\ 5
\end{matrix}\right]u \\
y=\left[\begin{matrix}
1 & 2
\end{matrix}\right]\boldsymbol{x}+4u
$$
等价的传递函数
$$G(s)=\frac{4s^2+36s+91}{s^2+6s+8}$$

# demo8 线性微分方程组
这是第一部分例程的线性微分方程组对应的使用矩阵模块重新搭建模型的例程。

# demo12/13 直流电机
（详细公式推导见 [中学知识推导直流电机数学模型](https://blog.csdn.net/qq_34288751/article/details/115764525)）
电枢回路电压平衡方程
$$U=L\frac{di}{dt}+Ri+E \\
E=C_e\omega
$$
电磁转矩方程
$$M_m=C_mi$$
转矩平衡方程
$$J\frac{d\omega}{dt}+f\omega=M_m-M_c$$
![在这里插入图片描述](https://img-blog.csdnimg.cn/e73a9a76484149758dab03fd0a49710e.png)
左侧sum1的输出$u_1=U-C_e\omega$，左侧传递函数表示$Li'+Ri=u_1$，输入$u_1$输出$i$，电磁转矩$M_m=C_mi$，右侧传递函数表示$J\omega'+f\omega=M$。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a748ddd255b24f6e82e893b082714387.png)
左图为电流曲线，右图为转速曲线。
![在这里插入图片描述](https://img-blog.csdnimg.cn/090ec7fd292843b7b02237709d19b4ac.png)

# demoadrc 自抗扰控制
## 跟踪微分器
左图为$x_1$，右图为$x_2$。
![在这里插入图片描述](https://img-blog.csdnimg.cn/52e0480ec999443db2b740f68de679bc.png)
## 扩张状态观测器
![在这里插入图片描述](https://img-blog.csdnimg.cn/069424237012447fbc5cf7d294d391c9.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/62aa23969d22436399f08c5cb06a1b19.png)
