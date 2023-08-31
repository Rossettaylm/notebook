## shell 扩展

- \*扩展 通配符
- ~扩展 家目录
- 算数扩展 $((5+2))
- 花括号扩展 {A..Z} {0,1}
- 参数扩展 $USER 通过 printenv 查看 
- 命令替换 以命令的结果作为扩展 echo $(ls)
- 历史扩展 !88 历史记录行号    !! 上一条命令    !string 上一条包含 string 的命令

## 引用

- 在""中防止 shell 截断字符串 如 ls "queshi run"
- 在""中可以使用参数扩展$USER，算术扩展$((5+2))，命令替换$(ls)
- 未经引用的空格，制表，换行会被视为分隔符，不会被视为文本的一部分
  - echo $(cal) 参数为 38 个，输出结果为:
    March 2022 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31
  - echo "$(cal)" 参数为 1 个，输出结果为:
    March 2022  
    Su Mo Tu We Th Fr Sa  
     1 2 3 4 5  
     6 7 8 9 10 11 12  
    13 14 15 16 17 18 19  
    20 21 22 23 24 *2*5 26  
    27 28 29 30 31
- 单引号''禁用所有扩展
- \表示对字符进行转义 下列解释了转义序列
  - echo -e "Time's up\a"
  - echo "Time's up"$'\a'

### 对密码 hash 加密

sudo apt install whois
mkpasswd -m sha-512 > pw

## 进程控制

- 查看进程(process) ps aux, top(动态)
- 将进程置于后台，即在命令后加上一个&符号，返回作业编号和 PID sleep 10 &
- 查看启动的作业列表 jobs
- 使后台作业返回前台(frontground) fg %1 百分号加上作业编号，如果只有一个作业，作业编号可选
- 停止进程(suspend) 使用 ctrl+z --- 使进程在后台恢复运行(background) bg %1 类似于 fg 命令
- 杀死/终止进程(terminate) kill PID 实际上 kill 是对操作系统发送信号，如 ctrl+c 发送 INT(interupt) 中断信号，kill -l 显示可用信号
- 使用 kill 发送信号 kill -signal PID signal 为信号的编号或名称 kill -1 4234; kill -INT 4324 信号列表见《The Linux Command Line》P97
- 使用 kill 向多个进程发送信号 killall [-u user] [-signal] name

## 关闭系统

- halt
- poweroff
- reboot
- shutdown -选项 -时间 如 shutdown -r now 表示现在重启 shutdown -h now 现在悬挂

## 环境

- printenv 只显示全部或者部分**环境变量** printenv USER 或不加参数
- set 显示**shell 变量**或者**环境变量** set (结果按顺序排列)
- echo 查看变量内容 echo $HOME
- alias 查看命令别名

shell 中含有两种基本类型数据：shell 变量和环境变量  
shell 中的程序化数据：alias 和 shell 函数

shell 有两种启动方式：

1. 登录 shell 会话（login shell session）：需要用户名和密码 读取的启动文件顺序如下
   - /etc/profile 全局配置脚本
   - ~/.bash_profile 个人启动文件，扩展或者覆盖全局配置
   - ~/.bash_login 如果没有找到.bash_profile，则会读取
   - ~/.profile 如果上述两个都没找到，则会读取
2. 非登录 shell 会话（non-login shell session）：在 gui 中启动终端 读取的配置文件顺序如下
   - /etc/bash.bashrc 应用于所有用户的全局配置脚本
   - ~/.bashrc 用户的个人配置文件，可覆盖或扩展全局配置  
     **非登录 shell 会话同时还会继承登录 shell 会话的环境**,因为.bashrc 基本上会被读取，所以在这里修改



----------------

## 存储设备

* RAID （redundant Arrays of independent disk）独立磁盘冗余阵列
* LVM （Logical Volume Manager）逻辑卷管理器

/etc/fstab (file system table) 中保存了系统在引导时挂载的各种设备

#### mount

mount [-t 文件系统类型] 挂载设备 挂载点

![image-20220327231720468](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220327231720468.png)

**设备名称 on 挂载目录 type 文件系统 （选项）**

卸载命令（需要root权限）

umount 设备名称 

#### buffer的作用

> 提高计算机的速度

计算机与低速设备进行IO时，会将数据先写入高速缓冲区RAM，再写入挂载的IO设备。

#### 为何要进行设备的卸载？

卸载设备会将所有的剩余数据写入设备，以便其能够安全的移除。如果移除设备前没有卸载设备，有可能造成数据并没有完全写入设备的可能，有几率导致文件系统损坏。

