# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)

@[TOC]  
模块的命名方式为，单元模块以大写字母`U`开头，矩阵模块以大写字母`M`开头，复用/解复用模块除外。

# 单元模块分类介绍
下表是一些模块所具有的特殊特点，有很多模块具有下面的多个特点，下面按特点介绍。
||||||
-|-|-|-|-
端点模块 | INTEGRATOR | OUTPUT | UNITDELAY
包含离散状态的模块 | INPUT  | NOISE | OUTPUT | ZOH
包含自定义函数的模块 | FCN | FCNMISO | INPUT
多输入单输出模块 | FCNMISO | PRODUCT | SUM
包含输入增益的模块 | OUTPUT| PRODUCT | SUM
信号源模块 | CONSTANT | INPUT | NOISE

<font color=#2E8B57 size=5 face="KaiTi">端点模块</font>  
&emsp;&emsp;放在环路中不会引起代数环的模块。详见 [模块次序表、代数环及其检测算法](https://blog.csdn.net/qq_34288751/article/details/122648967)。  
<font color=#2E8B57 size=5 face="KaiTi">包含离散状态的模块</font>  
&emsp;&emsp;可以通过`Set_SampleTime()`设置采样(更新)周期的模块，每个周期的结束更新一次输出值，在$t\in[nT,(n+1)T)$的时间段内输出值为$x(n)$保持不变。以仿真步长$h=0.01$和采样周期$T=1$为例，在$t=0$到$t=0.99$的100个采样点输出$x(0)$，$t=1.00$到$t=1.99$的100个采样点输出$x(1)$，以此类推。  
<font color=#2E8B57 size=5 face="KaiTi">包含自定义函数的模块</font>  
&emsp;&emsp;自定义的函数有两种，FCNMISO使用多输入单输出的函数，FCN和INPUT使用单输入单输出的函数。  
&emsp;&emsp;有两种方法自定义函数，一种是使用lambda表达式(匿名函数)，另一种是重写`UserFunc`类。
<font color=#2E8B57 size=5 face="KaiTi">多输入单输出模块</font>  
&emsp;&emsp;这些模块的不同之处在于，其它模块每次调用`connect()`函数设置的输入模块(孩子模块)会覆盖之前的设置，而这些模块每次调用`connect()`函数都会新添加一个输入模块。目前还没发现有需要覆盖设置的需求，因此多输入单输出模块暂时不能覆盖设置。  
<font color=#2E8B57 size=5 face="KaiTi">包含输入增益的模块</font>  
&emsp;&emsp;可以通过`Set_InputGain()`函数设置输入增益的模块。PRODUCT和SUM模块的这个函数有两个参数，第一个是输入增益，第二个是该输入增益对应设置于哪个输入端口，默认或者"-1"表示设置最新添加的端口。  
<font color=#2E8B57 size=5 face="KaiTi">信号源模块</font>  
&emsp;&emsp;没有输入端口，`connect()`函数为空。

# 单元模块单独介绍
&emsp;&emsp;单元模块的共性在上一节中已经介绍，这一节介绍每个单元模块独有的特点。  
<font color=#2E8B57 size=5 face="KaiTi">常数模块 CONSTANT</font>  
可用输入模块代替，为方便使用而引入。输出常数。默认输出1。  
<font color=#2E8B57 size=5 face="KaiTi">自定义单入单出模块 FCN</font>  
默认将输入值输出。  
<font color=#2E8B57 size=5 face="KaiTi">自定义多入单出模块 FCNMISO</font>  
默认将第一个输入值输出。  
<font color=#2E8B57 size=5 face="KaiTi">放大器 GAIN</font>  
默认增益为1。  
<font color=#2E8B57 size=5 face="KaiTi">输入模块 INPUT</font>  
有两种输入模式，连续信号输入和离散信号输入。连续信号输入就是通过`Set_Function()`设置一个以时间自变量的自定义函数；离散信号输入就是从一个vector数组中读取数据，然后以给定的采样周期输出该数据。  
连续模式下默认输出1，离散模式下如果没有提供数据默认输出0，如果仿真时间内提供的数据不够的话将持续输出最后一个值。  
<font color=#2E8B57 size=5 face="KaiTi">积分器 INTEGRATOR</font>  
<font color=#2E8B57 size=5 face="KaiTi">噪声模块 NOISE</font>  
可用输入模块代替，为方便使用而引入。输出高斯白噪声。可指定采样周期。输出默认均值为0方差为1的标准正态分布。默认采样周期$-1$，即每个仿真步长都输出新的采样点。  
<font color=#2E8B57 size=5 face="KaiTi">输出模块 OUTPUT</font>  
<font color=#2E8B57 size=5 face="KaiTi">乘法器 PRODUCT</font>  
<font color=#2E8B57 size=5 face="KaiTi">加法器 SUM</font>  
<font color=#2E8B57 size=5 face="KaiTi">传输延迟模块 TRANSPORTDELAY</font>  
默认缓存一个采样点，缓存值默认0，默认输出0。由于仿真的离散性质，通过`Set_DelayTime()`函数设置的延迟时间会根据仿真步长计算出四舍五入应该缓存的采样点数。  
<font color=#2E8B57 size=5 face="KaiTi">单位延迟模块 UNITDELAY</font>  
用于构建离散传递函数。  
<font color=#2E8B57 size=5n face="KaiTi">零阶保持器 ZOH</font>  
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa63cb00297c4d53a21de26f5ef9b333.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_10,color_FFFFFF,t_70,g_se,x_16#pic_center)  
&emsp;&emsp;图中模块int1的输入是一个离散的控制器，但从图中的情况可以看出，如果输入模块in1的输出连续，则int1的输入也连续，不符合要求，此时就应该在in1的输出串接ZOH模块。int1的输入前串接也可。  
&emsp;&emsp;注意，零阶保持器在此处的作用是将连续平滑的信号转换成阶梯信号，而不是连续信号和离散信号之间的转换。  

# 矩阵模块介绍
<font color=#2E8B57 size=5n face="KaiTi">多路复用器 MUX</font>  
用于将多个单线信号转换成一个总线信号，即输入为多个单元模块。输出为一个矩阵模块。

<font color=#2E8B57 size=5n face="KaiTi">多路解复用器 DEMUX</font>  
用于将一个总线信号分解成多个单线信号，即输入为一个矩阵模块。输出为多个单元模块。

# 组合模块介绍
下面的模块要大量用到`std::vector<double>`数据类型，而且在变量定义的时候也有点长，因此出于方便的考虑而添加自定义数据类型`vecdble`
```cpp
typedef std::vector<double> vecdble;
```
在此特别说明以防使用时出现困惑。  
<font color=#2E8B57 size=5 face="KaiTi">连续传递函数</font>  
原型
```cpp
TransferFcn(Simulator *sim,
        const std::vector<double> numerator,
        const std::vector<double> denominator,
        std::string name="tf");
```
以下示例表示
$$G(s)=\frac{0.1s+1.5}{10s^2+s+1}$$
```cpp
TransferFcn* mdGs = new TransferFcn(&sim1, vector<double>{0.1, 1.5}, vector<double>{10, 2, 0});
```
内部结构为可控标准型，结构图如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/7ec4906016b446e38bd200885c4618ae.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_13,color_FFFFFF,t_70,g_se,x_16)  
模块的`Set_InitialValue()`函数的输入和`Get_OutValue()`的输出参数均为`std::vector<double>`类型，分别设置上图中从左到右积分器组的初始值和获得积分器组的当前输出值。  
<font color=#2E8B57 size=5 face="KaiTi">离散传递函数</font>  
原型
```cpp
DiscreteTransferFcn(Simulator *sim,
    const std::vector<double> numerator,
    const std::vector<double> denominator,
    std::string name="dtf");
```
以下示例表示
$$D(z)=\frac{6.64-6.008z^{-1}}{1-0.3679z^{-1}}$$
```cpp
DiscreteTransferFcn* mdDz = new DiscreteTransferFcn(&sim1, vector<double>{6.64, -6.008}, vector<double>{0.3679});
```
对于下面的一般形式
$$D(z)=\frac{b_0+b_1z^{-1}+b_2z^{-2}}{1-a_1z^{-1}-a_2z^{-2}}$$
对应simulink中的结构图如下  
![在这里插入图片描述](https://img-blog.csdnimg.cn/3e3fb18d038246f3a2ba9c8d783c45e3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_14,color_FFFFFF,t_70,g_se,x_16)  
参数`numerator`写作
```cpp
vector<double>{b0, b1, b2};
```
参数`denominator`写作
```cpp
vector<double>{a1, a2};
```
由于离散传递函数的特殊结构，使用时要注意一下正负号，遵循奥本海姆《离散时间信号处理》第三版中的约定，对应直接Ⅱ型IIR结构。模块的`Set_InitialValue()`函数的输入和`Get_OutValue()`的输出参数均为`std::vector<double>`类型，分别设置上图中从上到下延迟器组的初始值和获得延迟器组的当前输出值。  
<font color=#2E8B57 size=5 face="KaiTi">求和器</font>  
积分器的简单离散化。结构如图所示。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/57b74042736a4054ba7837a1cfeae8af.png)  
<font color=#2E8B57 size=5 face="KaiTi">状态空间传递函数</font>  
使用状态空间建立模型。  
原型
```cpp
    StateSpace(Simulator *sim, const zhnmat::Mat& A, const zhnmat::Mat& B,
        const zhnmat::Mat& C, const zhnmat::Mat& D, bool isc=true, std::string name="sts");
```
