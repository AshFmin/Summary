# Linux和Shell学习

## Linux 原理

### Linux 启动过程

- 内核的引导。
- 运行 init。
- 系统初始化。
- 建立终端 。
- 用户登录系统。

#### 内核引导

BIOS自检，按照设置的启动设备(软盘、硬盘)启动，然后读取/boot目录下的文件

#### 运行init

init进程是所有进程的父进程，通过fork产生新子进程，子进程与父进程采用相同的系统资源，并采用写时复制，当写共享区域(eg:页表)时，子进程会生成新内存并完成写入

init程序首先需要读取配置文件 /etc/inittab

许多程序需要开机启动，在Linux中称为守护进程daemon。

init的一大任务就是去运行这些开机启动的程序

Linux允许



#### 系统初始化







-------------------

### Linux 系统目录结构









-------------------

### Linux 文件基本属性













-----------------------

### Linux 文件与目录管理

Linux 的目录结构为树状结构，最顶级的目录为根目录 /。

其他目录通过挂载可以将它们添加到树中，通过解除挂载可以移除它们。

- 绝对路径： 由根目录 / 写起，eg：/usr/share/doc 这个目录
- 相对路径： . 代表当前文件路径 .. 代表上一级文件路径 相对于当前文件的路径  /f/s1 +  ../s2 -> /f/s2

#### 处理目录的常用命令

- ls（英文全拼：list files）: 列出目录及文件名
- cd（英文全拼：change directory）：切换目录
- pwd（英文全拼：print work directory）：显示目前的目录
- mkdir（英文全拼：make directory）：创建一个新的目录
- rmdir（英文全拼：remove directory）：删除一个空的目录
- cp（英文全拼：copy file）: 复制文件或目录
- rm（英文全拼：remove）: 删除文件或目录
- mv（英文全拼：move file）: 移动文件与目录，或修改文件与目录的名称





---------------------

### Linux 用户与用户组管理















--------------------------

### Linux 磁盘管理













--------------------------

## Shell 基础语法

### 脚本解释器



### 变量



#### 定义变量



---------------------------

#### 使用变量

在定义的变量前加上$即可

``` shell
name="jtx"
echo $name
echo ${name}
```

加不加{}都行，但是推荐给所有变量加上{}

```shell
for skill in Ada Coffee Action Java; do
	echo "I am good at ${skill}Script"
done
```

已经定义的变量可以重复定义

```shell
name="jtx"
echo ${name}
name="haha"
echo ${name}
```

再次赋值的时候不能写成$name，只有使用变量时才加$

---------------

#### 只读变量

使用**readonly**可以将变量定义为只读变量

```shell
#!/bin/bash
myName="jtx"
readonly myName
myName="haha"  // This variable is read only
```

----------

#### 删除变量

使用**unset**可以删除变量
```shell
unset var_name
eg:
myName="jtx"
unset myName
echo ${myName} // null
```

变量被删除后不能再次使用，unset不能删除只读变量

------------

#### 变量类型

运行shell时，会同时存在三种变量：

- 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
- shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

##### 环境变量

Linux环境变量的分隔符是  **:**  ，而Windows下环境变量的分隔符为 **;** 

--------------

#### Shell 字符串

字符串可以单引号、双引号、不用引号。

##### 单引号

```shell
str='hello world'
```

单引号字符串的限制：

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的
- 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用

##### 双引号

```shell
name="jtx"
str="Hello, \"$name\"! \n"
echo -e $str
```

双引号的优点：

- 双引号里可以有变量
- 双引号里可以出现转义字符

##### 字符串拼接

```shell
name="jtx"
greet1="hello, "$name" !"
greet2="hello, ${name} !"
echo $greet1 $greet2
// hello, jtx ! hello, jtx !

greet3='hello, '$name' !'
greet4='hello, ${name} !'
echo $greet3 $greet4
// hello, jtx ! hello, ${name} !

greet5=\"hello\" # 使用转义字符 
echo $greet5
// "hello"
```

##### 获取字符串长度

```shell
str="abcd"
echo ${#str}	// 4
echo ${#str[0]}	// 4
# 变量为字符串时 ${#str} 等价于 ${#str[0]}
```

##### 提取字符子串

```shell
str="jtxhahaha"
echo ${str:1:4} // txha
:offset:len 	// 从0开始
```

##### 查找子字符串

```shell
str="jtxhahaha"
echo `expr index "$str" io`	// 0 i或o的位置
echo `expr index "$str" ja`	// 1 j或a的位置
echo `expr index "$str" ha`	// 4 h或a的位置
# 查找字符最先出现的位置 从1开始 0表示没找到 
```

---------

#### Shell 数组

bash支持一维数组，数组元素用 **空格** 分割

```shell
arrName=(v0 v1 v2 ... vn)
or
arrName=(
v0
v1
v2
)
```

可以单独定义各个分量

```shell
arrName[0]=v0
arrName[1]=v1
arrName[n]=vn
```

可以不使用连续的下标，且下标范围没限制

##### 读取数组

```shell
# 一般格式
${数组名[下标]}
vn=${arrName[n]}

# 使用@可以获得数组中全部的元素
echo ${arrName[@]}
eg:
arr=("haha" "jtx" 1 22 3)
echo ${arr[@]}				// haha jtx 1 22 3
```

##### 获取数组长度

```shell
# 获得元素个数
len=${#arrName[@]}
or
len=${#arrName[*]}
# 获得数组元素的长度
elmtLen=${#arrName[n]}
```

##### 关联数组

关联数组使用 [declare](https://tut.qzxdp.cn/linux/linux-comm-declare.html) 命令来声明

```shell
declare -A arrName	// -A 表明是关联数组 
array_name["index"]	// 访问数组
```

关联数组的键唯一，hashMap

```shell
declare -A site=(["google"]="www.google.com" ["taobao"]="www.taobao.com")

or

declare -A site
site["google"]="www.google.com"
site["taobao"]="www.taobao.com"

echo ${site["taobao"]}
# "www.taobao.com"
```

-------------

### Shell 注释

```shell
# 以#开头的行就是注释
#--------------------------------------------
# 这是一个注释
# 是一个注释
# 一个注释
# 注释
#--------------------------------------------
##### 用户配置区 开始 #####
#
#
# 这里可以添加脚本描述信息
# 
#
##### 用户配置区 结束  #####

# 也可以定义一个函数 函数体中写注释 但不调用该函数
```

#### 多行注释

```shell
:<<EOF
注释内容...
注释内容...
注释内容...
EOF

or

:<<'
注释内容...
注释内容...
注释内容...
'

or

:<<!
注释内容...
注释内容...
注释内容...
!
```

-------------

### Shell 传递参数

脚本内获得参数的格式为：$n, n代表参数的位置 $0表示执行文件名（包含文件路径）

```shell
echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";

$ chmod +x test.sh 
$ ./test.sh 1 2 3
Shell 传递参数实例！
执行的文件名：./test.sh
第一个参数为：1
第二个参数为：2
第三个参数为：3
```

| 参数处理 | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| $#       | 传递到脚本的参数个数                                         |
| $*       | 以一个单字符串显示所有向脚本传递的参数。 如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。 |
| $$       | 脚本运行的当前进程ID号                                       |
| $!       | 后台运行的最后一个进程的ID号                                 |
| $@       | 与$*相同，但是使用时加引号，并在引号中返回每个参数。 如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。 |
| $-       | 显示Shell使用的当前选项，与[set命令](https://tut.qzxdp.cn/linux/linux-comm-set.html)功能相同。 |
| $?       | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |

$* 与 $@ 区别：

- 相同点：都是引用所有参数。
- 不同点：只有在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。

```shell
echo "-- \$* 演示 ---"
for i in "$*"; do
    echo $i
done

echo "-- \$@ 演示 ---"
for i in "$@"; do
    echo $i
done


$ chmod +x test.sh 
$ ./test.sh 1 2 3
-- $* 演示 ---
1 2 3
-- $@ 演示 ---
1
2
3
```

-------------------------

### Shell 基本运算符

Shell支持多种运算符

- 算数运算符
- 关系运算符
- 布尔运算符
- 字符串运算符
- 文件测试运算符

原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

```shell
# expr 是表达式计算工具
val=`expr 2 + 2`
echo "两数之和为: $val" // 两数之和为: 4
```

注意：

- 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
- 完整的表达式要被 ``包含，注意这个字符不是常用的单引号，在 Esc 键下边。

#### 算数运算符

| 运算符 | 说明 下面例子中 a = 10 b = 20                 |             举例              |
| :----: | :-------------------------------------------- | :---------------------------: |
|   +    | 加法                                          |  `expr $a + $b` 结果为 30。   |
|   -    | 减法                                          |  `expr $a - $b` 结果为 -10。  |
|   *    | 乘法                                          | `expr $a \* $b` 结果为  200。 |
|   /    | 除法                                          |   `expr $b / $a` 结果为 2。   |
|   %    | 取余                                          |   `expr $b % $a` 结果为 0。   |
|   =    | 赋值                                          |  a=$b 把变量 b 的值赋给 a。   |
|   ==   | 相等。用于比较两个数字，相同则返回 true。     |   [ $a == $b ] 返回 false。   |
|   !=   | 不相等。用于比较两个数字，不相同则返回 true。 |   [ $a != $b ] 返回 true。    |

注意：

- 条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]

- *需要进行转义 \ 

```shell
a=10
b=20
# a + b
val=`expr $a + $b`
echo "$val"			// 30
echo `expr $a + $b`	// 30

# a - b
val=`expr $a - $b`
echo "$val"			// -10

# a * b
val=`expr $a \* $b`
echo "$val"			// 200

# a / b
val=`expr $a / $b`
echo "$val"			// 0

# a mod b
val=`expr $a % $b`
echo "$val"			// 10

val=`expr a=$b`
echo "$val"			// a=20

# a == b
if [ $a == $b ]		// false
then 
	echo "a等于b"	 
fi

# a != b
if [ $a != $b ]		// true
then
	echo "a不等于b"  // a不等于b
fi
```

--------------------

#### 关系运算符

| 运算符 | 说明 下面例子中 a = 10 b = 20                         | 举例                       |
| :----- | :---------------------------------------------------- | :------------------------- |
| -eq    | 检测两个数是否相等，相等返回 true。                   | [ $a -eq $b ] 返回 false。 |
| -ne    | 检测两个数是否不相等，不相等返回 true。               | [ $a -ne $b ] 返回 true。  |
| -gt    | 检测左边的数是否大于右边的，如果是，则返回 true。     | [ $a -gt $b ] 返回 false。 |
| -lt    | 检测左边的数是否小于右边的，如果是，则返回 true。     | [ $a -lt $b ] 返回 true。  |
| -ge    | 检测左边的数是否大于等于右边的，如果是，则返回 true。 | [ $a -ge $b ] 返回 false。 |
| -le    | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ $a -le $b ] 返回 true。  |

