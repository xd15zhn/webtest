# 集合优化问题与贪心算法的性能
**摘要：** 

*Submodular optimization problems and greedy strategies: A survey*  
<https://link.springer.com/article/10.1007/s10626-019-00308-7>

**论文摘要：** 贪婪策略是一种近似算法，用于解决多动作决策中出现的优化问题。与最优解相比，贪婪策略有多好？在这篇综述中，我们主要考虑两类优化问题，其中目标函数是子模的。第一种是集-子模优化，即选择一组动作来优化集-子模块目标函数；第二种是串-子模最优化，即选择有序的动作集来优化串-子模块函数。我们在这里的重点是子模块优化问题中贪婪策略的性能边界。具体来说，我们回顾了贪婪策略的性能边界、曲率方面的更一般和改进的边界、分批贪婪策略的绩效边界和纳什均衡的绩效边界。

# 集合优化问题
&emsp;&emsp;集合优化问题可以描述为
$$ \max f(M),\quad \text{s.t. }M\in\mathcal I $$
其中 $\mathcal I=\{2^X\}$ 是一个包含有限元素的非空集合 $X$ 的子集的所有组合的集合，$f:2^X\to\mathbb R$ 是一个集合函数。例如，$X=\{1,2,3\}$ 有3个元素，那么 $\mathcal I$ 有 $2^3=8$ 个元素，每个元素 $M$ 都是 $X$ 的一个子集。有的问题中要求 $2$ 和 $3$ 不能同时出现，此时 $\mathcal I$ 有6个元素
$$ \mathcal I=\{(),(1),(2),(3),(1,2),(1,3)\} $$
问题就成了，在这6个元素中找到那个使 $f$ 最大的元素，这就是集合优化问题。

# 次模与拟阵相关定义
&emsp;&emsp;参考链接的[1,2]有关于次模函数的介绍，[3]是次模函数性质的证明，写的都非常好可以先看这些，论文中与这些重复的就不写了。链接[4]中把拟阵跟向量组的线性无关作对比（后来发现这些内容在算法导论[5]里都有），我也在括号中加上我的理解。  
**(原文)例2：** 令 $X=\{a,b,c\}$，定义3种子集族(collection of subsets)
$$\begin{aligned}
& \mathcal I_1=\{(),(a),(b),(c),(a,c)\} \\
& \mathcal I_2=\{(a),(a,b)\} \\
& \mathcal I_3=\{(),(a),(b),(a,b)\} \\
\end{aligned}$$
可以看出 $\mathcal I_1$ 满足遗传性但不满足交换性，$\mathcal I_2$ 满足交换性但不满足遗传性，$\mathcal I_3$ 都满足，所以 $(X, \mathcal I_1)$ 是独立系统，$(X, \mathcal I_3)$ 是拟阵。$(X, \mathcal I_2)$ 什么也不是。$(X, \mathcal I_1)$ 中的最大无关组为 $\{b\}$ 和 $\{a,c\}$。  
&emsp;&emsp;给出一些论文里的定义。
- 极大独立子集(maximal independent subset)：令 $(X,\mathcal I)$ 为一个独立系统（满秩矩阵），$S\subseteq X$ 是 $X$ 的任意一个子集（向量组）。$S$ 的基 $B$ 指的是满足下面两个条件，并且是 $S$ 的一个子集：  
一，$B$ 是一个独立集，即 $B\in\mathcal I$；  
二，$B$ 的元素最多，也就是说，$B$ 不是任何 $S$ 的独立集的（真）子集。  
满足这两个条件的 $B$ 也被称作 $S$ 的极大独立子集（最大线性无关组）。
- 秩商(rank quotient)：定义 $S$ 的下秩 $\text{lr}(S)$ 和 上秩 $\text{ur}(S)$ 分别为
$$\begin{aligned}
& \text{lr}(S)=\min\{|B|\} \\
& \text{ur}(S)=\max\{|B|\} \\
\end{aligned}$$
其中 $|B|$ 表示 $B$ 中的元素数量。则定义
$$ q(X,\mathcal I)\triangleq\min\left\{\frac{\text{lr}(S)}{\text{ur}(S)}\right\} $$
为 $(X,\mathcal I)$ 的秩商。  
- *多拟阵集函数(polymatroid set function)*：满足单调次模并且 $f(\emptyset)=0$ 的函数。
- *归一拟阵(uniform matroid)*：对于一个给定的自然数 $K$，如果 $X$ 的一个子集 $S$ 满足 $|S|\leq K$，并且拟阵中所有极大独立子集的元素数量相同，那么称 $S$ 的集合组 $\mathcal I$ 为一个归一拟阵，并且 $K$ 称为它的秩。

