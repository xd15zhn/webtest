# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)

@[TOC]
# SIMUCPP
&emsp;&emsp;matlab被禁了怕什么，大不了自己再写一个😀  
&emsp;&emsp;代码 <https://gitee.com/xd15zhn/simucpp> <https://github.com/xd15zhn/simucpp>  
&emsp;&emsp;用C++求微分方程数值解、进行控制系统仿真，使用类似simulink的搭模块的方式。经大量例程测试仿真精度很高，一般系统仿真几十秒误差不超过$10^{-12}$数量级。

# 前言
&emsp;&emsp;我在使用simulink的过程中遇到了一些麻烦，以及其他一些问题使得我决定使用C++或者python能够用类似simulink的搭模块的方式求解微分方程。
- 速度慢。当然这不是主要原因。一般的使用基本上都能秒出结果。我用Mathieu方程测试过，同样的程序，C++/matlab/python耗时大约为1:10:100，如果用simulink的话肯定更慢，当然我也放弃了python，不过以后可以考虑添加python接口。另外python有库叫simupy，我没研究过，只是借鉴了名字所以叫simucpp。
- 模型发散问题。仿真的时候不能保证模型一定收敛，如果发散的话simulink会报错，大概是什么“at time 0.01s is not finite, try to reduce the simulation step"，实际上就是模型发散了，但我现在要跑一些AI算法，如果发散了我只希望它给出一个正无穷的损失然后继续跑代码而不是停下来报个错。
- 连续和离散混合仿真的原理细节不清楚。这应该是最主要的原因。混合仿真中有一些影响仿真精度的细节问题几乎没人能够注意到，我也是深入钻研了好几天才想明白这些细节是如何在仿真中引入误差的，以及如何消除这些误差。这些细节问题在番外篇有详细解读。
- 混合编程。一般来说C/C++比较适合写各种效率至上的算法，以前每次遇到需要simulink和C/C++混合编程的时候总得把C++用matlab重写一遍，现在不用了。
- 代码维护问题。如果一个系统中有多个相同的子系统，后面如果还需要修改子系统的话据我所知需要同样的操作改好几遍，且没有批量修改的方法。代码形式的话版本控制也是一个优点。
- matlab被禁。matlab在系统仿真中应用广泛，而且目前虽然有替代品但都不出名，我觉得这就跟操作系统一样，不是我们做不出来，而是没有做的必要，就算做了也没有收益。总之也算是为避免卡脖子做出了属于我的贡献。

&emsp;&emsp;最新补充：simulink仿真发散的报错是
>Derivative of state '1' in block '{模块名称}' at time 0.890625 is not finite. The simulation will be stopped. There may be a singularity in the solution.  If not, try reducing the step size (either by reducing the fixed step size or by tightening the error tolerances) 

不知道为什么给的建议是减小步长或者误差容限，而不是检查你的模型是不是发散。
&emsp;&emsp;与其他人交流过以后发现还有其他的一些方法。首先matlab中有ode23和ode45函数，但我试过结果不对，不知道我用法有没有问题(这就是闭源的弊端)，而且也不知道为什么没有ode4函数(截止R2020a版本)。或者有人说直接手撕ode4，区区4组函数也不算太复杂。  
&emsp;&emsp;也有人问是否有代码生成的功能，据说现在工业领域比如汽车行业已经广泛使用基于模型的设计(Model Based Design, MBD)，用simulink搭建模型然后直接将代码下载到处理器上。我之前用单片机写过航模飞控，一开始也是想用simulink代码生成的，但我后面发现控制部分的代码很重要但代码量并不多，占大头的还是串口通信、任务调度、传感器信号处理等simulink生成不了的代码，所以我写这个的初衷只是为了仿真，尤其是跟计算机有关的连续离散混合仿真。我也不知道MBD是如何解决我前面遇到的那些问题的，欢迎指教。  
&emsp;&emsp;仿真中还有一个与代码生成相关的问题是，代码中各个模块的更新大量使用了虚函数，严重限制了仿真速度。所以我目前对代码生成的需求是能够去掉虚函数，直接生成ode4的4组函数，加快仿真速度。  
&emsp;&emsp;最后，**如果有什么问题欢迎讨论**。

# 简单的例子