----------------------------------

#### 布尔运算符

| 运算符 | 说明 下面例子中 a = 10 b = 20                       | 举例                                     |
| :----- | :-------------------------------------------------- | :--------------------------------------- |
| !      | 非运算，表达式为 true 则返回 false，否则返回 true。 | [ ! false ] 返回 true。                  |
| -o     | 或运算，有一个表达式为 true 则返回 true。           | [ $a -lt 20 -o $b -gt 100 ] 返回 true。  |
| -a     | 与运算，两个表达式都为 true 才返回 true。           | [ $a -lt 20 -a $b -gt 100 ] 返回 false。 |

--------------------------

#### 逻辑运算符

| 运算符 | 说明                    | 举例                                       |
| :----- | :---------------------- | :----------------------------------------- |
| &&     | 逻辑的 AND 带有短路效果 | [[ $a -lt 100 && $b -gt 100 ]] 返回 false  |
| \|\|   | 逻辑的OR 带有短路效果   | [[ $a -lt 100 \|\| $b -gt 100 ]] 返回 true |

```shell
# 短路逻辑
a=1
if [ $a -eq 2 ] && echo "No Execution" 
then
	echo "true"		
else	
	echo "false"	// reach here
fi

if [ $a -eq 1 ] || echo "No Execution" 
then
	echo "true"		// reach here
else
	echo "false"
fi
# 返回 false
# 返回 true


# 逻辑判断
a=10
b=20

if [[ $a -lt 100 && $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi

if [[ $a -lt 100 || $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi
# 返回 false
# 返回 true
```

-----------------------

#### 字符串运算符

| 运算符 | 说明 a=“abc” b=“xyz”                         | 举例                     |
| :----- | :------------------------------------------- | :----------------------- |
| =      | 检测两个字符串是否相等，相等返回 true。      | [ $a = $b ] 返回 false。 |
| !=     | 检测两个字符串是否不相等，不相等返回 true。  | [ $a != $b ] 返回 true。 |
| -z     | 检测字符串长度是否为0，为0返回 true。        | [ -z $a ] 返回 false。   |
| -n     | 检测字符串长度是否不为 0，不为 0 返回 true。 | [ -n "$a" ] 返回 true。  |
| $      | 检测字符串是否不为空，不为空返回 true。      | [ $a ] 返回 true。       |

```shell
a="abc"
b="xyz"
if [ $a = $b ]		// false
then
	echo "true"
else
	echo "false"
fi

if [ $a = $a ]		// true
then
	echo "true"
else
	echo "false"
fi

if [ $a != $b ]		// true
then
	echo "true"
else
	echo "false"
fi

if [ -z $a ]		// false
then
	echo "true"
else
	echo "false"
fi


if [ -n $a ]		// true
then
	echo "true"
else
	echo "false"
fi

if [ $a ]			// true
then
	echo "true"
else
	echo "false"
fi
# false true true false true true
```

-----------------------

#### 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性

| 操作符  | 说明                                                         | 举例                      |
| :------ | :----------------------------------------------------------- | :------------------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。 block        | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。 character  | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。directory           | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。file | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。pipe            | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。 exist    | [ -e $file ] 返回 true。  |

其他检查符：

- -S: 判断某文件是否 socket。
- -L: 检测文件是否存在并且是一个符号链接。

---------------------------

### Shell echo 命令

echo用于字符串的输出

```shell
echo string
```

#### 显示普通字符串

```shell
echo "It is a test"
echo It is a test
// It is a test
// It is a test
```

#### 显示转义字符

```shell
echo "\"It is a test\""
echo \"It is a test\"
// "It is a test"
// "It is a test"
```

#### 显示变量

```shell
#!/bin/sh
read name // 从stdin 读取一行
echo "$name, it is a test"

# sh test.sh
# jtx
# jtx, it is a test

```

#### 显示换行

```shell
echo -e "OK! \n" # -e 开启转义
echo "It is a test"
# OK! 
#
# It is a test
```

#### 显示不换行

```shell
echo -e "OK! \c" # -e 开启转义 \c 不换行
echo "It is a test"
# OK! It is a test
```

#### 显示结果定向到文件

```shell
echo "It is a test" > myfile # 若不存在myfile 会创建myfile
echo "hahaha" > /dev/null
# /dev/null一个特殊的设备文件 这个文件接收到的任何数据都会被丢弃 null这个设备通常也被成为位桶（bit bucket）或黑洞
```

#### 原样输出 不进行转义和取变量

```shell
echo '$name\'
# $name\
```

#### 显示命令结果

```shell
echo `date` # ``表示执行命令 echo将该命令的返回值输出
# Wed 06 Sep 2023 02:43:44 AM UTC
```

#### echo * 

```shell
echo * 
# 此处 * 为通配符 表示任意字符 
# echo * 则被解释为打印当前目录下所有文件和文件夹的名称

# sql场景
# 若 sql 中存在 *
SQL='select * from tb'
echo $SQL
echo "$SQL"
# select a.txt b.txt c.sh d.sh from tb
# select * from tb
# echo $SQL 被直接翻译为 echo select * from tb  
# 猜想: echo 是一段一段输出的 根据解析的字段进行识别 当遇到分隔符就进行打印 当遇到有特殊含义的就去匹配
# echo select echo * echo from echo tb 故 echo * 被解析为打印当前目录下文件和文件夹的名称

SQLL='select a* from tb'
echo $SQLL
echo "$SQLL"
# select a.txt from tb
# select a* from tb
```

---------------------------

### Shell printf 命令

```shell
printf format-string [arguments...]
```

参数说明：

- format-string: 为格式控制字符串
- arguments: 为参数列表

```shell
echo "hello shell"
# hello shell
printf "hello shell\n"
# hello shell
```

printf 与 C 中的 printf 类似

```shell
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg   
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876
# 姓名     性别   体重kg
# 郭靖     男      66.12
# 杨过     男      48.65
# 郭芙     女      47.99
# ％s 输出一个字符串 ％d 整型输出 ％c 输出一个字符 ％f 输出实数 以小数形式输出
# %-10s 指一个宽度为 10 个字符（- 表示左对齐，没有则表示右对齐）
# 任何字符都会被显示在 10 个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来。
# %-4.2f 指格式化为小数，其中 .2 指保留2位小数。
```

```shell
# format-string为双引号
printf "%d %s\n" 1 "abc" # 1 abc

# 单引号与双引号效果一样 
printf '%d %s\n' 1 "abc" # 1 abc

# 没有引号也可以输出
printf %s abcdef		 
# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
printf %s abc def
printf "%s\n" abc def
# abcdefabcdefabc
# def

printf "%s %s %s\n" a b c d e f g h i j
# a b c
# d e f
# g h i
# j  

# 如果没有 arguments，那么 %s 用NULL代替，%d 用 0 代替
printf "%s and %d \n"
#  and 0 
```

