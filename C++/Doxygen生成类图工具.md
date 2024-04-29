# Doxygen生成类图工具

安装

```text
sudo apt install graphviz  # 用于生成代码关系图 
sudo apt install doxygen
```

使用

```shell
$ cd CODE_DIR
$ doxygen -g Doxygen.config  # 生成配置文件 
$ vi Doxygen.config          # 修改配置文件

RECURSIVE              = YES
INPUT = "/path/src" \
	 	"/path/include"
OUTPUT_DIRECTORY = "/outputpath"
GENERATE_LATEX = NO

$ doxygen Doxygen.config     # 根据代码生成文档
```

打开

进入生成的html文件夹中，打开index.html