#### Linux设备命名规则

- /dev/fd*   软盘驱动器
- /dev/hd*  旧式PATA硬盘
- /dev/lp*    打印机
- /dev/sd*   SCSI磁盘（所有类似磁盘的设备）
- /dev/sr*    光驱

命名规则：主板上有两个IDE通道，每个通道上可以连接两个驱动器，通道上的第一个驱动器（主设备），第二个驱动器（从设备）

/dev/sda2 表示第一个通道的主设备的第2个分区；

/dev/sdc3 表示第二个通道的主设备的第3个分区。

**确定可移动设备被接入系统后的名称**（可对系统进行实时监测）

```shell
sudo tail -f /var/log/messages
```

#### 查看修改分区

```shell
sudo fdisk /dev/sda
```

#### 创建文件系统

```shell
sudo mkfs -t ext4 /dev/sdb1
```

将/dev/sdb1创建尾ext4类型的文件系统，进行格式化

#### 在设备间直接移动数据(data definition)

```
dd if=input_file of=output_file [bs=block_size [count=blocks]]
```

将一个设备驱动器的内容复制到另一个设备驱动

```shell
dd if=/dev/sda1 of=/dev/sdb2
```

如：创建CD-ROM的ISO映像文件操作

```
dd if=/dev/cdrom of=ubuntu.iso
```

-----------------

## 联网

- ping www.baidu.com 向指定主机发送ICMP ECHO_REQUEST的特殊网络分组，根据响应测试网络是否连通
- traceroute/tracepath www.baidu.com 查看到达指定主机的路由及其状态信息
- ip a 查看系统网络接口和路由表 `ip a | grep state `查看网卡启用状态

![image-20220329001326560](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329001326560.png)

其中，lo为（loopback interface）是一个虚拟接口，系统用来和自己通信；eth0是以太网接口

![image-20220329001440385](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329001440385.png)

查看主机的ip地址，其中eth0启用了DHCP，即动态主机配置协议，有效ip地址证明其工作正常：

![image-20220329001556784](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329001556784.png)

- netstat命令查看各种网络设置和统计信息

![image-20220329002144874](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329002144874.png)

![image-20220329002157635](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329002157635.png)

netstat -r 显示分组的网络路由表

![image-20220329002257775](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329002257775.png)

#### 通过网络传输文件 ftp和wget

- ftp（基于FTP协议，File Transfer Protocol）

- wget 命令行下载工具（文件或是网页都可以）

#### 与远程主机连接 ssh

- ssh的作用
  - 认证远程主机的身份是否属实（避免了“中间人”的攻击）
  - 加密本地主机和远程主机之间的所有通信

- 将远程主机的命令输出结果放到本地显示

![image-20220329003534918](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329003534918.png)

也可以用单引号，将输出结果放到远程的服务上，而不是本机

![image-20220329003743369](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329003743369.png)

#### scp和sftp

- scp 通过ssh copy 可以实现本地和远程之间的文件传输，远程主机名和地址之间用冒号：连接

![image-20220329004441511](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329004441511.png)

- sftp 在传输信息时取代了ftp使用的明文形式，而是采用加密隧道。远程主机不需要ftp服务器，只需要ssh服务器就够了。通过ssh文件传输协议（SFTP://）开头的地址可以对存储在运行着SSH服务器上的远程主机的文件进行操作

![image-20220329004319609](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329004319609.png)

----------

## 查找文件

* locate：按照路径名查找文件
* find：在目录中查找文件
* xargs：通过标准输入构建并执行命令
* touch：修改文件时间
* stat：显示文件或文件系统状态

```shell
find [directory] -type [file_type] -name "*.jpg" -size +1M
```

- -type 指定文件类型
- -name 以通配符匹配文件
- -size 指定文件的大小

| file_type | 文件类型           |
| --------- | ------------------ |
| c         | 字符设备文件       |
| b         | 块设备文件         |
| d         | 目录               |
| f         | 普通文件           |
| l         | 符号链接（软链接） |



```shell
// ls file1
// ls file2
find [directory] -type [file_type] -exec [command] {} \;
// 合并参数，改成一条执行命令 ls file1 file2
find [directory] -type [file_type] -exec [command] {} + 
```

将find找到的 作为command的参数执行

![image-20220329230402488](https://rossetta-typora-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/image-20220329230402488.png)

```shell
find \( expression \) -or \( expression \)
```

- xargs 能过自动分批处理传来的参数，不超过shell最大限制

```
find . -type d -print0 | xargs -null ls -l 
// 以字符0作为分隔符，保证有空格的文件被正确执行
```