#### 利用printf实现sql参数拼接

```shell
# 自定义格式可变参数 利用printf通配符实现拼接
Q_SQL="select * from tb_user where name=%s and id=%s"
OLD_IFS="$IFS"
IFS="|"
arr=($line)
IFS="$OLD_IFS"
params=${arr[@]:3}
EX_SQL=`printf "$Q_SQL" ${params[@]}`
# 字符串替换
echo "$EX_SQL"
```

#### printf 转义序列

| 序列                     | 说明                                                         |
| :----------------------- | :----------------------------------------------------------- |
| \a                       | 警告字符，通常为ASCII的BEL字符                               |
| \b                       | 后退                                                         |
| \c                       | 抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略 |
| \f                       | 换页（formfeed）                                             |
| \n                       | 换行                                                         |
| \r                       | 回车（Carriage return）                                      |
| \t                       | 水平制表符                                                   |
| \v                       | 垂直制表符                                                   |
| \|一个字面上的反斜杠字符 |                                                              |
| \ddd                     | 表示1到3位数八进制值的字符。仅在格式字符串中有效             |
| \0ddd                    | 表示1到3位的八进制值字符                                     |

```she
$ printf "a string, no processing:<%s>\n" "A\nB"
a string, no processing:<A\nB>

$ printf "a string, no processing:<%b>\n" "A\nB"
a string, no processing:<A
B>

$ printf "www.qzxdp.cn \a"
www.qzxdp.cn $                  #不换行
```

--------------------

### Shell test 命令

Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。

#### 数值测试

| 参数 | 说明                            |
| :--: | :------------------------------ |
| -eq  | equal 等于则为真                |
| -ne  | not equal 不等于则为真          |
| -gt  | greater 大于则为真              |
| -ge  | greater or equal 大于等于则为真 |
| -lt  | less than 小于则为真            |
| -le  | less or equal 小于等于则为真    |

```shell
# $[] 和 $(()) 类似 都只能处理整数表达式运算
num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi
# 两个数相等！

a=5
b=6

result=$[a+b] # 注意等号两边不能有空格
echo "result 为： $result"
# result 为： 11
```

#### 字符串测试

|   参数    | 说明                     |
| :-------: | :----------------------- |
|     =     | 等于则为真               |
|    !=     | 不相等则为真             |
| -z 字符串 | 字符串的长度为零则为真   |
| -n 字符串 | 字符串的长度不为零则为真 |

```shell
s1="jtx"
s2="haha"
if test s1 = s2
then
    echo '相等！'
else
    echo '不相等！'
fi
# 不相等！
```

#### 文件测试

|   参数    | 说明                                           |
| :-------: | :--------------------------------------------- |
| -e 文件名 | exist 如果文件存在则为真                       |
| -r 文件名 | read 如果文件存在且可读则为真                  |
| -w 文件名 | write 如果文件存在且可写则为真                 |
| -x 文件名 | execute 如果文件存在且可执行则为真             |
| -s 文件名 | single 如果文件存在且至少有一个字符则为真      |
| -d 文件名 | directory 如果文件存在且为目录则为真           |
| -f 文件名 | file 如果文件存在且为普通文件则为真            |
| -c 文件名 | character 如果文件存在且为字符型特殊文件则为真 |
| -b 文件名 | block 如果文件存在且为块特殊文件则为真         |

```shell
cd /bin
if test -e ./bash
then
    echo '文件已存在!'
else
    echo '文件不存在!'
fi
# 文件已存在!
```

Shell 还提供了与( -a )、或( -o )、非( ! )三个逻辑操作符用于将测试条件连接起来，其优先级为： ! 最高， -a 次之， -o 最低。

```shell
cd /bin
if test -e ./notFile -o -e ./bash
then
    echo '至少有一个文件存在!'
else
    echo '两个文件都不存在'
fi
# 至少有一个文件存在!
```

### Shell 流程控制

和 Java、PHP 等语言不一样，sh 的流程控制不可为空，如果 else 分支没有语句执行，就不要写这个 else。

#### if

```shell
if condition
then
	command1
	command2
	...
	commandn
fi
# 写成一行
if [ ]; then ; fi
```

#### if else 

```shell
if condition
then
    command1 
    command2
    ...
    commandn
else
    command
fi
```

#### if else-if else

```shell
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

- if else 的 [...] 判断语句中大于使用 -gt，小于使用 -lt。
- 如果使用 ((...)) 作为判断语句，大于和小于可以直接使用 > 和 <。

---------------

#### for循环

```shell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
# 写成一行
for var in item1 item2 ... itemN; do command1; command2… done;

eg:
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
# The value is: 1
# The value is: 2
# The value is: 3
# The value is: 4
# The value is: 5

for str in this is a test
do
	echo $str
done
# this
# is
# a
# test

# 无限循环
for (( ; ; ))
```

#### while循环

```shell
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
# 1
# 2
# 3
# 4
# 5
# let 命令，它用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量

# 循环读取键盘输入
while read content
do
    ...$content
done

# 无限循环
while :
do
    command
done
或者
while true
do
    command
done	
```

#### for 与 while

```shell
# 由于sql中有空格 故采用while 
# 若采用cat+for会 按空格分割成字符串 然后每次读取字符串 
SQL_LIST='sqlcfg.lst'
cat "$SQL_LIST" | while read line
do
    Q_SQL=`echo "$line" | awk -F"[|]+" '{print $1}'`
    TABLE_NAME=`echo "$line" | awk -F"[|]+" '{print $2}'`
    PARAM_NUM=`echo "$line" | awk -F"[|]+" '{print $3}'`
    echo "$Q_SQL"
    echo "$TABLE_NAME"
    echo "$PARAM_NUM"
    echo "----------------"
done
```

#### until循环

until 循环执行一系列命令直至条件为 true 时停止

```shell
until condition
do
    command
done

eg:
a=0
until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1` # let "a++"
done
```

--------------

#### case ... esac

**case ... esac** 为多选择语句，与其他语言中的 switch ... case 语句类似，是一种多分支选择结构，每个 case 分支用右圆括号开始，用两个分号 ;; 表示 break，即执行结束，跳出整个 case ... esac 语句，esac（就是 case 反过来）作为结束标记。

```shell
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2)
    command1
    command2
    ...
    commandN
    ;;
esac

eg:
echo "please input a num between 1 and 4"
echo -n "your input:"
read num
case $num in
	1) echo "your choice is 1"
	;;
	2) echo "your choice is 2"
	;;
	3) echo "your choice is 3"
	;;
	4) echo "your choice is 4"
	;;
    *) echo "error input"
    ;;
esac
```

#### 跳出循环

在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell 使用两个命令来实现该功能：**break** 和 **continue**。

##### break

break 命令允许跳出所有循环。

```shell
while :
do
    echo -n "输入 1 到 5 之间的数字:"
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的! 游戏结束"
            break
        ;;
    esac
done
```

##### continue

continue 命令与 break 命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

```shell
while :
do
    echo -n "输入 1 到 5 之间的数字: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的!"
            continue
            echo "游戏结束" 		# never reach
        ;;
    esac
done
```

---------------------------------------

### Shell 函数

linux shell 可以用户定义函数，然后在shell脚本中可以随便调用。

```shell
[ function ] funname [()]

{

    action;

    [return int;]

}
```

- 可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
- 返回值，可以显式 return 返回，只能是整数，或以**最后一条命令运行结果**，作为返回值。 return后跟数值n( 0-255 )

```shell
function myFunc()
{
        echo "this is my first test for shell function"
}
echo "---- func is running ----"
myFunc
echo "---- func is done  ------"
# ---- func is running ----
# this is my first test for shell function
# ---- func is done  ------

function fwr()
{
	echo -n "please input first num:"
	read num1
	echo -n "please input second num:"
	read num2
	sum=$(($num1+$num2))
	echo "the sum of nums is $sum"
	return $sum
}
fwr
ret=$?		# $? 获取最近一条命令执行结果
echo "return value is $ret"
# please input first num:1
# please input second num:2
# the sum of nums is 3
# return value is 3

# 通过echo获得任意返回值
function FRByEcho()
{
	echo "this is return value"
}

ret=`FRByEcho`  # $(FRByEcho) 效果是一样的
echo "FRByEcho return value is: $ret"
# FRByEcho return value is: this is return value




```

- 所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。

#### 函数参数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数...

```shell
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"		# $10 不能获取第十个参数，获取第十个参数需要${10}
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
# 第一个参数为 1 !
# 第二个参数为 2 !
# 第十个参数为 10 !
# 第十个参数为 34 !
# 第十一个参数为 73 !
# 参数总数有 11 个!
# 作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !
```

