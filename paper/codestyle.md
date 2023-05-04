摘要：自用的编程规范：LaTeX，C/C++，Python。

# $\LaTeX$
## 其它
- 一个`section`一个文件，一个`chapter`一个文件夹。
- 注释方式为：空两格+注释符+空一格+注释内容(不止latex，所有程序都遵循这一注释规范)

## 标题
注释里写本节的缩写，括号里备注全名，文件名为`src`+缩写，例如：  
文件名`srcIDFsim.tex`，缩写`IDFsim`，全名`parameter identification simulation`，如下
```tex
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% IDFsim (parameter identification simulation)
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\section{一级标题}
正文。

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\subsection{二级标题}
正文。

% ////////////////////////////////////////
\subsubsection{三级标题}
正文。
```

## label标号
label标号格式为：类型+节缩写+名称，图片命名格式为：节缩写+名称.后缀
```tex
\label{eqIDFsimMathModel}  % 数学模型方程
\label{tabIDFsimErrTable}  % 误差表
\label{figIDFsimPlant}  % 被控对象模型的模块框图
\includegraphics{IDFsimPlant.pdf}  % 插入图片
```

# C/C++
## 命名方式
- 特别有名的代码或chatGPT生成的代码保持命名风格不变。
- **变量** 驼峰命名法 xxx/xxXxx  
例如 dist/shipSpeed
- **函数** 首字母大写加下划线分隔 Xxx_Xxx()  
例如 Simulate_OneStep(),Get_InputPort()
- **宏定义** 全部大写加下划线分隔 XXX_XXX

# Python
## 命名方式
与C/C++相同。

