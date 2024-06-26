# C语言知识点

> 作者：Rossetta

## 一、网络传输三大注意事项

### 1.1 字节对齐

#### 1.1.1 目的

* 保证不同平台之间的通用性
* 计算机一次只能取出总线位数32位/64位的数据，保证每次读取数据能够一次就从计算机中读取出来

#### 1.1.2 规则

* **最大对齐值**：32位平台为4,64位平台为8；可通过人为设置`#pragma pack(8)`。
* 结构体成员偏移地址填充：结构体第一个成员的**偏移量（offset）**为0，以后每个成员相对于结构体首地址的 offset 都是**该成员大小与最大对齐值中较小那个**的整数倍，如有需要编译器会在成员之间加上填充字节。
* 结构体的总大小：为**有效对齐值**的整数倍，如有需要编译器会在最末一个成员之后加上填充字节。
* 结构体的有效对齐值是最大成员的有效对齐值；基本变量的有效对齐值为其字节数。

```c++
struct myStruct {
  char a; 	// 0 -> 1 -> 8
  double b; // 8 -> 16（起始地址应该为对齐字节数的整数倍
  int c;		// 16 -> 20 
  					// 20 -> 24	（总大小应该为sizeof(double)的整数倍）
}
```

上述结构体中，首先a的起始偏移地址为0，占用了1个字节

b的起始地址要能够被`8字节`整除，所以需要填补7个字节，起始偏移地址为8，占用8个字节

c的起始地址要能够被`8字节`整除，起始偏移地址为16，占用4个字节

最后，20个字节不满足为最大字节数`sizeof(double)`的整数倍，需要填充4个字节，共24个字节

#### 1.1.3 人为的字节对齐

通过`#pragma pack(n)`指定进行n字节对齐，一般网络传输中，都需要指定进行1字节对齐

-------------

### 1.2 网络字节序和主机字节序

不同平台下，数据存储的方法有大端法和小端法两种，大端法就是正常顺序，小端法反顺序

如一个`int`变量`0x01020304`在不同的机器下存储方式为：

大端法：`0x01020304`

小端法：`0x04030201`

其中，==网络字节序是通过大端法==来传输的，而==Linux和Windows都是小端法==（倒着存）表示数据的

在网络通信中，数据统一按照大端序，即网络字节序。在进行数据传输时，发送数据时，将数据转成网络字节序（htons、htonl），接受数据时，再将数据转回主机子节序（ntohl、ntohs）。这样就避免了不同字节序造成的数据影响。

其中，`htonl`函数表示`host to network unsigned long`表示一个`uint32`类型从主机字节序到网络字节序的转换

---------------------

### 1.3 数据类型大小

|            | win32 | win64 | linux32 | linux64 |
| ---------- | ----- | ----- | ------- | ------- |
| char       | 1     | 1     | 1       | 1       |
| short      | 2     | 2     | 2       | 2       |
| int        | 4     | 4     | 4       | 4       |
| long       | 4     | ==4== | 4       | ==8==   |
| long long  | 8     | 8     | 8       | 8       |
| **size_t** | **4** | **4** | **8**   | **8**   |

-----------

## 二、变长数组 Variable-Length Array

#### 2.1 简介

```c++
struct VLC {
	int a;
  int b;
  int c[];	// 等价于int c[0];
};
```

上述结构体中，`sizeof(VLC) = 8`，其中`int c[]`不占用空间

* `int c[]`称之为变长数组，数组不占用空间，因为数组名不是指针，只是代表了**不可修改的地址常量**

```c++
int a[1] = {1};	//a为数组名，是一个地址常量

int temp = 1;
int *b = &temp;	//b为指针，指向temp的地址，是可变的

a = b;	// 错误！a不能被赋值
b = a;	// 正确！b为指针，可以被赋值
```

* 对于变长数据的大小，我们可以进行**动态分配**

#### 2.2 优缺点

##### 2.2.1 优点

当需要在一个结构体中开辟一段空间时，使用固定大小的数组容易浪费，使用指针需要额外开辟堆空间，此时可以使用变长数组

```C++
// 1.固定分配大小
#define maxLen 1000
struct data {
  int a;
  int b[maxLen];
};

// 2.使用指针
struct data {
  int a;
  int *b;
};

// 3.使用变长数组😍
struct data {
  int a;
  int b[];
};	
```

##### 2.2.2 缺点

变长数组只能放在一个结构体的**最后面**

#### 2.3 用法

变长数组顾名思义，即==长度为变量的数组==

##### 2.3.1 普通的变长数组

```c++
int n = 10;	
int a[n];	// 通过变量来初始化一个数组
for (int i = 0; i < n; ++i) {
  a[i] = i;
}
```

##### 2.3.2 结构体中的变长数组

```c++
struct Data {
  int a;
  int b[];
};

int n = 10;
Data *p_data = (Data *)malloc(sizeof(Data) + n * sizeof(int));	//动态分配大小为n的动态数组
for (int i = 0; i < n; ++i) {
  p_data->b[i] = i;
}

free(p_data);
```

-----

## 三、通过位与运算实现取模

在某些情况下，为了提高代码的运行效率，可以使用位运算来代替模运算。使用位运算代替模运算的基本思路是利用位运算的性质，将模数转换成二进制形式，然后通过位运算来实现取模。

具体来说，**如果模数是2的幂次方，可以使用位与运算代替取模运算**。假设模数为n，那么n的二进制表示中只有一个1，其余位都为0，例如n=8时，**二进制表示为1000。此时，对一个数x进行取模，可以使用`x & (n-1)`来代替x % n。**

例如，假设n=8，x=13，那么``x & (n-1)``的结果为`13 & (8-1) = 5`，即`13 % 8 = 5`。

需要注意的是，这种方法只适用于模数是2的幂次方的情况。如果模数不是2的幂次方，则不能使用位与运算代替取模运算。

## 四、位域结构体

```c++
struct bitset {
  int val1 : 4;
  int val2 : 4;
  int val3 : 4;
  int val4 : 4;
}
```

每个成员变量占据``4位``，加起来总共一个`short(2字节) 4 * 4 = 16bit = 2Byte`

每个位都是从低位开始填充，如上述`val1`取第一个字节的低4位，`val2`取第一个字节的高4位

```c++
bitset b;
b.val1 = 0;	// 第一个字节低4位
b.val2 = 1;	// 第一个字节高4位
b.val3 = 2; // 第二个字节低4位
b.val4 = 3; // 第二个字节高4位

```

上述`b`对象在内存中的表现形式为`0x1032`

---------

## 五、指向二维数组的指针

```c++
int arr[3][4] = {};

int (*ptr1)[3][4] = &arr;		// &arr表示取二维数组的地址，ptr1类型是二维数组指针
int (*ptr2)[4] = arr;			// arr表示数组首地址，即数组第一个元素的地址，二维数组的第一个元素为指向int [4]的指针

sizeof(arr) == sizeof(int) * 3 * 4;
sizeof(*arr) == sizeof(int) * 4;     // 同理
```