&emsp;&emsp;补充一些人话，得拿具体的例子才能说明白。先说极大独立子集。在例2中补充一个子集族
$$ \mathcal I_4=\{(),(a),(a,b),(a,c)\} $$
单独一个元素 $c$ 不在 $\mathcal I_4$ 中，所以 $c$ 不是一个独立集；$(a),(a,b)$ 都是独立集，但 $(a)$ 是 $(a,b)$ 的子集，所以 $(a,b)$ 是极大独立集但 $(a)$ 不是。再说秩商。$\mathcal I_4$ 中，$(a,b),(a,c)$ 都是极大独立集，这两个极大独立集的秩（也就是元素数量）都是2，所以秩商就是1。但注意 $\mathcal I_4$ 不满足遗传性所以不是拟阵。$\mathcal I_1$ 的极大独立集是 $(b),(a,c)$，所以秩商是0.5。
# 贪心算法的性能下界
&emsp;&emsp;贪心算法的形式化数学描述见原文，主要思路是，初始化一个空集 $G_0$，在第i次迭代中，计算往 $G_{i-1}$ 中加入哪一个元素会使函数值最大就加入这个元素变为 $G_i$。  
&emsp;&emsp;下面以例2为例来说明这个秩商的概念有什么用。原文中定理1说如果 $(X,\mathcal I)$ 是独立系统，并且 $f$ 是可加的，也就是 $f(\{a,b\})=f(\{a\})+f(\{b\})$，那么贪心算法的下界满足
$$ \frac{f_\text{gre}}{f_\text{opt}}=q(X,\mathcal I) $$
例如 $\mathcal I_1$ 是个独立系统，那么 $f(\mathcal I_1)$ 的解不会低于最优解的 $50\%$。这很好理解，假如每个函数的收益相同，那么贪心算法的第一步选a或b或c，如果选b那最后的收益就是0.5，选a或c那最后的收益就是1；如果b的收益最大，算法第一步贪心地选择b，最后的收益就是0.5，否则就是选a或c，收益是1。  
&emsp;&emsp;解释一下为什么要定义 $q=\min(lr/ur)$ 而不是直接 $(lr/ur)$，也就是说为什么要另外摘出一个子集 $S$。定义独立系统 $(X,\mathcal I)$ 为
$$\begin{aligned}
& X=\{1,2,3,4,5\} \\
& \mathcal I_{\max}=\{(1,2,3),(3,4,5)\} \\
\end{aligned}$$
其中 $\mathcal I_{\max}$ 表示 $\mathcal I$ 中所有的极大独立子集，也就是说集合 $(1,2,3)$ 的8个子集和集合 $(3,4,5)$ 的8个子集也都在 $\mathcal I$ 中，去掉重复的元素 $\emptyset,3$， $\mathcal I$ 中的元素个数为 $16-2=14$ 个。可以看出 $X$ 只有两个极大独立子集，而且秩都是3，所以集合 $X$ 的上下秩之比为1，但很明显在 $X$ 上定义的贪心算法达不到最优。下面取出 $X$ 的一个子集 $S_1=\{1,3,4,5\}$，它的极大独立子集为 $\mathcal I_{\max}(S_1)=\{(1,3),(3,4,5)\}$；若取 $S_2=\{1,4,5\}$，则 $\mathcal I_{\max}(S_2)=\{(1),(4,5)\}$。上下秩之比分别为 $2/3$ 和 $1/2$，秩商为上下秩之比的最小值，为 $0.5$。事实上，如果秩商等于1，那么这个系统就是拟阵，跟交换性等价。  
&emsp;&emsp;定义次模函数的曲率
$$ c=\max_{a\in\mathcal A}
\left\{1-\frac{U(\mathcal A)-U(\mathcal A \setminus\{a\})}{U(\mathcal \{a\})}\right\} $$
$c\in[0,1]$，也就是边际收益递减效果越明显则曲率越高，没有边际收益递减效果时曲率为0。当系统是拟阵时，次优性满足
$$ \frac{f_\text{gre}}{f_\text{opt}}=\frac{1}{1+c} $$
&emsp;&emsp;然后分析一下为什么次模函数[2]、秩商、曲率给出的性能下界都不一样，秩商给出的界是极大独立子集的元素数量之比，次模函数[2]给出的是 $1-1/e$，曲率给出的是 $1/(1+c)$，分别对应原文中定理1、3、5。从链接[2]中的证明过程可以看出，集合同时满足遗传性和交换性，还要求集合解的个数等于 $K$，是个秩为 $K$ 的归一拟阵，刚好符合原文中定理3的要求；而定理1只需要是个独立系统就行。总结如下表。

定理 | 性能下界 | 满足条件
-|-|-
定理1(秩商) | $$\min\left\{\frac{\text{lr}(S)}{\text{ur}(S)}\right\}\in(0,1]$$ | 独立系统
定理3(次模函数) | $$1-\frac{1}{e}\approx 0.63$$ | 次模函数，归一拟阵
定理5(曲率) | $$\frac{1}{1+c}\in[0.5,1]$$ | 次模函数，拟阵

