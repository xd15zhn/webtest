# 专栏目录
[simucpp：C++搭建微分方程求解器框架(重写simulink)](https://blog.csdn.net/qq_34288751/article/details/117740605)  
[simucpp系列教程(1)安装教程](https://blog.csdn.net/qq_34288751/article/details/121111051)  
[simucpp系列教程(2)例程解析(第一部分)](https://blog.csdn.net/qq_34288751/article/details/121112003)  
[simucpp系列教程(3)例程解析(第二部分)](https://blog.csdn.net/qq_34288751/article/details/122155363)  
[simucpp系列教程(4)使用教程与程序说明](https://blog.csdn.net/qq_34288751/article/details/122285634)  
[simucpp系列教程(5)各模块的简要介绍](https://blog.csdn.net/qq_34288751/article/details/122724035)  
[simucpp系列教程(6)函数文档](https://blog.csdn.net/qq_34288751/article/details/123325005)  

@[TOC]  
&emsp;&emsp;<font color=red>**由于simucpp及其依赖库的更新改动较大，下面的教程我还没来得及更新或补充，可能不适用于最新的simucpp版本，仅供参考。**</font> simucpp使用的是最普通的cmake构建工具，不需要包管理器。下面的教程就是使用cmake配置的教程，更好的教程可以参考EGE的：[（二）EGE安装与配置 -CSDN博客](https://blog.csdn.net/qq_39151563/article/details/100161986)  
&emsp;&emsp;当然对新手来说有一种最简单的方法，就是直接把所有代码文件都加入到IDE里(比如visual studio)一起编译即可，下面的教程除了要知道需要哪些依赖库以外完全可以不用看。下面介绍的方法是分别编译成库再链接的过程。  

# 编译环境准备
项目使用 [cmake](https://cmake.org) 构建，cmake教程和C++的编译环境搭建推荐一个B站博主的视频：  
[基于VSCode和CMake实现C/C++开发 | Linux篇](https://www.bilibili.com/video/BV1fy4y1b7TC)  
[手把手教会VSCode的C++环境搭建，多文件编译，Cmake，json调试配置（ Windows篇）](https://www.bilibili.com/video/BV13K411M78v)  
目前已在Windows系统的 [visual studio 2019](https://visualstudio.microsoft.com
)，[qt creator](https://www.qt.io/)，[vs code](https://code.visualstudio.com)，和Linux系统的 vs code 中测试过。其他编译器暂未尝试。推荐几个自认为比较好的cmake教程  
[modern cmake](https://modern-cmake-cn.github.io/Modern-CMake-zh_CN/)  
[Modern CMake is like inheritance](https://kubasejdak.com/modern-cmake-is-like-inheritance)  
[CMake: Public VS Private VS Interface](https://leimao.github.io/blog/CMake-Public-Private-Interface/)  

## 安装依赖库 matplotlib-cpp
原作者代码仓库 <https://github.com/lava/matplotlib-cpp>  
经过我部分修改后用于simucpp的代码仓库 <https://gitee.com/xd15zhn/matplotlibcpp>  
目前仅绘制波形图功能使用了该依赖库，如果不需要绘制波形图，则可以在`CMakeLists.txt`中禁用该库。(或者自己改源代码换成其它的库)  
[matplotlibcpp在linux上安装及简单使用](https://blog.csdn.net/qq_43495930/article/details/118380291)

## 安装依赖库 zhnmat
代码仓库 <https://gitee.com/xd15zhn/zhnmat>  
目前仅有矩阵模块使用了该依赖库，如果不使用矩阵模块，则可以在`CMakeLists.txt`中禁用该库。(或者自己改源代码换成其它的库)  

# 编译项目
出于多次使用的考虑，从V1.5.4版本开始将编译过程分成两步，第一步生成静态链接库，第二步将库链接到自己的程序中，甚至可以将simucpp复制到c++的安装目录中，这样在各个项目中都可以使用。

## visual studio
网上查查教程，比如：[利用 cmake 工具生成 Visual Studio 工程文件](https://cloud.tencent.com/developer/article/1593532)。

## qt creator
qt creator自带cmake所以配置起来简单的多。打开`CMakeLists.txt`文件即可。

## 命令行(vs code)
未完待续。。。

# 第一个项目
新建一个文件夹，命名为`sim1`(名称任意)，新建`CMakeLists.txt`文件（大小写和后缀名必须严格对应）和`main.cpp`(名称任意)文件，新建`build`(名称任意)文件夹，如下图所示(示例)。上图为simucpp文件夹，下图为新建的sim1文件夹。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba332d1da0ce43dd8ee09aff80013a82.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5om-5LiN5Yiw5pyN5Yqh5ZmoMTcwMw==,size_8,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/3ad4058cc709446a8e52a3b96b8014b0.png)
在`CMakeLists.txt`文件中输入(复制)如下内容：
```
cmake_minimum_required(VERSION 3.12)
project(sim)
set(CMAKE_BUILD_TYPE debug)
set(SIMUCPP_DIR D:/cppcode/simucpp)  # 这里输入simucpp文件夹的位置
set(ZHNMAT_DIR D:/cppcode/zhnmat)  # 这里输入zhnmat文件夹的位置
set(PYTHON_DIR D:/python)  # 这里输入python目录的位置

add_executable(${CMAKE_PROJECT_NAME}
    ${PROJECT_SOURCE_DIR}/main.cpp
)
target_link_libraries(${CMAKE_PROJECT_NAME}
    ${SIMUCPP_DIR}/build/libsimucpp.a
    ${ZHNMAT_DIR}/build/libzhnmat.a
    ${PYTHON_DIR}/libs/_tkinter.lib
    ${PYTHON_DIR}/libs/python3.lib
    ${PYTHON_DIR}/libs/python39.lib
)
target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC
    ${SIMUCPP_DIR}/inc
    ${ZHNMAT_DIR}
    # ${MPLT_DIR}
)
```
进入`build`文件夹，使用cmake或者命令行进行编译。windows的mingw命令行为`cmake .. -G "MinGW Makefiles"`和`mingw32-make`，Linux的命令行为`cmake ..`和`make`。
如果你熟悉cmake的话也可自由修改。
