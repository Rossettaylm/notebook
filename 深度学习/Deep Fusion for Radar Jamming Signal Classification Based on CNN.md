# Deep Fusion for Radar Jamming Signal Classification Based on CNN

作者：GUANGQING SHAO, YUSHI CHEN, YINSHENG WEI

时间：2021年11月7日

## 一、创新点

1. 提出了有效的特征融合方法，通过1D-CNN提取干扰信号的模值，相位，实部，虚部等特征；通过2D-CNN提取经过STFT后的时频图特征。
2. 通过对特征融合的方式改善了分类的准确性。
3. 提出软标签平滑(soft label smoothing)的方法，来增强分类的表现，以及模型的泛化能力。



## 二、网络结构

![image-20211107204721164](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211107204721164.png)

* 1D-CNN用于提取原始信号的deep features。
  将原始信号通过STFT得到时频图，2D-CNN用于提取时频光谱图中的deep features。
  其中GAP表示Global-Average-Pooling，用GAP代替传统的全连接层目的是使最后卷积层的特征图更加匹配干扰信号的种类，同时为了防止过拟合采用了dropout层。

* 为了融合1D-CNN和2D-CNN提取的特征，将两个网络提取的特征拼接送入全连接DNN，最后采用softmax进行分类。
* soft label smoothing，为了改善过拟合

## 三、参数设置

![image-20211110163205473](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211110163205473.png)

![image-20211110163227804](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211110163227804.png)

## 四、结果

![image-20211110163620967](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211110163620967.png)

![image-20211110163636516](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211110163636516.png)

经过与SVM，RF，普通CNN的对比试验，得出模型的优越性。
