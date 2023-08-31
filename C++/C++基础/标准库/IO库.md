# IO库

Date：2021年12月10日

> c++通过一组定义在标准库中的类型来处理IO。这些类型支持从设备读取、写入数据，设备可以是文件、控制台窗口等。还有些支持从内存IO，如向string变量读取、写入数据

部分IO库设施：

* `istream`，输入流
* `ostream`，输出流
* `cin`，一个`istream`对象，从标准输入读入数据
* `cout`，一个`ostream`对象，向标准输出写入数据
* `cerr`，一个`ostream`对象，用于输出程序错误消息到标准输出
* `>>`运算符，从一个`istream`对象读取输入数据
* `<<`运算符，从一个`ostream`对象写入输出数据
* `getline(istream, string)`函数，从一个给定的`istream`读取一行数据，存入一个`string`对象中

[TOC]



-----------

## 一、IO类

| 头文件   | 类型                                                         |
| :------- | ------------------------------------------------------------ |
| iostream | istream，wistream从流读取数据；ostream，wostream向流写入数据；iostream，wiostream读写流 |
| fstream  | ifstream，wiftream从文件读取数据；ofstream，wofstream向文件写入数据；fstream，wfstream读写文件 |
| sstream  | istringstream，wistringstream从string读取数据；ostringstream，wostringstream向string写入数据；stringstream，wstringstream读写string |

**其中，开头带w的类型是为了操作`wchar_t`类型的数据。**宽字符版本的对象应该和普通char版本类型的对象定义在同一个头文件中

----------

### 1、IO对象无拷贝或者赋值

* 我们不能对IO对象进行拷贝或者赋值，所以**IO对象的形参和返回类型都需要用引用传递**
* 读写一个IO对象会改变其值，所以**传递和返回引用不能是const**

---------

### 2、条件状态

IO类定义的一些函数和标志，帮助我们访问和操纵流的**条件状态（condition state）**

![image-20211211150928477](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211211150928477.png)

![image-20211211150940682](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211211150940682.png)

通常，while循环检查>>表达式返回的流的状态

```c++
while (cin >> word)
    // ok：读操作成功
```

----------

### 3、管理输出缓冲

> 每个输出流都管理着一个缓冲区用来保存程序读写的数据。
> 文本串可能被直接打印或者保存在缓冲区中，缓冲区机制可以让操作系统将程序的多个输出操作组合成单一的系统级写操作，带来性能提升。

导致缓冲刷新的原因有：

* 程序正常结束，作为main函数的return的一部分，缓冲刷新被执行
* 缓冲区满时，需要刷新缓冲
* 使用操作符刷新，如endl, ends, flush
* 每个输出操作结束后，用操作符unitbuf来设置流内部状态，清空缓冲区。默认情况下，cerr是设置unitbuf的，故会即使刷新
* 一个输出流可能被关联到另一个流。当读写被关联的流时，关联到的流的缓冲区会被刷新。如cin和cerr都关联到cout，因此读cin或写cerr的操作会刷新cout的缓冲区

#### 刷新输出缓冲区

* `cout << "hi!" << endl;	//输出hi和一个换行，并刷新缓冲区`
* `cout << "hi!" << ends;	//输出hi和一个空字符，并刷新缓冲区`
* `cout << "hi!" << flush;	//输出hi，并刷新缓冲区`

#### unitbuf操纵符

> 使用unitbuf操作符可以在每次操作后立即刷新缓冲区，即每次写操作之后都进行一次flush操作
>
> nounitbuf操纵符则重置流，使其恢复使用正常的系统管理的缓冲区刷新机制

```c++
cout << unitbuf;	// 所有输出操作后都会立即刷新缓冲区
cout << nounitbuf;	// 回到正常的缓冲方式
```

#### 关联输入和输出流

已知cerr和cin关联到了cout，所以`cin >> ival;`会导致cout的缓冲区被刷新。

Tips：

**交互系统应该关联输入流和输出流，保证所有输出在读操作之前被打印出来**