- 当n>=10时，需要使用${n}来获取参数。

| 参数处理 | 说明                                                         |
| :------: | :----------------------------------------------------------- |
|    $#    | 传递到脚本或函数的参数个数                                   |
|    $*    | 以一个单字符串显示所有向脚本传递的参数 把所有参数看成一个整体 |
|    $$    | 脚本运行的当前进程ID号                                       |
|    $!    | 后台运行的最后一个进程的ID号                                 |
|    $@    | 与$*相同，但是使用时加引号，并在引号中返回每个参数。 把每个参数区分对待 |
|    $-    | 显示Shell使用的当前选项，与set命令功能相同。                 |
|    $?    | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |

--------------------------

### Shell 输入/输出重定向

大多数 UNIX 系统命令从你的终端接受输入并将所产生的输出发送回到您的终端。一个命令通常从一个叫标准输入的地方读取输入，默认情况下，这恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下，这也是你的终端。

| 命令            | 说明                                               |
| :-------------- | :------------------------------------------------- |
| command > file  | 将输出重定向到 file。                              |
| command < file  | 将输入重定向到 file。                              |
| command >> file | 将输出以**追加**的方式重定向到 file。              |
| n > file        | 将文件描述符为 n 的文件重定向到 file。             |
| n >> file       | 将文件描述符为 n 的文件以追加的方式重定向到 file。 |
| n >& m          | 将输出文件 m 和 n 合并。                           |
| n <& m          | 将输入文件 m 和 n 合并。                           |
| << tag          | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。 |

> 需要注意的是文件描述符 0 通常是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。

#### 输出重定向

重定向一般通过在命令间插入特定的符号来实现

```shell
command1 > file1
```

上面这个命令执行command1然后将输出的内容存入file1。

注意任何file1内的已经存在的内容将被新内容替代。如果要将新内容添加在文件末尾，请使用 **>>** 操作符。

```shell
who > info
cat info
# jtx      :0           2023-09-06 10:26 (:0)
# jtx      pts/0        2023-09-06 10:43 (:0)

echo "hahaha" > info
cat info
# hahaha

echo "jtx fight!" >> info	# Linux -bash: !“: event not found 可以通过 set -H 或 用单引号
cat info
# hahaha
# jtx fight!
```

#### 输入重定向

和输出重定向一样，Unix 命令也可以从文件获取输入，语法为：

```shell
command1 < file1
```

这样，本来需要从键盘获取输入的命令会转移到文件读取内容。

``` shell
wc -l info
# 2 info
wc -l < info
# 2
# 第一个例子，会输出文件名；第二个不会，因为它仅仅知道从标准输入读取内容。

command < infile > outfile 
# 同时替换输入和输出，执行command，从文件infile读取内容，然后将输出写入到outfile中。
```

#### 详解重定向

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

- 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
- 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
- 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

默认情况下，command > file 将 stdout 重定向到 file，command < file 将stdin 重定向到 file。

如果希望 stderr 重定向到 file，可以这样写：

```shell
command 2>file
command 2>>file

# 如果希望将 stdout 和 stderr 合并后重定向到 file，可以这样写：
$ command > file 2>&1
或
$ command >> file 2>&1
```

#### Here Document

Here Document 是 Shell 中的一种特殊的重定向方式，用来将输入重定向到一个交互式 Shell 脚本或程序。

```shell
command << delimiter
    document
delimiter
# 它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command

wc -l << EOF
    hahaha
    jtx
    hahaha
EOF
# 3

# Here Document 也可用在脚本中
cat << EOF
	ha
	haha
	hahaha
EOF

# 	 ha
# 	 haha
#	 hahaha
```

##### << 与 <<-







#### /dev/null 文件

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null

```shell
command > /dev/null
```

> /dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。

如果希望屏蔽 stdout 和 stderr，可以这样写：

```shell
command > /dev/null 2>&1
```

> 注意：0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。 这里的 **2** 和 **>** 之间不可以有空格，**2>** 是一体的时候才表示错误输出。

--------------------

### Shell 文件包含

Shell 也可以包含外部脚本，这样可以很方便的封装一些公用的代码作为一个独立的文件。

```shell
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename
```

```shell
#test1.sh
#!/bin/sh
name="jtx"

#test2.sh
#!/bin/sh
. ./test1.sh
echo "hello $name"

chmod +x test2.sh 
./test2.sh 
# hello jtx
```

> 注：被包含的文件 test1.sh 不需要可执行权限。

--------------------

### 常见命令

#### ls

ls的常见选项





ls通配符





#### cp

cp后的文件的修改日期是命令执行的日期





#### awk

AWK 是一种处理文本文件的语言，是一个强大的文本分析工具。

之所以叫 AWK 是因为其取了三位创始人 Alfred Aho，Peter Weinberger, 和 Brian Kernighan 的 Family Name 的首字符。

```shell
awk [选项参数] 'script' var=value file(s)
或
awk [选项参数] -f scriptfile var=value file(s)
```

选项参数说明：

- -F fs or --field-separator fs 指定输入文件折分隔符，fs是一个字符串或者是一个正则表达式，如-F:。
- -v var=value or --asign var=value 赋值一个用户定义变量。
- -f scripfile or --file scriptfile 从脚本文件中读取awk命令。
- -mf nnn and -mr nnn 对nnn值设置内在限制，-mf选项限制分配给nnn的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。
- -W compact or --compat, -W traditional or --traditional 在兼容模式下运行awk。所以gawk的行为和标准的awk完全一样，所有的awk扩展都被忽略。
- -W copyleft or --copyleft, -W copyright or --copyright 打印简短的版权信息。
- -W help or --help, -W usage or --usage 打印全部awk选项和每个选项的简短说明。
- -W lint or --lint 打印不能向传统unix平台移植的结构的警告。
- -W lint-old or --lint-old 打印关于不能向传统unix平台移植的结构的警告。
- -W posix 打开兼容模式。但有以下限制，不识别：/x、函数关键字、func、换码序列以及当fs是一个空格时，将新行作为一个域分隔符；操作符**和**=不能代替^和^=；fflush无效。
- -W re-interval or --re-inerval 允许间隔正则表达式的使用，参考(grep中的Posix字符类)，如括号表达式[[:alpha:]]。
- -W source program-text or --source program-text 使用program-text作为源代码，可与-f命令混用。
- -W version or --version 打印bug报告信息的版本。

##### 基本用法

```shell
2 this is a test
3 Do you like awk
This's a test
10 There are orange,apple,mongo
```

```shell
awk '{[pattern] action}' {filenames} 

 # 每行按默认分隔符空格或TAB分割，输出文本中的1、4项 
 $ awk '{print $1,$4}' log.txt
 ---------------------------------------------
 2 a
 3 like
 This's
 10 orange,apple,mongo
 # 格式化输出
 $ awk '{printf "%-8s %-10s\n",$1,$4}' log.txt
 ---------------------------------------------
 2        a
 3        like
 This's
 10       orange,apple,mongo
```

```shell
awk -F  #-F相当于内置变量FS, 指定分割字符

 # 使用","分割
 $  awk -F, '{print $1,$2}'   log.txt
 ---------------------------------------------
 2 this is a test
 3 Do you like awk
 This's a test
 10 There are orange apple
 # 或者使用内建变量
 $ awk 'BEGIN{FS=","} {print $1,$2}'     log.txt
 ---------------------------------------------
 2 this is a test
 3 Do you like awk
 This's a test
 10 There are orange apple
 # 使用多个分隔符.先使用空格分割，然后对分割结果再使用","分割
 $ awk -F '[ ,]'  '{print $1,$2,$5}'   log.txt
 ---------------------------------------------
 2 this test
 3 Do awk
 This's a
 10 There apple
```

```shell
awk -v  # 设置变量

 $ awk -va=1 '{print $1,$1+a}' log.txt
 ---------------------------------------------
 2 3
 3 4
 This's 1
 10 11
 $ awk -va=1 -vb=s '{print $1,$1+a,$1b}' log.txt
 ---------------------------------------------
 2 3 2s
 3 4 3s
 This's 1 This'ss
 10 11 10s
```

```shell
awk -f {awk脚本} {文件名}

 $ awk -f cal.awk log.txt
```

##### 运算符

| 运算符                  | 描述                             |
| :---------------------- | :------------------------------- |
| = += -= *= /= %= ^= **= | 赋值                             |
| ?:                      | C条件表达式                      |
| &&                      | 逻辑与                           |
| ~ 和 !~                 | 匹配正则表达式和不匹配正则表达式 |
| < <= > >= != ==         | 关系运算符                       |
| 空格                    | 连接                             |
| + -                     | 加，减                           |
| * / %                   | 乘，除与求余                     |
| + - !                   | 一元加，减和逻辑非               |
| ^ ***                   | 求幂                             |
| ++ --                   | 增加或减少，作为前缀或后缀       |
| $                       | 字段引用                         |
| in                      | 数组成员                         |

