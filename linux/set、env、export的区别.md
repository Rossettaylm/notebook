

### Linux中set,env和export这三个命令的区别

set命令显示**当前shell**的变量，包括当前用户的变量，所以其主要用来设置sh的参数与选项;

env命令显示**当前用户**的变量，所以其主要用来构建的环境中运行命令;

export命令显示当前**导出成用户变量的shell变量**。

例子：使用env命令显示所有环境变量

例子2:使用set命令显示所有本地定义的Shell变量

set可以设置某个环境变量的值。清除环境变量的值用unset命令。如果未指定值，则该变量值将被 设为NULL

例子3:删除环境变量TEST

例子4:设置一个新的环境变量WELCOME