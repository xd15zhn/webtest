# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)  

@[TOC]
# 简介
&emsp;&emsp;实现一次仿真需要做的工作为，添加仿真器并设置其参数，添加模块并设置其参数，连接各个模块，以及后续的仿真器初始化、仿真、绘图步骤（这些就是所有工作，不需要加"等"字）。  
&emsp;&emsp;所有模块分为单元模块、组合模块和矩阵模块。矩阵模块中有两个特殊的模块，即用于标量和矩阵信号相互转换的多路复用/解复用模块。  
&emsp;&emsp;单元模块是完成仿真必备的模块，除了几个简化代码量的模块以外，其余的模块如果少了任意一个都无法实现某些仿真。在仿真器初始化后，组合模块和矩阵模块也都将被删除，最后参与仿真的也只剩下单元模块。模块的具体介绍见教程(5)。单元模块的输入端口数量可以为0个、1个或多个，而且如果有多个输入端口，这些端口的功能也是相同的，也就是说多个输入端口调换顺序不会影响结果。单元模块的输出端口数量只有1个，没有输出端口的输出模块例外。  
&emsp;&emsp;矩阵模块在版本V1.7.13加入，主要是为了解决矩阵微分方程的问题，当然也可以用来解决状态空间以及过多输入输出端口之间的连接问题。单元模块之间连接的信号均为标量，使用`connect()`函数一次只能连接一个信号，而矩阵模块之间使用总线连接，一条总线代表一个矩阵（而不是一个向量）。矩阵模块和单元模块之间的连接需要使用多路复用/解复用模块连接单线和总线。复用/解复用的名称好像不太准确，实际上多路复用器就是数据选择器，而不是总线合并器。但好像又没有一个合适的名字，所以这里就称作复用器。  
&emsp;&emsp;组合模块由单元模块和矩阵模块组成。如果需要一个模块的输入端口有多个不同的功能，或者需要有多个输出端口，或者像simulink一样使用子模块，就可以使用组合模块。  

# 仿真程序介绍
以例程第一部分的demo1为例说明。
```cpp
/*一阶系统阶跃响应*/
#include <iostream>
#include "simucpp.hpp"
using namespace simucpp;
using namespace std;

double Input_Function(double t)
{
    return 1;
}

int main()
{
    // 初始化仿真器
    Simulator sim1(10);

    // 初始化模块
    MIntegrator *integrator1 = new MIntegrator(&sim1);
    MSum *sum1 = new MSum(&sim1);
    MInput *input1 = new MInput(&sim1);
    MOutput *output1 = new MOutput(&sim1);

    // 模块之间的连接
    sim1.connect(input1, sum1);
    sum1->Set_InputGain(1);
    sim1.connect(sum1, integrator1);
    sim1.connect(integrator1, sum1);
    sum1->Set_InputGain(-0.5);
    sim1.connect(integrator1, output1);

    // 参数设置
    integrator1->Set_InitialValue(0);
    input1->Set_Function(Input_Function);

    // 运行仿真
    sim1.Initialize();
    sim1.Simulate();
    cout.precision(12);
    cout << output1->Get_OutValue() << endl;
    sim1.Plot();
    return 0;
}
```
## 初始化仿真器
仿真器默认仿真时长为10，因此可简写作
```cpp
Simulator sim1;
```
## 初始化模块
下面代码表示初始化一个积分器。
```cpp
MIntegrator *integrator1 = new MIntegrator(&sim1);
```
每个模块都有个默认的名字，名字可以重复但是建议最好不要重复，如果仿真哪里出现问题的话会在命令行或IDE里输出错误或警告，在这些信息里会指出哪个模块出了问题，如果名字重复的话出了问题不好判断。使用自定义的名字的话可以写成
```cpp
MIntegrator *integrator1 = new MIntegrator(&sim1, "name");
MIntegrator *integrator1 = new MIntegrator(&sim1, "integrator1");
```
推荐给模块起的名字与模块的变量名相同。这里可以看出代码比较复杂了，需要简化一下。比如前面的`MIntegrator`可以改成`auto`。
```cpp
auto *int1 = new MIntegrator(&sim1, "int1");
```
重复写两遍`int1`还是比较麻烦，这里有个反射的概念我暂时没搞懂，可以临时用宏定义代替。下面的宏我已经在V1.5.8版本以后加到库中了，在用户代码中可以直接用。
```cpp
#define FMIntegrator(x,sim)      MIntegrator*x=new MIntegrator(sim,#x)
FMIntegrator(int1, &sim1);  // 宏定义展开成 MIntegrator*int1=new MIntegrator(&sim1,"int1");
```
在自定义组合模块里，指针变量的定义和赋值要分成两步，
```cpp
MIntegrator *integrator1;  // 在类的成员中定义
integrator1 = new MIntegrator(&sim1);  // 在类的初始化函数中赋值
```
对应的宏定义也要分成两步。具体详见例程。
## 模块之间的连接
```
sim1.connect(input1, sum1);
```
上面的代码表示将输入模块`input1`的输出端口与加法器`sum1`的输入端口相连。
## 参数设置
&emsp;&emsp;设置模块参数和仿真器参数，比如设置输入模块的输入函数、放大器的增益，仿真器是否储存仿真数据、是否打印数据至文件等。很多模块都有默认参数，只要修改不是默认的参数即可。
&emsp;&emsp;包含自定义函数的模块需要设置函数。设置函数有两种方法，分别是函数传参和匿名函数(lambda表达式)。
函数传参很容易理解：
```cpp
double YourFunc(double t) {
    return exp(t)+1.0;
}
input1->Set_Function(YourFunc);
```
lambda表达式用法如下：
```cpp
input1->Set_Function([](double t) {
    return exp(t)+1.0;
});
```
lambda表达式更简洁，推荐使用。