```shell
# 过滤第一列大于2的行
$ awk '$1>2' log.txt    #命令
#输出
3 Do you like awk
This's a test
10 There are orange,apple,mongo

# 过滤第一列等于2的行
$ awk '$1==2 {print $1,$3}' log.txt    #命令
#输出
2 is

# 过滤第一列大于2并且第二列等于'Are'的行
$ awk '$1>2 &&amp; $2=="Are" {print $1,$2,$3}' log.txt    #命令
#输出
3 Are you
```

##### 内建变量

| 变量        | 描述                                              |
| :---------- | :------------------------------------------------ |
| $n          | 当前记录的第n个字段，字段间由FS分隔               |
| $0          | 完整的输入记录                                    |
| ARGC        | 命令行参数的数目                                  |
| ARGIND      | 命令行中当前文件的位置(从0开始算)                 |
| ARGV        | 包含命令行参数的数组                              |
| CONVFMT     | 数字转换格式(默认值为%.6g)ENVIRON环境变量关联数组 |
| ERRNO       | 最后一个系统错误的描述                            |
| FIELDWIDTHS | 字段宽度列表(用空格键分隔)                        |
| FILENAME    | 当前文件名                                        |
| FNR         | 各文件分别计数的行号                              |
| FS          | 字段分隔符(默认是任何空格)                        |
| IGNORECASE  | 如果为真，则进行忽略大小写的匹配                  |
| NF          | 一条记录的字段的数目                              |
| NR          | 已经读出的记录数，就是行号，从1开始               |
| OFMT        | 数字的输出格式(默认值是%.6g)                      |
| OFS         | 输出字段分隔符，默认值与输入字段分隔符一致。      |
| ORS         | 输出记录分隔符(默认值是一个换行符)                |
| RLENGTH     | 由match函数所匹配的字符串的长度                   |
| RS          | 记录分隔符(默认是一个换行符)                      |
| RSTART      | 由match函数所匹配的字符串的第一个位置             |
| SUBSEP      | 数组下标分隔符(默认值是/034)                      |

##### 使用正则，字符串匹配

``` shell
# 输出第二列包含 "th"，并打印第二列与第四列
$ awk '$2 ~ /th/ {print $2,$4}' log.txt
---------------------------------------------
this a
```

~ 表示模式开始。// 中是模式。

```shell
# 输出包含 "re" 的行
$ awk '/re/ ' log.txt
---------------------------------------------
10 There are orange,apple,mongo
```

##### 忽略大小写

```shell
$ awk 'BEGIN{IGNORECASE=1} /this/' log.txt
---------------------------------------------
2 this is a test
This's a test
```

##### 模式取反

```shell
$ awk '$2 !~ /th/ {print $2,$4}' log.txt
---------------------------------------------
Do like
a
There orange,apple,mongo
$ awk '!/th/ {print $2,$4}' log.txt
---------------------------------------------
Do like
a
There orange,apple,mongo

$ awk '$1 !~ /a/ {print $2,$4}' log.txt
---------------------------------------------
this a
Do like
a 
There orange,apple,mongo
$ awk ' ! /a/ {print $2,$4}' log.txt
---------------------------------------------
null
```

##### awk 脚本

关于 awk 脚本，我们需要注意两个关键词 BEGIN 和 END。

- BEGIN
- END

假设有这么一个文件（学生成绩表）：

```shell
$ cat score.txt
Marry   2143 78 84 77
Jack    2321 66 78 45
Tom     2122 48 77 71
Mike    2537 87 97 95
Bob     2415 40 57 62
```

我们的 awk 脚本如下：

```shell
$ cat cal.awk
#!/bin/awk -f
#运行前
BEGIN {
    math = 0
    english = 0
    computer = 0
 
    printf "NAME    NO.   MATH  ENGLISH  COMPUTER   TOTAL\n"
    printf "---------------------------------------------\n"
}
#运行中
{
    math+=$3
    english+=$4
    computer+=$5
    printf "%-6s %-6s %4d %8d %8d %8d\n", $1, $2, $3,$4,$5, $3+$4+$5
}
#运行后
END {
    printf "---------------------------------------------\n"
    printf "  TOTAL:%10d %8d %8d \n", math, english, computer
    printf "AVERAGE:%10.2f %8.2f %8.2f\n", math/NR, english/NR, computer/NR	# NR读出的记录数，从1开始
}
```

```shell
$ awk -f cal.awk score.txt
NAME    NO.   MATH  ENGLISH  COMPUTER   TOTAL
---------------------------------------------
Marry  2143     78       84       77      239
Jack   2321     66       78       45      189
Tom    2122     48       77       71      196
Mike   2537     87       97       95      279
Bob    2415     40       57       62      159
---------------------------------------------
  TOTAL:       319      393      350
AVERAGE:     63.80    78.60    70.00
```

> BEGIN 代码块先执行一次
>
> 对于输入的每一行执行中间代码块
>
> 最后执行 END代码块

```shell
# 计算文件大小
$ ls -l *.txt | awk '{sum+=$5} END {print sum}'
# ls -l 通常第五项为文件大小

# 从文件中找出长度大于 80 的行：
awk 'length>80' log.txt
```

---------------------------------

#### grep

Linux grep (global regular expression) 命令用于查找文件里符合条件的字符串或正则表达式。

grep 指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，预设 grep 指令会把含有范本样式的那一列显示出来。若不指定任何文件名称，或是所给予的文件名为 -，则 grep 指令会从标准输入设备读取数据。

```shell
grep [options] pattern [files]
或
grep [-abcEFGhHilLnqrsvVwxy][-A<显示行数>][-B<显示列数>][-C<显示列数>][-d<进行动作>][-e<范本样式>][-f<范本文件>][--help][范本样式][文件或目录...]
```

- pattern - 表示要查找的字符串或正则表达式。
- files - 表示要查找的文件名，可以同时查找多个文件，如果省略 files 参数，则默认从标准输入中读取数据。

常用选项：：

- -i：忽略大小写进行匹配。
- -v：反向查找，只打印不匹配的行。
- -n：显示匹配行的行号。
- -r：递归查找子目录中的文件。
- -l：只打印匹配的文件名。
- -c：只打印匹配的行数。 

更多参数说明：

- -a 或 --text : 不要忽略二进制的数据。
- -A<显示行数> 或 --after-context=<显示行数> : 除了显示符合范本样式的那一列之外，并显示该行之后的内容。
- -b 或 --byte-offset : 在显示符合样式的那一行之前，标示出该行第一个字符的编号。
- -B<显示行数> 或 --before-context=<显示行数> : 除了显示符合样式的那一行之外，并显示该行之前的内容。
- -c 或 --count : 计算符合样式的列数。
- -C<显示行数> 或 --context=<显示行数>或-<显示行数> : 除了显示符合样式的那一行之外，并显示该行之前后的内容。
- -d <动作> 或 --directories=<动作> : 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。
- -e<范本样式> 或 --regexp=<范本样式> : 指定字符串做为查找文件内容的样式。
- -E 或 --extended-regexp : 将样式为延伸的正则表达式来使用。
- -f<规则文件> 或 --file=<规则文件> : 指定规则文件，其内容含有一个或多个规则样式，让grep查找符合规则条件的文件内容，格式为每行一个规则样式。
- -F 或 --fixed-regexp : 将样式视为固定字符串的列表。
- -G 或 --basic-regexp : 将样式视为普通的表示法来使用。
- -h 或 --no-filename : 在显示符合样式的那一行之前，不标示该行所属的文件名称。
- -H 或 --with-filename : 在显示符合样式的那一行之前，表示该行所属的文件名称。
- -i 或 --ignore-case : 忽略字符大小写的差别。
- -l 或 --file-with-matches : 列出文件内容符合指定的样式的文件名称。
- -L 或 --files-without-match : 列出文件内容不符合指定的样式的文件名称。
- -n 或 --line-number : 在显示符合样式的那一行之前，标示出该行的列数编号。
- -o 或 --only-matching : 只显示匹配PATTERN 部分。
- -q 或 --quiet或--silent : 不显示任何信息。
- -r 或 --recursive : 此参数的效果和指定"-d recurse"参数相同。
- -s 或 --no-messages : 不显示错误信息。
- -v 或 --invert-match : 显示不包含匹配文本的所有行。
- -V 或 --version : 显示版本信息。
- -w 或 --word-regexp : 只显示全字符合的列。
- -x --line-regexp : 只显示全列符合的列。
- -y : 此参数的效果和指定"-i"参数相同。

