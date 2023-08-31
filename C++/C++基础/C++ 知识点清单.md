* 基础： 
  * 数据类型、变量类型、常量、修饰符、字符串
  * 判断、循环、函数
  * 类、对象、继承、多态等
* 指针、引用、数组、内存
* 引用与指针区别
* 虚机制：虚函数、虚函数表、纯虚函数
* 四种类型转换：static_cast, dynamic_cast, const_cast, reinterpret_cast
* STL部分容器的实现原理，如 vector、deque、map、hashmap
* 智能指针原理：引用计数、RAII（资源获取即初始化）思想
* 智能指针使用：shared_ptr、weak_ptr、unique_ptr等
* 编译链接机制、内存布局（memory layout）、对象模型
* C++11 部分新特性，比如右值引用、完美转发等
* 模板特化、偏特化，萃取 traits 技巧
* C 和 C++ 的一些区别，比如 new、delete 和 malloc、free 的区别
* 一些关键字的作用：static、const、volatile、extern
* 继承、虚继承、菱形继承等
* 多态： 动态绑定，静态多态
* 重写、重载

----------------





- local static	函数内的静态变量，生命周期是整个程序，作用域只在函数内

- static in a cpp       一个cpp文件中的静态变量，main函数之外，其作用域是这个cpp文件内，无法被外部声明（extern）
- static in a class
  - static member variable	属于整个类的静态成员变量，类内只有声明，所以需要在类外定义，无法在生成一个实例的时候进行定义
  - static member function    属于整个类的静态成员函数，可以通过类作用域符号或者实例调用，但是函数内只能使用静态成员变量
