
# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)  

@[TOC](目录)
&emsp;&emsp;(摘要)simucpp的第一部分例程解读，包含各种类型的常微分方程(组)、连续/离散传递函数和简单滤波器等。主要使用单元模块以及由单元模块构成的组合模块，均使用单线连接。  
&emsp;&emsp;部分图片未及时更新，保留了以前版本使用 gdi+ 的作图图片，目前使用的是 matplotlib。
# demo1 一阶系统
$$F(s)=\frac{1}{s+0.5}$$
$y(t)=2-2\text{e}^{-0.5t}$，$y(10)=1.986524106001829$
# demo2 多个单元模块
积分器、单位延迟模块、传输延迟模块、零阶保持器效果展示。
# demo3 mathieu方程
$$\frac{d^2u}{d\epsilon^2}+(a-2q\cos 2\epsilon)u=0$$
$y(0)=1$,$y'(0)=0$,$y(15)=5.8219712843$
# demo4 将文件中的数据加载到输入端口并直连到输出端口显示
首先生成一个包含数据的文件，一行一个点。下面是python生成数据和读取数据的示例代码。
```python
import numpy as np
f = open("sinewave", 'w')
for n in range(100):
    print(np.sin(0.1*n), file=f)
f.close()
```
```python
import numpy as np
with open('sinewave', 'r+') as r:
    data = []
    for n in range(100):
        text = r.readline()
        data.append(float(text))
    data = np.array(data)
```
注意要将生成的数据文件放到生成的exe文件的同级目录下。
![在这里插入图片描述](https://img-blog.csdnimg.cn/d37fe473a72d4c3c99f1b9a6f4a75238.png#pic_center =50%x50%)
# demo5 单摆
$$ml\theta''=-mg\sin\theta-kl\theta' \\
x''=-\frac{k}{m}x'-\frac{g}{l}\sin x$$
![在这里插入图片描述](https://img-blog.csdnimg.cn/22de5a89ef6748d6b25d4e5f1d1d53ca.png#pic_center =50%x50%)
# demo6 简谐振动与共振
例题出自《高等数学》同济版第七版的常系数非齐次线性微分方程。
$$\frac{d^2x}{dt^2}+2n\frac{dx}{dt}+K^2x=h\sin pt \\
x''+5x=0.5\sin 2.2t$$
通解
$$x=A\sin(kt+\phi)+\frac{h}{k^2-p^2}\sin pt$$
对该问题$x(0)=0$,$x'(0)=1$的特解
$$x=\frac{25}{8}\sin 2.2t-\frac{47}{40}\sqrt{5}\sin\sqrt{5}t$$
$x(150)=-2.185724668757026$，误差约$1.5\times 10^{-10}$
```python
import numpy as np
x=150
(25/8)*np.sin(2.2*x)-(47/40*np.sqrt(5))*np.sin(np.sqrt(5)*x)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb086140f0b644f0940005de0f1c6ab0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8e5adb8b60af43e0a823ad5b640b694a.png#pic_center =50%x50%)
# demo7 传递函数
$$F(s)=\frac{s+2}{s^2+2s+3}$$
# demo8 线性微分方程组
例题出自方保镕《矩阵论》第二版的若尔当标准形。
$$\begin{cases}
    \frac{\text{d}y_1}{\text{d}x}=-y_1+y_2 \\
    \frac{\text{d}y_2}{\text{d}x}=-4y_1+3y_2 \\
    \frac{\text{d}y_3}{\text{d}x}=y_1+2y_3
\end{cases}$$
$$\begin{cases}
y_1=\text{e}^{x}(k_3x+k_2) \\
y_2=\text{e}^{x}(2k_3x+2k_2+k_3) \\
y_3=k_1\text{e}^{2x}-\text{e}^{x}(k_3x+k_2+k_3)
\end{cases}$$
在初始条件$y_1(0)=y_2(0)=y_3(0)=1$下，$y_1(1)=0,y_2(1)=-e$。仿真结果$y_1(1)=9.89828878584\times10^{-14}$，$y_2(1)=-2.71828182846$。
# demo9 一阶非齐次线性微分方程
微分方程$y'+\frac{2}{x+1}y=\sin x$的通解为
$$y=\frac{1}{(x+1)^2}[(2x+2)\sin x-(x^2+2x-1)\cos x+C]$$
$y(0)=1$的初始条件下$C=0$
```python
import numpy as np
x=15
((2*x+2)*np.sin(x)-(x*x+2*x-1)*np.cos(x))/(x+1)**2
```
$y(15)=0.835038831059$，步长$2^{-10}$下仿真15秒误差$<10^{-12}$。
# demo10 零极点对消
$$G_1(s)=\frac{s-3}{s^2+2s},\ G_2(s)=\frac{1}{s-3}$$
上图为仿真10秒的结果，下图为仿真20秒的结果，左图为被控对象输出，右图为被控对象输入。
![在这里插入图片描述](https://img-blog.csdnimg.cn/3fef45c58f85421487c9d64f2edc39ad.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b0cf7abb71e64f71a8e4f25012c1042f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_20,color_FFFFFF,t_70,g_se,x_16)
# demo11 时滞系统
例题出自胡寿松《自动控制原理》第六版例5-10。开环传递函数
$$G(s)H(s)=\frac{2\text{e}^{-\tau s}}{s+1}$$
延迟时间$\tau<\frac{2}{3\sqrt{3}}\pi$时闭环稳定。$\tau=\frac{2}{3\sqrt{3}}\pi-0.01$时仿真15秒见下图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/e3af41383dbd4c20a676e97451b2b180.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
延迟时间$\tau=15$时仿真100秒见下图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a414064cc03547638479659ba83840a1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
图中展示了两条曲线，分别是惯性环节和延迟环节一前一后和一后一前的情况，100秒后的输出值分别为$-37.7005907666$和$-37.7025631374$，不知道这误差算不算大。结果基本相同所以第二条曲线覆盖了第一条。
# demo12 定积分
$$\int_{-1}^3(x+1)^2\text{d}x=\frac{64}{3}$$
令
$$y(x)=\int_{-1}^x(t+1)^2\text{d}t$$
则
$$y'(x)=(x+1)^2$$
将输入函数设置为$(x+1)^2$，然后将仿真器的起止时间分别设置为积分上下限，然后取积分器的输出即可。
# demo13 离散低通滤波器
$$D(z)=0.008125\frac{1+2z^{-1}+z^{-2}}{1-1.7z^{-1}+0.7325z^{-2}}$$
![在这里插入图片描述](https://img-blog.csdnimg.cn/bff8c0c1e5a4477ca92bb55bd969dd3f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center =50%x50%)
# demo14 离散传递函数与单步仿真
![在这里插入图片描述](https://img-blog.csdnimg.cn/3e3fb18d038246f3a2ba9c8d783c45e3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center)
例题出自奥本海姆《离散时间信号处理》第三版例6.4。其中$a_1=0.75,a_2=-0.125,b_0=1,b_1=2,b_2=1$。
$$H(z)=\frac{b_0+b_1z^{-1}+b_2z^{-2}}{1-a_1z^{-1}-a_2z^{-2}}$$
下面是输出各个时刻的值的python代码。
```python
x0 = 0
x1 = 0
x2 = 0
for n in range(11):
    x0 = 1 + 0.75*x1 - 0.125*x2
    y = x0 + 2*x1 + x2
    x2 = x1
    x1 = x0
    print(y)
```
下面是cpp中的输出结果。需要注意的是虽然只仿真了10秒，但是内部实际上生成了10001个采样点(假设采样周期为1ms)。
```
0, 1, 1
1, 1001, 3.75
2, 2001, 6.6875
3, 3001, 8.546875
4, 4001, 9.57421875
5, 5001, 10.1123046875
6, 6001, 10.3874511719
7, 7001, 10.526550293
8, 8001, 10.5964813232
9, 9001, 10.6315422058
10, 10001, 10.649096489
```
# demo15 代数环
每个环路（如果有的话）都必须包含至少一个积分器或单位延迟模块，否则就会报代数环错误。代数环的进一步说明见 [代数环简介与代数环检测算法](https://blog.csdn.net/qq_34288751/article/details/122648967)。
该例子包含两个仿真，一个复杂一个简单，可以将两个仿真分别修改成包含和不包含代数环的形式。