&emsp;&emsp;定理1和3有个看似矛盾的点在于，一个同时满足次模函数和归一拟阵的系统一定是独立系统，而且其上下秩都是 $K$，按照定理1，性能下界是1；但按照定理3，性能下界又是 $0.63$。原因在于定理1有个要求是收益函数是可加的，而定理3要求是次模的，也就是分别为
$$\begin{aligned}
& f(\sum a)=\sum f(a) \\
& f(A\cup\{a\})-f(A)\neq f(B\cup\{a\})-f(B) \\
\end{aligned}$$
可以看出次模函数跟可加函数是矛盾的，次模函数不可加，可加函数不次模。

<!--下面举个原文里的例子来说明这个秩商的概念有什么用。  
**例5：** 集合为 $X=\{1,2,3,4,5,6\}$，子集族为
$$\begin{aligned}
\mathcal I=&\{\emptyset, \\
& \{1\},\{2\},\{3\},\{4\},\{5\},\{6\},\\
& \{1,2\},\{0\}\} \\
\end{aligned}$$ -->

# 定理1证明
&emsp;&emsp;证明过程见参考文献[2]，这里大概翻译一下。对独立系统 $(X, \mathcal I)$ 和可加的收益函数 $f$，把集合 $X$ 中的元素从大到小排列，即 $X=\{x_1,x_2,\cdots,x_n\}$ 并且满足 $i\leq j\Rightarrow f(x_i)\geq f(x_j)$，令 $X$ 的子集 $X_i=\{x_1,x_2,\cdots,x_i\}$，最优解集合为 $O$，贪心算法的解集合为 $G$。为了方便解释还是举例子
$$\begin{aligned}
& X=\{1,2,3,4,5\} \\
& \mathcal I_{\max}=\{(1,2,4),(2,3,5)\} \\
\end{aligned}$$
其中 $X$ 中的元素已经按从大到小顺序排列。假设 $G=\{1,2,4\},O=\{2,3,5\}$。先列出下面两个等式
$$\begin{aligned}
& f(G)=\sum_{i=1}^{n}|G\cap X_i|\Big[f(x_i)-f(x_{i+1})\Big] \\
& f(O)=\sum_{i=1}^{n}|O\cap X_i|\Big[f(x_i)-f(x_{i+1})\Big] \\
\end{aligned}$$
举个例子（这个式子太妙了我都没能一眼看出来成立），
$$\begin{aligned}
f(G) =& 1\cdot\Big[f(1)-f(2)\Big]+2\Big[f(2)-f(3)\Big]
+2\cdot\Big[f(3)-f(4)\Big]+3\cdot[]+3\cdot[] \\
=& f(1)+f(2)+f(4)
\end{aligned}$$
因为 $O\cap X_i\subseteq O\in\mathcal I$，所以 $|O\cap X_i|\leq\text{ur}(X_i)$。按照定义，$G\cap X_i$ 是子集 $E_i$ 的极大独立子集，所以 $|G\cap X_i|\geq\text{lr}(X_i)$，$|G\cap X_i|\cdot\text{ur}(X_i)\geq|O\cap X_i|\cdot\text{lr}(X_i)$，于是
$$ \frac{|G\cap X_i|}{|O\cap X_i|}\geq\frac{\text{lr}(X_i)}{\text{ur}(X_i)}
\geq\min\left\{\frac{\text{lr}(X_i)}{\text{ur}(X_i)}\right\}
\triangleq q(X,\mathcal I) $$
$f(G)/f(O)$ 中每一项之比都大于 $q$，所以两者之比大于 $q$，也就是
$$ \frac{a_1}{b_1}\geq q,\frac{a_2}{b_2}\geq q
\Rightarrow\frac{a_1+a_2}{b_1+b_2}\geq q $$
原文证明了这个下界是紧的，等号成立的一个条件是，找出使上下秩最小的子集 $S_0$，令 $S_0$ 中的元素的收益函数为1，不在 $S_0$ 中的元素的收益为0。


#  参考
1. [怎么理解次模函数 submodular function？-知乎](https://www.zhihu.com/question/34720027/answer/79260358)
2. [次模函数](https://spiritsaway.info/submodual.html)
3. [equivalence of definitions of a submodular function on sets -stackexchange](https://math.stackexchange.com/a/1976562/1242938)
4. [怎么理解拟阵（matroid）？-知乎](https://www.zhihu.com/question/316879980/answer/740466359)
5. 算法导论 16.4：贪心算法的理论基础

[1] Submodular optimization problems and greedy strategies: A survey.  
[2] An Analysis of the Greedy Heuristic for Independence Systems.  
- [图解：什么是最小生成树？ -知乎](https://zhuanlan.zhihu.com/p/136387766)  
