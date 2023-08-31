# 线性调频信号

Date:	2021年11月19日

------------------

## 一、公式推导

典型的线性调频信号为：
$$
s_1(t) = rect(\frac t\tau) e^{j2\pi(f_0t + \frac k2 t^2)}
$$
其中，$f_0$为调频中心频率，$k$为调频斜率，存在$B_w = \abs {k} \tau$ 。
$$
s_1(t) = s(t) e^{j2\pi f_0 t}
$$
$s(t)$是线性调频信号的复包络，线性调频信号的频谱由它的复包络决定。
$$
s(t) = rect(\frac t\tau) e^{j\pi kt^2}
$$
信号的瞬时频率为$\frac 1{2\pi} \frac {\mathrm{d} \phi}{\mathrm{d} t} = \frac 1{2\pi} \frac {\mathrm{d} (\pi k t^2)}{\mathrm{d} t} = kt$

## 二、MATLAB代码

```matlab
close all;clc;clear all;
%%
%   LFM ：linear frequency modulation
%%
B = 70e6; % 带宽70MHz
tau = 2e-6; % 脉宽20us
Fs = 100e6; % 采样率
N = tau*Fs;
t = -tau/2:1/Fs:tau/2-1/Fs; % 在脉冲宽度内限定时间范围，相当于rect(t/τ)
K = B/tau;
%%
St = exp(j*pi*K*t.^2); %信号
theta = pi*K*t.^2; %信号相位
f = K*t; %信号频率
 
figure
subplot(2,2,1);plot(real(St));title('信号实部');
subplot(2,2,2);plot(imag(St));title('信号虚部');
subplot(2,2,3);plot(theta);title('信号相位 °');
subplot(2,2,4);plot(f);title('信号频率 Hz');
 
figure;
plot(abs(fftshift(fft(St))));title('信号频谱');
```

## 三、仿真波形

![img](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image16373028853810.png)

![img](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image16373029166760.png)



