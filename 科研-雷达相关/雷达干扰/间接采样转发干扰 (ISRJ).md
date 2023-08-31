间接采样转发干扰 (ISRJ):
$$
J(t) = \sum_{n=1}^N rect((t - \tau/2-(n-1)T_s)/T) e^{j2\pi(f_0t + \frac K2 t^2)} \newline
\text{矩形窗中心：}t_0 = \tau/2+(n-1)T_s \newline
\text{矩形窗宽度：}T = T
$$
其中，$\tau$是间歇采样的脉冲宽度，$T$是雷达信号的脉宽，$T_s$是采样周期，用$\frac{\tau}{T_s}$表示间歇采样占空比。