# 仿真器的其它设置选项
仿真器的设置比较简单，见教程(6)函数文档。

# 自定义组合模块
下面代码是写组合模块的代码模板：
```cpp
class YourModuleName: public PackModule
{
public:
    YourModuleName(Simulator *sim, std::string name="yourmdl");
private:
    virtual PUnitModule Get_InputPort(int n=0) const {
        if (n==0) return in1;
        if (n==1) return in2;
        return nullptr;
    }
    virtual PUnitModule Get_OutputPort(int n=0) const {
        if (n==0) return in1;
        if (n==1) return in2;
        return nullptr;
    }
};
```
&emsp;&emsp;上面的代码示例一个最简单的组合模块的类定义，其中的两个虚函数`Get_InputPort`和`Get_OutputPort`以及使用组合模块时可能会用到的连接器CONNECTOR模块的使用需要重点说明。
&emsp;&emsp;所有的单元模块都有一个或多个输入端口，但只有一个输出端口，假如，一个组合模块包含了两个SUM模块，一个SUM有两个输入端口，这样这个组合模块共有4个输入端口和2个输出端口。重写的两个虚函数需要返回一个具体的模块句柄，对于返回输入端口句柄函数`Get_InputPort`，如果只是返回SUM的话，并不能向仿真器指明应该将一个模块设置为SUM的第几个孩子模块(换句话说将SUM的哪个输入端口与另一个模块的输出端口相连)，此时就需要加入4个连接器来指代这个组合模块的4个输入端口；但对于返回输出端口句柄函数`Get_OutputPort`，返回SUM模块即可，不需要使用连接器，因为SUM只有一个输出端口。这个组合模块的类定义如下
```cpp
class TwoSums: public PackModule
{
public:
    TwoSums(Simulator *sim, std::string name="sums") {
        SMSum(msum1, sim);
        SMSum(msum2, sim);
        min1 = new MConnector*[4];
        min1[0] = new MConnector(sim, "in0");
        min1[1] = new MConnector(sim, "in1");
        min1[2] = new MConnector(sim, "in2");
        min1[3] = new MConnector(sim, "in3");
        sim->connect(min1[0], msum1);
        sim->connect(min1[1], msum1);
        sim->connect(min1[2], msum2);
        sim->connect(min1[3], msum2);
    }
private:
    virtual PUnitModule Get_InputPort(int n=0) const {
        if (n<4) return min1[n];
        return nullptr;
    }
    virtual PUnitModule Get_OutputPort(int n=0) const {
        if (n==0) return msum1;
        if (n==1) return msum2;
        return nullptr;
    }
    MSum *msum1 = nullptr;
    MSum *msum2 = nullptr;
    MConnector **min1 = nullptr;
};
```
# 不同类型模块之间的连接
&emsp;&emsp;仿真器对组合模块的连接函数`connect()`也与单元模块不太一样。`connect()`函数及其重载的完整定义如下
```cpp
    void connect(PUnitModule m1, PUnitModule m2);
    void connect(PUnitModule m1, PPackModule m2, int n2=0);
    void connect(PPackModule m1, int n1, PUnitModule m2);
    void connect(PPackModule m1, PUnitModule m2);
    void connect(PPackModule m1, int n1, PPackModule m2, int n2=0);
    void connect(PPackModule m1, PPackModule m2, int n2=0);
```
组合模块与组合模块、组合模块与单元模块、单元模块与组合模块之间连接时，对组合模块需要指明要连接的是哪个端口，不指明时默认连接第0端口；而单元模块之间的连接不需要指明，单元模块与组合模块之间连接时也不需要指明单元模块的端口，尽管单元模块可能有多个输入端口。
# 如何进行单步仿真
单步仿真需要调用如下两个函数。
```cpp
int Simulate_OneStep();
int Simulate_FinalStep();
```
第一个函数每调用一次仿真一个步长，第二个函数用于输出最后一个步长的结果。比如，仿真1秒，步长0.001秒，如果不调用后一个函数则出来的结果是0秒到0.999秒的1000个点，没有1秒时刻的值。
# 其他细节问题
## 程序的运行顺序与野指针问题
`Simulator::connect()`函数中有检测空指针与空模块的步骤，因此建议在初始化时将`模块之间的连接`放在`参数设置`之前，出了问题便于及时定位。C++检测野指针困难，因此定义模块时建议写成
```cpp
UIntegrator *integrator1 = nullptr;
```
## 影响仿真精度的因素
各种因素的原因详见番外篇(2)混合仿真.
- 老生常谈的舍入误差，比如仿真步长设置为$0.001$与$\frac{1}{1024}$等。误差一般小于$10^{-12}$数量级，一般使用基本不用考虑。
- 连续系统仿真时，仿真时长应当设置成仿真步长的整数倍，否则会有约$10^{-6}$数量级的误差。
- 连续离散混合仿真时离散周期应为仿真步长的整数倍，否则会有约$10^{-4}$数量级的误差。
