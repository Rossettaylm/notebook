# LeNet网络基本结构

## 一、模型基本结构

![LeNet](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/20190822140134868.png)

LeNet共有七层，每层都包含可训练参数。

### （1）input层（32×32）

数据input层，输入图像的尺寸一律归一化为32×32 。

### （2）Conv2d 1 （卷积层）

输入图片：32×32
卷积核大小：5×5
输入层数：1
卷积核种类（输出层数）：6
参数（weight和bias）：weight=(6， 1， 5，5)    bias=(6)
输出特征图大小：28×28
输出神经元个数：6 × 28 × 28
每个像素都与输入图像中的5 × 5个像素和1个bias有连接，故有(5 × 5 + 1) × 6 × 28 × 28 = 122304个连接

**特征图大小的计算公式如下：**

一般取$padding = \frac {kernel - 1}{2}$，如kernel = 3, padding = 1, stride = 2进行下采样
$$
output = \lfloor \frac{input + 2 \times padding - kernel}{stride} \rfloor + 1
$$
**input~h~ = 32, padding~h~ = 0, kernel~h~ = 5, stride~h~ = 1**
有output~h~ = (32 + 2 × 0 - 5) / 1 + 1 = 28，同理output~w~ = 28，得到输出特征图大小为28 × 28

### （3）MaxPool2d 1 （最大池化层）

输入大小：28 × 28
采样区域：2 × 2
步幅：2
采样种类（输入层数）：6
输出特征图大小：14 × 14
输出神经元个数：6 × 14 × 14
连接数：(2 × 2 +1) × 6 × 14 × 14 = 5880

每次步进两个单位，同时池化窗口大小为2，故将input~h~和input~w~缩小一半

### （4）Conv2d 2 （卷积层）

输入特征图大小：14×14
卷积核大小：5×5
输入层数：6
卷积核种类（输出层数）：16
参数（weight和bias）：weight=(16， 6， 5，5)    bias=(16)
输出特征图大小：10 × 10
输出神经元个数：16 × 10 × 10

**input~h~ = 14, padding~h~ = 0, kernel~h~ = 5, stride~h~ = 1**
有output~h~ = (14 + 2 × 0 - 5) / 1 + 1 = 10，同理output~w~ = 10，得到输出特征图大小为10 × 10

### （5）MaxPool2d 2 （最大池化层）

输入大小：10 × 10
采样区域：2 × 2
采样种类（输入层数）：16
输出16个特征图(feature map)，每个特征图的大小：5 × 5 (10 / 2)
神经元数量：5 × 5 × 16

### （6）Conv2d 3 （卷积层）

输入特征图大小：5 × 5
卷积核大小：5×5
输入层数：16
卷积核种类（输出层数）：120
参数（weight和bias）：weight=(120， 16， 5，5)    bias=(120)
输出特征图大小：1 × 1
输出神经元个数：120 × 1 × 1 （得到了一个120维的向量，转为全连接层）

**input~h~ = 5, padding~h~ = 0, kernel~h~ = 5, stride~h~ = 1**
有output~h~ = (5 + 2 × 0 - 5) / 1 + 1 = 1，同理output~w~ = 1，得到输出特征图大小为1 × 1

### （7）Linear1 （全连接层）

输入大小：120
参数（weight和bias）：weight=(84，120)    bias=(84)
激活函数：sigmoid
输出大小：84

### （8）Linear2 （output层）

输入大小：84
参数（weight和bias）：weight=(10，84)    bias=(10)
激活函数：sigmoid
输出大小：10

![image-20211024224950705](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211024224950705.png)