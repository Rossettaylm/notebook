# Lua基本语法

## 1.Lua的编译

- lua -- 脚本解释器
- luac -- 脚本编译器，将.lua脚本编译成字节码，通过lua脚本解释器进行执行

## 2.Lua的类型与基本运算符号

lua中默认变量为全局，使用local将变量声明为局部变量

### 2.1 nil

空类型，如果一个未被定义的类型就是nil类型

```lua
a = 1
b = "str"
print(c)

// c为nil类型
```

### 2.2 number

lua中所有的数类型，如`int`,`double`,`unsigned long`都视为`number`类型

### 2.3 string

用单引号或者双引号来表示字符串，使用`[[]]`来表示一段原生的字符串，不做任何转义字符处理

#### 如何拼接字符串？

```lua
a = "hello"
b = "world"
c = a..b
print(c)
```

#### 如何获取字符串长度?

```lua
print(#c)
```

#### 字符串和数字互相转换

```lua
tostring()
tonumber()
```

### 2.4 bool

!**lua中不等于是通过`~=`来进行表示的**

```lua
a = true
b = false
a > b
a < b
a ~= b
```

### 2.5 运算符号

其中有几个特殊的

- `~=`:不等于
- `^`:幂方
- lua中不支持自增和自检运算符
- `and`、`or`、`not`:与、或、非

```lua
a = nil -- nil和false代表假
b = 0 -- 非nil，故为真
print(a and b) -- nil and 0 为假，输出为nil
print(a or b) -- nil or 0 ，其中0为真，输出为0
print(not a) -- not nil 为真 true

print(b > 0 and "yes" or "no") -- 类似于三目运算符，判断b > 0为真则输出"yes"，否则输出为"no"

1 and "yes" -- 两者都为真, 输出后面一个,取"yes"
nil and "no" -- nil为false，输出为false
1 or "yes" -- 1为真，且取或，直接输出1
```



## 3.函数

```lua
function func(a, b)
    print(a, b)
end

// 或者是

func = function(a, b)
    print(a, b)
    return a, b
end
```

## 4.容器

### 4.1 table

!**lua中下标是从1开始的**

```lua
a = {1, "str", 2.2, nil, function() end}
print(a[2])]


a = {
    a = 1,
    b = "str",
    c = function()
    end,
    [",;"] = "hello"
}
print(a["a"])
print(a.a)		// 上述两种方法都是访问table a中key a的方法，实际上以字符串作为下标
```

#### 支持越界访问

```lua
a = {3.2, "who", function() end};
a[5] // nil
a[5] = 123

print(#a) //获取table的长度
```

#### table中一些内置方法

- `table.insert`
- `table.remove`

#### 全局表

`_G`：全局表

lua中所有的全局变量都存在于全局表`_G`中

#### 迭代table

```lua
-- 以数字索引为下标的
table = {3, "str", function() end, nil}
for i=1,#table do
    print(i, table[i])
end

for i,j in ipairs(table) do -- 非连续的数字下标会中断 1，2，3，5只能迭代到3
    print(i, j)
end

-- 以字符串为下标的
table = {
    a = "str",
    b = 3,
    c = function() end,
    d = nil,
    [";,"] = 444
}

for i, j in pairs(table) do
    print(i, j)
end

-- 使用next获取下一个迭代器
print(next(table, "b")) -- c = function pointer

```



## 5.判断和循环

### 5.1 if else

```lua
if a < b then
 ...
elseif c < d then
 ...
else
 ...
end
```

### 5.2 for

```lua
for i = 1,10,1 do -- begin, end, step
    ...
end
```

### 5.3 while

```lua
local n = 10
while n > 1 do
    n = n - 1
end
```