![](https://img-blog.csdnimg.cn/47f3423aa5af4cd59a223527783e1bed.png)

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

![在这里插入图片描述](https://img-blog.csdnimg.cn/44474cc166a74ddc87ce704c06a412bb.png)  

这一示例的详细介绍见 [simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)。
# 另一个简单的例子
仿真$x'=-x^3-x$，$x(0)=0.2$。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/d0b18ee53bed4328b89e29de5362255f.png)
```cpp
#include "simucpp.hpp"
using namespace simucpp;
using namespace std;
int main()
{
    Simulator sim1;  // 仿真器
    FMIntegrator(int1, &sim1);  // 积分器
    FMFcn(fcn1, &sim1);  // 函数模块
    FMOutput(out1, &sim1);  // 输出模块
    sim1.connect(int1, fcn1);  // 连接积分器和函数模块
    sim1.connect(fcn1, int1);  // 连接函数和积分器模块
    sim1.connect(int1, out1);  // 连接积分器和输出模块
    int1->Set_InitialValue(0.2);  // 将积分器初值设置为0.2
    fcn1->Set_Function([](double u){return -u*u*u-u;});  // 设置函数模块的函数为-x^3-x
    sim1.Initialize();  // 仿真器初始化
    sim1.Simulate();  // 仿真
    return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ca54fca9648440cbfc7546f7da89e04.png)

# 关于框图显示的说明
&emsp;&emsp;类似于simulink的框图显示是一开始就有的功能，但后来因为种种原因废弃了，包括但不限于：
- 布局布线麻烦，如果使用网格式布局的话则限制多输入单输出模块的输入端口不超过3个，加入传递函数模块后就出现了问题；
- 使用代码布局布线这件事本身对于使用库的用户来说工作量也很大，而自动布局布线对库的作者又是另一个巨大的工作量；
- 框图并不是一个刚需，而且大量的使用效果表明，大部分时候都是给出微分方程，而像simulink一样把方程转为框图反而多了一个步骤，得不偿失，直接把方程写成代码反而更直观，而且对于复杂的系统来说，代码相比于框图更便于后期维护。

&emsp;&emsp;当然由于框图直观易懂的优点，不排除以后还会把它以另一种方式加进来的可能。

# 其它相关的项目
- <https://www.zhihu.com/question/400822347/answer/1298266706> 这篇回答列举了与matlab功能类似的开源软件。
- <https://github.com/OpenModelica/OpenModelica> modelica建模语言。
- <https://github.com/petercorke/bdsim> 与simucpp类似，使用python编写。
- <https://github.com/simupy/simupy> 与simucpp类似，使用python编写。
- <http://headmyshoulder.github.io/odeint-v2/index.html> 数值求解常微分方程的C++库，目测不能解决包含混合仿真和矩阵模块的问题，有时间看看能不能借鉴。
- <https://github.com/ikorotkin/dae-cpp> 求解微分代数方程（differential algebraic equations）
- <https://github.com/NTNU-IHB/FMI4cpp> 解析FMU文件。
- ege图形库: <https://xege.org/> 2D图形库，可用于动态展示2D仿真结果。实际使用的例子见下。
- <https://github.com/raysan5/raylib> 3D游戏编程库，可用于动态展示3D仿真结果。实际使用的例子见下。

目前用该库解决的实用问题如下：
- [倒立摆控制与仿真动画展示](https://blog.csdn.net/qq_34288751/article/details/122640930)
- [卫星轨道3D仿真程序](https://gitee.com/xd15zhn/satellitesim)
- [降维状态观测器的直观理解与仿真](https://blog.csdn.net/qq_34288751/article/details/123061451)
- [导弹追踪问题](https://blog.csdn.net/qq_34288751/article/details/122725421)
- [水流冲重物问题](https://blog.csdn.net/qq_34288751/article/details/123159543)

# 参考文献和资料
- [Modelling and Simulation Lectures](http://msdl.cs.mcgill.ca/people/hv/teaching/MS/COMP522A2006/lectures/lecture.CBD/)  
- [Causal-Block Diagrams](http://msdl.cs.mcgill.ca/people/claudio/pub/Gomes2016a.pdf)  
- 李俊,朱长皓,陆梦寒.基于Simulink模型确定计算顺序的研究[J].计算机科学,2014,41(11):227-232.  
- [Processing Causal Block Diagrams with Graph Grammars in AToM](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.93.402&rep=rep1&type=pdf)  
- Denckla B ,  Mosterman P J . Formalizing Causal Block Diagrams for Modeling a Class of Hybrid Dynamic Systems[C]// European Control Conference Cdc-ecc 05 IEEE Conference on Decision & Control. IEEE, 2005.

# 致谢
- 特别感谢 [@雪浪云-杭波](https://blog.csdn.net/tanhngbo) 为 simucpp 中涉及到的诸多技术细节提供的思路以及找到的诸多参考文献，对我帮助很大。
- <https://blog.csdn.net/m0_49572858> 汇川 <https://www.inovance.com/>