```shell
# 在文件 file.txt 中查找字符串 "hello"，并打印匹配的行：
grep hello file.txt

# 在文件夹 dir 中递归查找所有文件中匹配正则表达式 "pattern" 的行，并打印匹配行所在的文件名和行号：
grep -r -n pattern dir/

# 在标准输入中查找字符串 "world"，并只打印匹配的行数：  xxx：匹配的行数
echo "hello world" | grep -c world

# 查找前缀有“test”的文件包含“test”字符串的文件  
grep test *file
```

---------------------------

#### sed

Linux sed 命令是**利用脚本来处理文本文件**。

sed 可依照脚本的指令来处理、编辑文本文件。

Sed 主要用来自动编辑一个或多个文件、简化对文件的反复操作、编写转换程序等。

```shell
sed [-hnV][-e<script>][-f<script文件>][文本文件]
```

参数说明：

- `-e<script>`或`--expression=<script>` 以选项中指定的script来处理输入的文本文件。
- `-f<script文件>`或`--file=<script文件>` 以选项中指定的script文件来处理输入的文本文件。
- `-h`或`--help` 显示帮助。
- `-n`或`--quiet`或`--silent` 仅显示script处理后的结果。
- `-V`或`--version` 显示版本信息。

动作说明：

- a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～  \

- c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！   \

- d ：删除，因为是删除啊，所以 d 后面通常不接任何东东；

- i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)； \  

- p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～

- s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正则表达式！例如 1,20s/old/new/g 就是啦！

  > -i  与 -e 
  >
  > -i 是在原文件上进行修改
  >
  > -e 是将修改后的数据重定向到输出流

```shell
# 创建一个 testfile 文件，内容如下：
$ cat testfile #查看testfile 中的内容  
HELLO LINUX!  
Linux is a free unix-type opterating system.  
This is a linux testfile!  
Linux test 
Google
Taobao
Qzxdp
Tesetfile
Wiki
```

```shell
# 在第四行后的下一行添加 newLine
$ nl testfile | sed -e 4a\newLine
-------------------------------------------
     1	HELLO LINUX!  
     2	Linux is a free unix-type opterating system.  
     3	This is a linux testfile!  
     4	Linux test 
newLine
     5	Google
     6	Taobao
     7	Qzxdp
     8	Tesetfile
     9	Wiki

```

##### 以行为单位的新增/删除

```shell
# 删除第2-5行
$ nl testfile | sed '2,5d'
-------------------------------------------
     1  HELLO LINUX!  
     6  Taobao
     7  Qzxdp
     8  Tesetfile
     9  Wiki
   
# 删除第2行
$ nl testfile | sed '2d' 
-------------------------------------------
     1  HELLO LINUX!  
     3  This is a linux testfile!  
     4  Linux test 
     5  Google
     6  Taobao
     7  Qzxdp
     8  Tesetfile
     9  Wiki

# 删除第 3 到最后一行
$ nl testfile | sed '3,$d' 
-------------------------------------------
     1  HELLO LINUX!  
     2  Linux is a free unix-type opterating system.
     
     
# 在第二行后添加'drink tea'  
$ nl testfile | sed '2a drink tea'
-------------------------------------------
     1  HELLO LINUX!  
     2  Linux is a free unix-type opterating system.  
drink tea
     3  This is a linux testfile!  
     4  Linux test 
     5  Google
     6  Taobao
     7  Qzxdp
     8  Tesetfile
     9  Wiki
# 在第二前后添加'drink tea'  
$ nl testfile | sed '2i drink tea'

# 若添加多行需要加 \ 标注每行结尾
$ nl testfile | sed '2a Drink tea or ......\
drink beer ?'
-------------------------------------------
1  HELLO LINUX!  
     2  Linux is a free unix-type opterating system.  
Drink tea or ......
drink beer ?
     3  This is a linux testfile!  
     4  Linux test 
     5  Google
     6  Taobao
     7  Qzxdp
     8  Tesetfile
     9  Wiki
```

> sed 的 -e 可省略，同时也要注意的是， sed 后面接的动作，必须 ' '

##### 以行为单位的替换与显示

```shell
# 将第2-5行替换成 No 2-5 number
$ nl testfile | sed '2,5c No 2-5 number'
-------------------------------------------
     1  HELLO LINUX!  
No 2-5 number
     6  Taobao
     7  Qzxdp
     8  Tesetfile
     9  Wiki
     
# 仅列出 testfile 文件内的第 5-7 行
$ nl testfile | sed -n '5,7p'
-------------------------------------------
     5  Google
     6  Taobao
     7  Qzxdp
```

##### 数据的搜寻

```sh
# 搜索 testfile 有 oo 关键字的行
$ nl testfile | sed -n '/oo/p'
-------------------------------------------
     5  Google

# 删除 testfile 所有包含 oo 的行，其他行输出
$ nl testfile | sed  '/oo/d'
-------------------------------------------
     1	HELLO LINUX!  
     2	Linux is a free unix-type opterating system.  
     3	This is a linux testfile!  
     4	Linux test 
     6	Taobao
     7	Qzxdp
     8	Tesetfile
     9	Wiki

# 找到含有oo的行 对该行进行 oo 替换 kk的操作  p表示打印  q表示不再继续处理
$ nl testfile | sed -n '/oo/{s/oo/kk/;p;q}'  
-------------------------------------------
     5  Gkkgle
     
# 在testfile末尾再添加一行 hohohoo
$ nl testfile | sed -n '/oo/{s/oo/kk/;p;q}'  
-------------------------------------------
     5  Gkkgle
     
$ nl testfile | sed -n '/oo/{s/oo/kk/;p}'  
-------------------------------------------
     5	Gkkgle
    10	hohohkk
# 去掉p会继续处理后续符合 /oo/的行
```

##### 数据编辑

除了整行的处理模式之外， sed 还可以用行为单位进行部分数据的查找与替换

```shell
# 与vim类似
sed 's/要被取代的字串/新的字串/g'
# g 标识符表示全局查找替换 修改后内容会到标准输出，不会修改原文件：
sed -e 's/oo/kk/g' testfile
# 选项 i 使 sed 修改文件
sed -i 's/oo/kk/g' testfile

# 批量操作当前目录下以 test 开头的文件，将全部oo替换成kk 等价于 vim中的 %s/oo/kk/g
sed -i 's/oo/kk/g' ./test*
```

##### 多点编辑

```shell
# -e 表示多点编辑，第一个编辑命令删除 testfile 第三行到末尾的数据，第二条命令搜索 HELLO 替换为 hell。
$ nl testfile | sed -e '3,$d' -e 's/HELLO/hello'
-------------------------------------------
     1  hello LINUX!  
     2  Linux is a free unix-type opterating system.
```

----------------------------

#### date

Linux **date** 命令可以用来显示或设定系统的日期与时间

```shell
date [OPTION]... [+FORMAT]
date [-u] [-d datestr] [-s datestr] [--utc] [--universal] [--date=datestr] [--set=datestr] [--help] [--version] [+FORMAT] [MMDDhhmm[[CC]YY][.ss]]
```

- -d, --date=STRING：通过字符串显示时间格式，字符串不能是'now'。
- -f, --file=DATEFILE：类似于--date; 一次从DATEFILE处理一行。
- -I[FMT], --iso-8601[=FMT]：按照 ISO 8601 格式输出时间，FMT 可以为'date'(默认)，'hours'，'minutes'，'seconds'，'ns'。 可用于设置日期和时间的精度，例如：2006-08-14T02:34:56-0600。
- -R, --rfc-2822 ： 按照 RFC 5322 格式输出时间和日期，例如: Mon, 14 Aug 2006 02:34:56 -0600。
- --rfc-3339=FMT：按照 RFC 3339 格式输出，FMT 可以为'date', 'seconds','ns'中的一个，可用于设置日期和时间的精度， 例如：2006-08-14 02:34:56-06:00。
- -r, --reference=FILE：显示文件的上次修改时间。
- -s, --set=STRING：根据字符串设置系统时间。
- -u, --utc, --universal：显示或设置协调世界时(UTC)。
- --help：显示帮助信息。
- --version：输出版本信息。

在显示方面，使用者可以设定欲显示的格式 ，格式设定为一个**加号**后接数个标记，其中可用的标记列表如下：