如何进行关联？

* `ostream *tie()`，不带参数，返回指向输出流的指针，本对象如果关联到输出流，则返回其指针，如果没有则返回空指针
* `void tie(ostream *of)`，接受一个ostream的指针，将本对象关联到指针所指的输出流

```c++
// x是一个IO流对象
ostream *px = x.tie();	// 类似于get函数，获得x已绑定的输出流,
x.tie(&o);				// 将x与输出流o绑定
```

**既可以将istream绑定到ostream，也可以将ostream绑定到ostream**，每个流最多关联到一个流，但是多个流可以同时关联到同一个ostream

-----------------

## 二、文件输入输出

通过ifstream和ofstream可以实现对文件的读写操作，除了IO操作符之外，还定义了以下特有的操作。

![image-20211214203241085](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211214203241085.png)

-----------

### 1、使用文件流对象

**首先定义一个文件流对象，并将对象和文件关联起来，完成对文件的读写操作**

```c++
ifstream in(ifile);	// 构造一个ifstream并打开给定文件
ofstream out;		// 输出文件流未关联到任何文件
```

Tips：

在使用基类对象的时候，可以用一个继承类型的对象来进行代替。
如果一个函数接受ostream&的参数，可以传入一个ofstream来对文件进行写操作 

#### 成员函数open和close

如果我们定义了一个空文件流对象，可以调用open函数来将它和文件关联起来：

```c++
ifstream in(ifile);	// 构建一个ifstream并打开给定文件
ofstream out;		// 输出文件流未与任何文件相关联
out.open(ifile + ".copy");	// 打开指定文件
```

如果调用open失败，failbit会被置位。
**通常需要进行open是否成功的检测**：`if (out)`，一旦一个文件打开，就会和对应文件进行关联。如果调用open失败，会导致failbit被置位。为了将文件流关联到另外一个文件，必须先关闭已经关联的文件。

```c++
in.close();	// 关闭一个文件
in.open(ifile + "2");	// 打开另一个文件
```

#### 自动构造和析构

如下程序对要处理的文件列表进行处理：

```c++
for (auto p = i + 1; p != i + length; ++p)
{
    ifstream input(*p);	// 创建输出流并打开文件
    if (input)	// 如果文件打开成功，“处理”此文件
    {
        process(input);
    }
    else
        cerr << "couldn't open: " + string(*p);
}	// 每个循环步input都会离开作用域，因此会被销毁，自动调用close函数
```

#### 文件流操作方法get和peek

* ifstream.get()相当于读取一个字符并从流中移除这个字符；ifstream.peek()相当于读取下一个字符得到它的副本
* peek函数仅仅只是预读取，可用来判断下一个字符类型来使用
* <u>循环读取文件到结尾的判断条件</u>可以设置为`infile.peek() != EOF`

------------

### 2、文件模式

![image-20211214222300471](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211214222300471.png)

ifstream默认以in模式打开；ofstream默认以out模式打开且截断；fstream默认以in和out模式打开。

以out模式打开ofstream时，文件的内容会被丢弃，**阻止一个ofstream清空给定文件内容的方法是同时指定app模式或者in模式。**

```c++
ofstream out("file1");	// out模式下file1都被截断
ofstream out2("file1", ofstream::out);

ofstream app("file2", ofstream::app);	// 隐含为输出模式并保留文件内容，不截断文件
ofstream app2("file2", ofstream::out | ofstream::app);
```

----------

### 3、string流

> sstream头文件定义了三个类型来支持内存IO，可以向string写入并读取数据

istringstream从string读取数据，ostringstream向string写入数据，头文件stringstream既可以从string读取数据也可以向string写数据。

![image-20211216130207586](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211216130207586.png)

**当一些工作不是对整行进行处理，而是处理行内的单个单词时，可以考虑用istringstream**

**当我们逐步构造输出，希望最后一起打印时，ostringstream是很有用的**



## 小结：

![image-20211216163200043](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20211216163200043.png)
