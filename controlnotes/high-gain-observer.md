被控对象
$$\begin{aligned}
\dot{x}_1 =& x_2 \\
\dot{x}_2 =& \phi(x,u) \\
y =& x_1
\end{aligned}$$
设 $u=\gamma(x)$ 能使系统稳定，设计观测器

$$\begin{aligned}
& \dot{\hat{x}}_1=\hat{x}_2+h_1(y-\hat{x}_1) \\
& \dot{\hat{x}}_2=\phi_0(\hat{x},u)+h_2(y-\hat{x}_1) \\
\end{aligned}$$

其中 $\phi_0$ 为 $\phi$ 的已知部分。观测误差

$$\begin{aligned}
& \dot{\tilde{x}}_1=-h_1\tilde{x}_1+\tilde{x}_2 \\
& \dot{\tilde{x}}_2=-h_2\tilde{x}_1+\delta(x,\tilde{x}) \\
\end{aligned}$$

其中 $\delta(x,\tilde{x})=\phi_0(\hat{x},\gamma(x))-\phi(x,\gamma(x))$。

$$
\tilde{x}=Ax+B\delta \\
\begin{bmatrix}
\dot{\tilde{x}}_1 \\ \dot{\tilde{x}}_2
\end{bmatrix}
=\begin{bmatrix}
-h_1 & 1 \\ -h_2 & 0
\end{bmatrix}
\begin{bmatrix}
\tilde{x}_1 \\ \tilde{x}_2
\end{bmatrix}
+\begin{bmatrix}
0 \\ 1
\end{bmatrix}\delta
$$

$$\begin{aligned}
G_0(s) =& \frac{1}{s^2+h_1s+h_2}
\begin{bmatrix} 1 \\ s+h_1 \end{bmatrix} \\
\lim_{\epsilon\rightarrow 0}G_0(s)
=& \frac{\epsilon}{(\epsilon s)^2+\alpha_1\epsilon s+\alpha_2}
\begin{bmatrix} \epsilon \\ \epsilon s+\alpha_1 \end{bmatrix} \\
\end{aligned}$$