```shell
%%   输出字符 %
%a   星期几的缩写 (Sun..Sat)
%A   星期的完整名称(Sunday..Saturday)。 
%b   缩写的月份名称（例如，Jan）
%B   完整的月份名称（例如，January）
%c   本地日期和时间（例如，Thu Mar  3 23:05:25 2005）
%C   世纪，和%Y类似，但是省略后两位（例如，20）
%d   日 (01..31)
%D   日期，等价于%m/%d/%y
%e   一月中的一天，格式使用空格填充，等价于%_d
%F   完整的日期；等价于 %Y-%m-%d
%g   ISO 标准计数周的年份的最后两位数字
%G   ISO 标准计数周的年份，通常只对%V有用
%h   等价于 %b
%H   小时 (00..23)
%I   小时 (01..12)
%j   一年中的第几天 (001..366)
%k   小时，使用空格填充 ( 0..23); 等价于 %_H
%l   小时, 使用空格填充 ( 1..12); 等价于 %_I
%m   月份 (01..12)
%M   分钟 (00..59)
%n   新的一行，换行符
%N   纳秒 (000000000..999999999)
%p   用于表示当地的AM或PM，如果未知则为空白
%P   类似 %p, 但是是小写的
%r   本地的 12 小时制时间(例如 11:11:04 PM)
%R   24 小时制 的小时与分钟; 等价于 %H:%M
%s   自 1970-01-01 00:00:00 UTC 到现在的秒数
%S   秒 (00..60)
%t   插入水平制表符 tab
%T   时间; 等价于 %H:%M:%S
%u   一周中的一天 (1..7); 1 表示星期一
%U   一年中的第几周，周日作为一周的起始 (00..53)
%V   ISO 标准计数周，该方法将周一作为一周的起始 (01..53)
%w   一周中的一天（0..6），0代表星期天
%W   一年中的第几周，周一作为一周的起始（00..53）
%x   本地的日期格式（例如，12/31/99）
%X   本地的日期格式（例如，23:13:48）
%y   年份后两位数字 (00..99)
%Y   年
%z   +hhmm 格式的数值化时区格式（例如，-0400）
%:z  +hh:mm 格式的数值化时区格式（例如，-04:00）
%::z  +hh:mm:ss格式的数值化时区格式（例如，-04:00:00）
%:::z  数值化时区格式，相比上一个格式增加':'以显示必要的精度（例如，-04，+05:30）
%Z  时区缩写 （如 EDT）
```

// TODO



----------------------

#### bc

bc是一个高精度计算器，可以进行任意精度的整数和浮点数运算。

```shell
#计算 3.14乘以3的结果
[root@nat1 ~]# echo "3.14 * 3" | bc
9.42
#计算 3除以2 的结果
[root@nat1 ~]# bc <<< "scale=2; 3/2"                  #使用了HereString<<<格式
1.50
#计算 1.5 + 2.5 的结果
[root@nat1 ~]#  bc <<< "scale=2; 1.5 + 2.5"           #使用了HereString<<<格式
4.0
```

------------

#### wc

wc 命令用于计算字数。

参数：

- -c, --bytes：统计字节数。

- -m, --chars：统计字符数。

- -w, --words：统计字数。

- -l, --lines：统计行数。

- -L, --max-line-length：统计最长行的长度。







#### tee





#### tr



#### cat









## VIM 编辑器

三种模式

一般模式

n + yy 复制n

n + dd 删除n

u 回滚

n + p 粘贴n

y + 光标$ 复制该光标之后行内容

y + 光标^ 复制该光标之前行内容

d + 光标$ 删除该光标之后行内容

d + 光标^ 删除该光标之前行内容

y + w 复制词

d + w 删除词

r 替换当前字符

R 替换模式

x 剪切当前字符

X 剪切当前光标前字符

移动光标

b 前一个词 

w 后一个词

^ 行头

$ 行尾

n + G 移动到第n行

gg/H 移动到开头

G/L 移动到末尾





编辑模式

i 当前光标

I 当前行头

a 下个光标

A 当前行尾

o 添加新一行 并到下一行

O 在上一行添加一行 并跳到上一行





命令模式

:w 保存

:q 退出

:wq 保存并退出

:q! 强制退出

:wq! 强制保存退出



:set nu 显示行号

:set nonu 不显示行号

/ 查找  eg: /haha 按n下一个 N上一个



:s/old/new 替换当前行 第一个 old 为 new

:s/old/new/g 替换当前行所有 old 为 new

:%s/old/new 替换每行 第一个 old 为 new

:%s/old/new/g  替换所有 old 为 new

















## 工作问题总结

### 按行读取

```shell
# a.txt
# 1 2
# 2 3
# 3 4
for line in `cat a.txt`
do
	echo $line
done
# 1
# 2
# 2
# 3
# 3
# 4

# 在处理文件的时候 cat + for 会根据空格进行字符串分割 导致无法按行读取
# 可采用 while + read
cat a.txt | while read line
do
	echo $line
done
# 1 2
# 2 3
# 3 4
```

### while read line 读取不到最后一行

```shell
cat a.txt | while read line
do
	xxx...
done

# Unix及Unix系统里，每行结尾只有"<换行>"，即"\n" Windows系统里面，每行结尾是"<回车><换行>"，即"\r\n"
# Unix系统下的文件在Windows里打开的话，所有文字会变成一行 Windows里的文件在Unix下打开的话，在每行的结尾可能会多出一个^M符号
# Windows下新建文件，最后一行不会添加回车符或换行符 而linux新建的文件，最后一行还会添加1个换行符
# 因为我的目标文件是在windows下创建然后传到服务器上的
# 这样在利用while read line读取文件时，如果文件最后一行之后没有换行符\n，则read读取最后一行时遇到文件结束符EOF时循环即终止。
# 上面代码中，虽然此时$line内存有最后一行的内容，但程序已经没有机会再处理此行内容，因此导致了最后一行无法读取。

# 解决办法1
# 文件没有到最后一行时不会测试-n $line 当遇到文件结束（最后一行）时 仍然可以通过测试$line是否有内容来进行继续处理
while read line || [[ -n ${line} ]] 

# 解决办法2
:set ff=unix # vim修改文件格式


# 同样的问题 GIT上传代码时
git config --global core.autocrlf true	# 提交时转换未LF 检出时转换为CRLF
git config --global core.autocrlf input	# 提交时转换为LF 检出时不转换 -- 提交shell脚本
git config --global core.autocrlf false	# 提交检出时均不转换			-- 提交java脚本

git config --global core.safecrlf true	# 拒绝提交包含混合换行符的文件
git config --global core.safecrlf false	# 允许提交混合换行符的文件
git config --global core.safecrlf warn	# 提交包含混合换行符的文件时给出警告 
```

### 注意脚本执行路径与目标路径

```shell
clean_expire()
{
    TARGET_PATH=$1
    LIFESPAN=$2
    SYSDATE=`date +%Y%m%d`
    for dir in `ls $TARGET_PATH`
    do
        if [ -d $dir ]
        then
            diff=`date_diff $dir $SYSDATE`
            if [ $diff -ge $LIFESPAN ]
            then
                rm -r $TARGET_PATH/$dir
            fi
        fi
    done
}

# 发现执行不到 if [ -d $dir ] 的代码块内部 无法删除过期文件
# 问题在于 脚本当前路径 与 目标文件夹不在同一文件夹内
# 使得 if [ -d $dir ] 判断出现问题
# 修改为 if [ -d $TARGET_PATH/$dir ]
```

### wc -l 

```shell
# 1. wc -l 读取文件行数


```



不要轻易使用wc-l 统计windows下的文件







### $?  与 return 的理解

$? 是一个特殊变量，用来获取上一个**命令**的退出状态，或者上一个函数的返回值。

严格来说，Shell 函数中的 return 关键字用来表示函数的退出状态，而不是函数的返回值；Shell 不像其它编程语言，没有专门处理返回值的关键字。

实际上对于变量的定义语句也属于命令，故对于退出状态的获取需要紧邻执行语句。



### shell 连接 MYSQL



### shell 连接 Oracle





mysql found_rows 和 row_count







### “” 与 ‘’

```shell
# 首先 " " 会对文本进行解析 进行变量替换 字符转义等

# 个人理解 首先 $VAR 和 "$VAR" 是等价的
# 在进行替换的时候

# 测试
t1="!!\n"
t2='!!\n'

echo $t1
echo "$t1"

echo $t2
echo "$t2"

```













### | 与 变量

管道中操作全局变量发现，退出循环时，外部的变量打印没有变化，而变量在内部执行的时候是正确的。

原来是因为管道|创建了新的子进程，而子进程是在独立的进程空间(Context)运行了。需要跟父进程通信的话, 得使用进程间通信机制. 不是简单的变量问题。

```shell
# 解决方法 
# 改管道输入为重定向输入
while read line
do
	xxx
done < file
```

Shell 变量的作用域可以分为三种：

- 有的变量只能在函数内部使用，这叫做局部变量（local variable）
- 有的变量可以在当前 Shell 进程中使用，这叫做全局变量（global variable）
- 而有的变量还可以在子进程中使用，这叫做环境变量（environment variable）

```shell
# shell变量默认是全局变量 local可以在函数内部设置局部变量
#!/bin/bash
#定义函数
function func(){
    local num=1
}
#调用函数
func
#输出函数内部的变量
echo $num
```

