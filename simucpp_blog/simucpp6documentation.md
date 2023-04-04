# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)  

@[TOC]  
&emsp;&emsp;所有函数包括仿真器的函数和模块的函数。仿真器的函数中包括用于批量设置输出模块的函数和其它设置函数；模块的函数中包括基类模块函数、含有特殊属性的函数、模块自有的函数等。  
&emsp;&emsp;这篇文章叫文档其实不太合适，只做了大致的总结但足够日常使用了，具体的使用细节在之前的文章中已经进行了详细介绍，仿真器的函数的具体使用与设置见教程(4)，模块的函数的具体使用与设置见教程(5)。或者，程序的头文件中对各个函数的注释也很详细。
&emsp;&emsp;

# 仿真器函数
通过下面的函数对仿真器进行设置。
| 函数 | 含义 | 备注
| -|-|-|
| void Initialize() | 建立模块之间的连接 | 公有
| void Simulation_Reset() | 属于当前仿真器的所有模块复位 | 公有
| int Simulate() | 进行一次仿真 | 公有
| int Simulate_OneStep() | 进行一次单步仿真 | 公有
| int Simulate_FinalStep() | 单步仿真的最后一步 | 公有
| void Set_EnableStore(bool) | 是否将输出模块的数据保存到一个vector数组中 | 公有,OUTPUT$^1$
| void Set_EnablePrint(bool) | 是否将仿真数据写入文件 | 公有
| void Set_PrintPrecision(unsigned int) | 设置写入文件的浮点数有效数字位数 | 公有
| void Set_SampleTime(bool) | 设置多长时间保存一次数据至vector | 公有,OUTPUT
| void Set_t(double) | 设置开始仿真的时间，默认为0，用于计算定积分 | 公有
| double Get_t() | 获取当前仿真时间 | 公有
| void Set_Endtime(double) | 设置仿真截止时间 | 公有
| double Get_Endtime() | 获取仿真截止时间 | 公有
| void Set_SimStep(double) | 设置仿真步长 | 公有
| double Get_SimStep() | 获取仿真步长 | 公有
| void Set_DivergenceCheckMode(int) | 设置仿真发散后的行为，见下表 | 公有
| void Add_Module(const PUnitModule) |  | 私有
| void Add_Module(const PMatModule) |  | 私有
| void Build_Connection(std::vector<int>&) |  | 私有

$^1$该函数与输出(OUTPUT)模块的对应函数同名，用于批量设置输出模块。  
&emsp;&emsp;仿真发散的类型分3种，正无穷、负无穷和非数，仿真发散后的行为列在下表，暂时没起名字，只给编号。
| 行为编号 | 是否打印 | 是否终止仿真 | 是否终止程序
| -|-|-|-|
| 0(默认) | $\checkmark$ | $\checkmark$ | $\checkmark$
| 1 | $\checkmark$ | $\times$ | $\times$
| 2 | $\times$ | $\times$ | $\times$
| 3 | $\checkmark$ | $\checkmark$ | $\times$
| 4 | $\times$ | $\checkmark$ | $\times$

# 单元模块基类函数
所有单元模块都有的函数。
| 函数 | 含义 | 备注
| -|-|-|
| double Get_OutValue() | 得到模块的输出值 | 公有
| void Set_Enable(bool enable) | 使能模块 | 私有
| int Self_Check() | 初始化时检查模块是否存在问题 | 私有
| void Module_Update(double time) | 模块更新 | 私有
| void Module_Reset() | 复位模块 | 私有
| int Get_childCnt() | 模块的输入端口数量 | 私有
| PUnitModule Get_child(unsigned int n) | 得到第N个输入端口对应的模块句柄 | 私有
| void connect(const PUnitModule m) | 将一个模块连接到这个模块的输入端口 | 私有

# 单元模块派生类函数
| 函数 | 含义 | 备注
| -|-|-|
| void Set_SampleTime(double) | 设置采样周期 | 公有,离散$^1$
| void Set_Function(double(*function)(double)) | 设置输入函数 | 公有,函数$^2$
| void Set_Function(UserFunc*) | 设置输入函数 | 公有,函数
| void Set_InputGain(double, int) | 设置输入端口的增益 | 公有,增益$^3$
| void Set_Continuous(bool) | 设置是否是连续模块 | 公有,INPUT
| void Set_EnableStore(bool) | 是否将数据保存到一个vector数组中 | 公有,OUTPUT
| vector<double>& Get_StoredData() | 得到内部存储的数据 | 公有,OUTPUT
| void Set_MaxDataStorage(int n) | 设置最大可以存储的数据量 | 公有,OUTPUT

$^1$包含离散内容的模块均有该函数。
$^2$包含自定义函数的模块均有该函数。
$^3$包含输入增益的模块均有该函数。

# 矩阵模块基类函数
所有单元模块都有的函数。
| 函数 | 含义 | 备注
| -|-|-|
| PUnitModule Get_OutputPort(BusSize size)| 根据总线中的给定点返回总线中的一个单元模块 | 公有
| BusSize Get_OutputBusSize() | 返回输出总线尺寸 | 公有
| bool Initialize() | 生成指针容器，建立连接 | 公有
| u8 Get_State() | 判断模块是否已确定总线尺寸或初始化 | 公有

# 矩阵模块派生类函数
| 函数 | 含义 | 备注
| -|-|-|

# 程序结构与包含关系
![在这里插入图片描述](https://img-blog.csdnimg.cn/0850ff1ae5774ab4b0b49f9bf24de9c2.png)
图中省略了每个cpp文件都包含的头文件`deninitions.hpp`，这个头文件是库的内部文件，不对外公开。