```shell
# 全局变量的作用范围是当前 shell进程 在其子进程中是访问不到的
# 若想在子进程中也能访问到 则需要给变量添加export

#!/bin/bash
export a=1
bash
echo $a	# 1 
exit 0
# bash会创建一个子进程 exit从子进程返回
```

### 重定向与管道





### source 和 export

"source" 和 ". " 作用相同，在**当前shell进程**中执行shell文件中的命令

添加环境变量的时候，可以通过 /etc/profile 文件进行修改，使用export命令来使得其成为环境变量。

> 我们与Linux系统交互是会获得一个shell终端，在当前shell终端中执行bash xxx.sh 
>
> 此时会创建一个shell子进程来处理xxx.sh 若etc/profile中的环境变量不使用export，则xxx.sh是无法访问的

> 如果就是不想加export， 那么可以用 . ./xxx.sh 来执行脚本，这种方式不会创建子进程，故可以访问到加载的变量
>
> 或者在xxx.sh中 使用source /etc/profile，在shell子进程中加载并访问变量

当我们需要修改系统级别的环境变量时，可以通过编辑 /etc/profile 文件来进行修改。但修改后的环境变量并不会立即生效（原因后面会讲到），需要使用 source 命令重新加载 /etc/profile 文件，以便使修改后的环境变量立即生效。


###  interactive 和 login

> **interactive**
>
> 交互 shell 会有一个输入提示符，并且它的标准输入、输出和错误输出都会显示在控制台上。所以一般来说只要是需要用户交互的，即一个命令一个命令的输入的shell都是 interactive shell
>
> 而如果无需用户交互，它便是 non-interactive shell。通常来说如 bash script.sh 此类执行脚本的命令就会启动一个 non-interactive shell，它不需要与用户进行交互，执行完后它便会退出创建的shell

> **login**
>
> login shell：取得 bash 时需要完整的登陆流程的，就称为 login shell。举例来说，我们登陆 tty1 ~ tty6 时，需要输入用户的账号与密码，此时取得的 bash 就称为“ login shell ”；
>
> non-login shell：取得 bash 接口的方法不需要重复登陆的举动，比如我们登陆 Linux 后， 启动终端Terminal，此时那个终端接口并没有需要再次的输入账号与密码，那个 bash 的环境就称为 non-login shell了。又或者你在原本的 bash 环境下再次使用 bash 这个命令，建立了一个bash子进程，同样的也没有输入账号密码， 那第二个 bash (子程序) 也是 non-login shell 。

**一般来说，login shell 其实只会读取这两个配置文件：**

- **/etc/profile：**这是系统整体的配置，一般尽量不要修改这个文件

- **~/.bash_profile 或 ~/.bash_login 或 ~/.profile：**属于使用者个人配置，需要修改自己的数据，就写入这些文件

  > 其实 bash 的 login shell 配置只会按左到右的顺序(优先级)读取上面三个文件的其中一个

![img](https://images2015.cnblogs.com/blog/685303/201512/685303-20151210211959715-1099799706.jpg)

#### **interactive + login shell**

- 用户直接登录到机器上获得的第一个 shell
- 用户通过 ssh 连接到机器上获得的第一个 shell

加载配置文件：

-  /etc/profile

- 然后再尝试依次去加载下列三个配置文件之一

  - ~/.bash_profile

  - ~/.bash_login
  - ~/.profile

#### **non-interactive + login shell**

- bash -l script.sh 

加载配置文件：

-  /etc/profile
- 然后再尝试依次去加载下列三个配置文件之一

  - ~/.bash_profile

  - ~/.bash_login
  - ~/.profile

#### **interactive + non-login shell**

- 在已有的shell中运行bash

加载配置文件：

- /etc/bash.bashrc 和 ~/.bashrc

#### **non-interactive + non-login shell**

- bash script.sh
- ssh user@remote command

加载配置文件：

- 会去寻找环境变量 BASH_ENV，将变量的值作为文件名进行查找，如果找到便加载它

![image-20231011102049030](C:\Users\Y9000P2023\AppData\Roaming\Typora\typora-user-images\image-20231011102049030.png)

#### 配置文件源码解读

``` shell
# /etc/profile 系统级的环境和启动程序 用于登录设置

# System wide environment and startup programs, for login setup
# Functions and aliases go in /etc/bashrc

# 定义pathmunge(添加PATH)
# 若存在/usr/bin/id 
# 若不存在EUID 则获得EUID UID 
# 设置USER LOGNAME MAIL 
# 根据EUID 为PATH添加 /usr/sbin  /usr/local/sbin
# root  PATH_NEW = /usr/sbin:/usr/local/sbin:PATH_OLD   
# other PATH_NEW = PATH_OLD:/usr/local/sbin:/usr/sbin  

# 设置HOSTNAME   HISTSIZE (命令最大条数)  HISTCONTROL
# export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL

# 根据UID设置umask -- 后续更新 umask会影响新建目录、文件的默认权限
# if UID > 199 && uid = gid then umask = 002
# else umaks = 022
# 遍历/etc/profile.d/目录下的所有.sh文件，
# if 文件可读 && $PS1设置(交互式shell) then 执行文件 else 忽略文件
```

```shell
# ~/.bash_profile 特定用户环境和启动程序

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
---------------------------------------------------
# ~/.bashrc 特定用户级函数和别名

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

# User specific aliases and functions
---------------------------------------------------
# /etc/bashrc 系统级函数和别名

# System wide functions and aliases
# Environment stuff goes in /etc/profile

# 如果PS1设置(交互式shell) 则根据终端类型设置PROMPT_COMMAND 
# 若PS1为特定格式则修改提示符显示格式

# 判断是否为login shell
# 如果不是login shell
# 重新(在/etc/profile中定义过 但unset了)定义pathmunge的函数(添加PATH) 最后再unset

# 此处逻辑与/etc/profile相同
# 若 UID > 199 && 用户名 和 用户组名 一致 umask = 002
# 否则 umaks = 022
# 遍历/etc/profile.d/目录下的所有.sh文件，
# if 文件可读 && $PS1设置(交互式shell) then 执行文件 else 忽略文件
---------------------------------------------------
# ~/.bash_logout login shell 退出时执行
# 可以设置用户自定义逻辑 eg:清除临时文件、截断历史文件和记录退出登录时间等作用的命令
```

> 参考自man bash 和 man sh
>
> 当 bash 以是 sh 命启动时，配置文件的加载变成了下面这样：
>
> - interactive + login: 读取 /etc/profile 和 ~/.profile
> - non-interactive + login: 读取 /etc/profile 和 ~/.profile
> - interactive + non-login: 读取 ENV 环境变量对应的文件
> - non-interactive + non-login: 不读取任何文件
>
> 当bash 以POSIX模式启动 读取 ENV 环境变量对应的文件

#### 为什么需要source 修改的配置文件才能生效

由于获得的Shell terminal 已经加载过一遍配置文件了，但将相应的配置文件修改，不会影响到正在运行的Shell进程中变量的值，故需要使用source将配置文件重新载入Shell进程中才能生效。

### Linux shell进程

Shell 会创建子进程的方式：

- 直接执行脚本 bash xx.sh    ./xx.sh
- 通过管道执行命令 
- cat filename | while read line ； do xxx done
- bash命令

> Linux 进程由 fork创建 
>
> 创建shell子进程类似 fork + exec(bash)
>
> 而执行shell时会根据shell的类型读取一些配置文件 

### 远程连接shell

1. 使用ssh登录到服务器再执行脚本或者命令

获得的服务器终端shell是 **interactive + login shell**

在子shell进程中执行的脚本或命令，该子shell进程是**non interactive + non login shell** 会继承父shell的环境变量

2. 使用ssh执行远程命令和脚本

**non interactive + non login shell** 



### 综合案例

```shell
# 描述



```









### 





### 链接

Linux链接分两种：一种被称为硬链接（Hard Link），另一种被称为软链接也叫符号链接（Symbolic Link）。默认情况下，ln命令产生硬链接。

2.硬链接：

在Linux文件系统当中，保存在磁盘中的文件不管是什么类型都会有一个值叫做inode值（索引节点编号），硬链接允许多个文件的inode值相同，一个文件拥有多个有效路径名，这样用户就可以建立硬链接到重要文件，以防止“误删”。因为有多个文件指向同一文件，只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，硬链接才彻底消失。











### sh 与 bash  









### 锁定屏幕

在shell界面不小心按到了**ctrl + s** (vim 保存文件总是习惯性按下)， 会使得整个界面呈现"卡死"的状态

实际上  **ctrl + s**  会暂停该终端的运行

按下 **ctrl + q** 即可退出这种状态，让终端继续运行



### 空间占用

no space left on device

