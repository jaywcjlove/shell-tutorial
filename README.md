
Shell Tutorial
===

最近需要写一个脚本管理服务，花了两天时间学习了一下shell脚本，记录我的学习笔记。

## 目录

- [脚本运行](#脚本运行)
- [特殊字符](#特殊字符)
  - [注释](#注释)
  - [命令分割符](#命令分割符)
  - [结束符](#结束符)
  - [句号/圆点](#句号圆点)
  - [引号](#引号)
  - [命令替换](#命令替换)
- [操作符](#操作符)
  - [赋值](#赋值)
  - [计算操作符](#计算操作符)
  - [位操作符](#位操作符)
  - [逗号操作符](#逗号操作符)
  - [逻辑操作符](#逻辑操作符)
- [变量](#变量)
  - [变量值](#变量值)
  - [定义变量](#定义变量)
  - [只读变量](#只读变量)
  - [使用变量](#使用变量)
  - [删除变量unset](#删除变量unset)
  - [变量类型](#变量类型)
  - [内部变量](#内部变量)
  - [位置参数](#位置参数)
  - [参数替换](#参数替换)
  - [declare/typeset](#declaretypeset)
  - [变量间接引用](#变量间接引用)
  - [$RANDOM](#$RANDOM)
  - [双括号结构](#双括号结构)
- [转义字符](#转义字符)
- [测试](#测试)
  - [测试结构](#测试结构)
  - [文件测试操作符](#文件测试操作符)
  - [比较操作符](#比较操作符)
- [操作字符串](#操作字符串)
- [for/while](#forwhile)
  - [for](#for)
  - [while](#while)
  - [until](#until)
  - [嵌套循环](#嵌套循环)
  - [循环控制](#循环控制)
- [case/select](#caseselect)
  - [case](#case)
  - [select](#select)
- [函数](#函数)
  - [局部变量](#局部变量)
  - [函数参数](#函数参数)
  - [函数返回值](#函数返回值)



## 脚本运行

建立一个文本文件[demo1](./example/demo1)，以`.sh`结尾的或者不需要后缀都可以，下面是demo1的内容:

```shell
#!/bin/bash
NUM=10
printf "输出数字$NUM\n"
echo $NUM
```

直接运行是会报错误的，因为没有权限执行（Permission denied），需要给权限。下面方式

```bash
chmod +x ./demo1   # 使每个人都有执行的权限
chmod +rx ./demo1  # 使每个人都有读和执行的权限
chmod u+rx ./demo1 # 仅仅使脚本文件拥有者有读和执行的权限
chmod u+x ./demo1  # 只有自己可以执行，其它人不能执行
chmod ug+x ./demo1 # 只有自己以及同一群可以执行，其它人不能执行 
chmod 555 ./demo1  # 使每个人都有读和执行的权限
chmod 777 ./demo1 
```

当前目录下运行 [demo1](./example/demo1) 脚本

```bash
./demo1
# 输出结果===>
输出数字10
10
```

直接运行demo1脚本，这个方法是把脚本给系统中所有其他的用户使用，这个时候需要你将demo1脚本移动到目录`/usr/local/bin` 中(必须要有root的权限)，移动到 bin 目录之后，你只需使用 `demo1` 加回车就能执行脚本了。

```bash
demo1

# 输出结果===>
输出数字10
10
```

通过sh或者bash命令运行脚本，`sh scriptname` 运行一个Bash脚本将会禁止所有Bash的扩展特性。

```bash
# 你能够运行它用命令
sh demo1

# 另外也也可以用bash来执行
bash demo1
```

脚本以"#!"行开头，行将会命令解释器(sh或是bash)。`#!/bin/rm` 当你运行这个脚本时，除了这个脚本消失了之外，你不会发现更多其他的东西。

## 特殊字符

### 注释

以`#`开头的行就是注释，会被解释器忽略。注释行前面也可以有空白字符。

```shell
#--------------------------------------------
# 这是一个注释
# author：作者
# site：https://github.com/jaywcjlove
#--------------------------------------------

echo "A comment will follow." # 这里可以添加注释.
#                            ^ 注意在#前面可以有空白符
```

echo命令给出的一个转义的#字符并不会开始一个注释。同样地，出现在一些参数代换结构和在数值常量表达式中的#字符也同样不会开始一个注释。

```shell
echo "这里的 # 不会被注释"
echo '这里的 # 不会被注释'
echo 这里的 \# 不会被注释
echo 这里的 # 会被注释

echo ${PATH#*:}       # 前面的#是参数代换，不是注释.
echo $(( 2#101011 ))  # 基本转换，不是注释.
```

### 命令分割符

分号`;`命令分割符，分割符允许在同一行里有两个或更多的命令。执行[demo2](./example/demo2)，会将 demo2 拷贝输出 demo2.bak 。

```shell
echo hello; echo there         # 输出 hello 和 there
filename='demo2'               # 变量
if [ -x "$filename" ]; then    # 注意："if" and "then"需要分隔符
                               # 思考一下这是为什么?
  echo "File $filename exists."; cp $filename $filename.bak
else
  echo "File $filename not found."; touch $filename
fi; echo "File test complete."
```

### 结束符

双分号`;;`，case语句分支的结束符。[demo3](./example/demo3)

```shell
read Keypress
case "$Keypress" in
  [[:lower:]]   ) echo "Lowercase letter";;
  [[:upper:]]   ) echo "Uppercase letter";;
  [0-9]         ) echo "Digit";;
  *             ) echo "Punctuation, whitespace, or other";;
esac      #  允许字符串的范围出现在[]中,
          #+ 或者POSIX风格的[[中.
exit 0
```

### 句号/圆点

作为一个文件名的组成部分`.`，当点`.`以一个文件名为前缀时，起作用使该文件变成了隐藏文件。这种隐藏文件ls一般是不会显示出来的。

作为目录名时，单个点（.）表示当前目录，两个点(..)表示上一级目录（或称为父目录）。

点(.)字符匹配。作为正则表达式的一部分,匹配字符时，单点（.）表示匹配任意一个字符。

### 引号

引号一个很重要的作用是保护命令行上的一个参数不被shell解释，而把此参数传递给要执行的程序来处理它。

```bash
bash$ grep '[Ff]ile' demo*
demo2:filename='demo2'
demo2:if [ -x "$filename" ]; then    # 注意："if" and "then"需要分隔符
demo2:  echo "File $filename exists."; cp $filename $filename.bak
```

引号能改掉echo's不换行的“习惯”。

```bash
bash$ echo $(ls -al)
total 24 drwxr-xr-x 5 kacperwang staff 170 1 22 16:47 . drwxr-xr-x 5 kacperwang staff 170 1 22 13:29 .. -rwxr-xr-x 1 kacperwang staff 58 1 22 16:20 demo1 -rwxr-xr-x 1 kacperwang staff 325 1 22 16:39 demo2 -rwxr-xr-x 1 kacperwang staff 899 1 22 17:16 demo3

bash$ echo "$(ls -al)"
total 24
drwxr-xr-x  5 kacperwang  staff  170  1 22 16:47 .
drwxr-xr-x  5 kacperwang  staff  170  1 22 13:29 ..
-rwxr-xr-x  1 kacperwang  staff   58  1 22 16:20 demo1
-rwxr-xr-x  1 kacperwang  staff  325  1 22 16:39 demo2
-rwxr-xr-x  1 kacperwang  staff  899  1 22 17:16 demo3
```

**单引号**

```shell
str='this is a string'
```

单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的  
单引号字串中不能出现单引号（对单引号使用转义符后也不行）  

**双引号**

```shell
your_name='qinjx'
str="Hello, I know your are \"$your_name\"! \n"
```

双引号里可以有变量  
双引号里可以出现转义字符  

### 命令替换

命令替换"`"，将会重新分配一个命令甚至是多个命令的输出；它会将命令的输出如实地添加到另一个上下文中。[demo4](./example/demo4)

```shell
script_name=`basename $0`
echo "The name of this script is $script_name."

textfile_listing=`ls *`
# 变量中包含了当前工作目录下所有的*文件
echo $textfile_listing
```

通过这个符号，批量删除文件

```shell
rm `cat filename`   # "filename" 包含了需要被删除的文件列表
# 可能会产生"参数列表太长"的错误
# 更好的方法是              xargs rm -- < filename 
# ( -- 同时覆盖了那些以"-"开头的文件所产生的特殊情况 )
```

## 操作符

### 赋值

变量赋值，初始化或改变一个变量的值，通用的变量赋值操作符，可以用于数值和字符串的赋值

```shell
var=27
category=minerals  # "="字符后面不能加空白字符.
```

不要把"="赋值操作符和`=`测试操作符搞混了。

```shell
# = 用于测试操作符
if [ "$string1" = "$string2" ]
# if [ "X$string1" = "X$string2" ] 会更安全,
# 它为了防止其中有一个字符串为空时产生错误信息.
# (增加的"X"字符可以互相抵消.) 
then
   command
fi
```

### 计算操作符

| 操作符 | 描述 | 操作符 | 描述 | 操作符 | 描述 |
| ---- | ---- | ---- | ---- |---- | ---- |
| `+` | 加 | `/` | 除 | `**` | 求幂 |
| `-` | 减 | `*` | 乘 | `%` | 求模[demo6](./example/demo6) |

```shell
# Bash在版本2.02引入了"**"求幂操作符.
let "z=5**3"
echo "z = $z"   # z = 125

# 求模（它返回整数整除一个数后的余数）
let "y=5 % 3"
echo "y = $y"   # y = 2
```

| 操作符 | 描述 | 
| ---- | ---- | 
| `+=` | 加等(plus-equal) 把原变量值增加一个常量并重新赋值给变量 |
| `-=` | 减等(minus-equal) 把原变量值减少一个常量并重新赋值给变量 |
| `*=` | 乘等(times-equal) 把原变量值乘上一个常量并重新赋值给变量 |
| `/=` | 除等(slash-equal) 把原变量值除以一个常量并重新赋值给变量 |
| `%=` | 模等(mod-equal) 把原变量值除以一个常量整除（即取模）并重新赋余数的值给变量 |

```shell
let "var += 5" # 会使变量var值加了5并把值赋给var.
let "var *= 4" # 使变量var的值乘上4并把值赋给var.
```

例子：
- [用10种不同的方法计数到11](./example/demo8)
- [最大公约数](./example/demo8)
- [整数变量符号的长整数测试](./example/demo9)，到2.05b版本为止，Bash支持64位的整数，以前的版本会有长度溢出错误。
- [Bash不能处理浮点计算,它会把含有小数点的数当成字符串。](./example/demo10)


### 位操作符

位操作符很少在脚本中使用。他们主要用于操作和测试从端口或sockets中读到的数据。“位运算”更多地用于编译型的语言，比如说C和C++，它们运行起来快地像飞。

| 操作符 | 描述 | 操作符 | 描述 |
| ---- | ---- | ---- | ---- |
| `<<` | 位左移（每移一位相当乘以2） | `|` | 位或 |
| `<<=` | 位左移赋值 | `|=` | 位或赋值 |
| `>>` | 位右移（每移一位相当除以2） | `~` | 位反 |
| `>>=` | "位右移赋值"（和<<=相反） | `!` | 位非 |
| `&` | 位与 | `^` | 位或 |
| `&=` | 位于赋值 | `^=` | 位或赋值 |


```shell
# <<=
# "位左移赋值"
let "var <<= 2" 结果使var的二进制值左移了二位（相当于乘以4）
```

### 逻辑操作符

逻辑与`&&`

```shell
if [ $condition1 ] && [ $condition2 ]
# 等同于:  if [ $condition1 -a $condition2 ]
# 如果condition1和condition2都为真则返回真...
fi;

if [[ $condition1 && $condition2 ]]    # Also works.
# 注意&&操作不能在[ ... ]结构中使用.
fi;
```

逻辑或`||`

```shell
if [ $condition1 ] || [ $condition2 ]
# 等同于:  if [ $condition1 -o $condition2 ]
# 如果condition1和condition2有一个为真则返回真...
fi;
if [[ $condition1 || $condition2 ]]    # Also works.
# 注意||操作不能在[ ... ]结构中使用.
fi;
```

使用&&和||进行混合条件测试[demo11](./example/demo11)。在算术计算的环境中，&&和||操作符也可以使用。

```
bash$ echo $(( 1 && 2 )) $((3 && 0)) $((4 || 0)) $((0 || 0))
 1 0 1 0
```

### 逗号操作符

逗号`,`操作符连接两个或更多的算术操作。所有的操作都被求值(可能会有副作用)，但只返回最后一个操作的结构。[demo5](./example/demo5)

```shell
let "t1 = ((5 + 3, 7 - 1, 15 - 4))"
echo "t1 = $t1"               # t1 = 11

let "t2 = ((a = 9, 15 / 3))"  # 初始化"a"并求"t2"的值.
echo "t2 = $t2    a = $a"     # t2 = 5    a = 9
```

## 变量

变量，是脚本编程中的如何进行数据表现的办法。它们可以在算术计算中作为操作数，在一个字符串表达式中作为符号表达抽象的意义或是其他的其它意义。变量是表示计算机内存中保存一种数据需要占的一个位置或一组的位置的标识。

### 变量值

如果variable1是一个变量的名字，那么$variable1就是引用这个变量的值――即这个变量它包含的数据。[变量赋值与替换例子](./example/demo12)

```shell
t1=12
echo $t1
```

一个未初始化的变量有一个”null”值――表示从没有被赋值过（注意null值不等于零）。在一个变量从未赋值之前就使用它通常会引起问题。然而，仍然有可能在执行算术计算时使用一个未初始化的变量。

```shell
echo "$uninitialized"      # (blank line)
let "uninitialized += 5"   # Add 5 to it.
echo "$uninitialized"      # 5

#  结论:
#  一个未初始化的变量没有值，
#+ 但是似乎它在算术计算中的值是零。
#  这个无法证实（也可能是不可移植）的行为。
```

### 定义变量

```shell
# 变量名不加美元符号
your_var="elaine"
# 重新定义
your_var="newname"
```

**注意⚠️**

1. 首个字符必须为字母（a-z，A-Z）。
1. 中间不能有空格，可以使用下划线（_），等号左右也不能有空格。
1. 不能使用标点符号。
1. 不能使用bash里的关键字（可用help命令查看保留关键字）。

### 只读变量

```shell
#!/bin/bash
github="https://jaywcjlove.github.io"
readonly github
github="https://www.github.com"

# 运行脚本，结果如下：
/bin/sh: NAME: This variable is read only.
```

### 使用变量

```shell
your_var="github"
echo $your_var
echo ${your_var}
echo "your name is ${your_var}-l"
```

### 删除变量unset

变量被删除后不能再次使用。unset 命令不能删除只读变量。

```shell
myUrl="https://jaywcjlove.github.io"
unset myUrl
echo $myUrl
```

### 变量类型

不同与许多其他的编程语言，Bash不以"类型"来区分变量。本质上来说，Bash变量是字符串，但是根据环境的不同，Bash允许变量有整数计算和比较。其中的决定因素是变量的值是不是只含有数字。

1. **局部变量** 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
2. **环境变量** 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
3. **shell变量** shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

### 内部变量

| 内部变量 | 说明 |
| ---- | ---- |
| $BASH | Bash二进制程序文件的路径 |
| $BASH_ENV | 该环境变量保存一个Bash启动文件路径，当启动一个脚本程序时会去读该环境变量指定的文件。 |
| $BASH_SUBSHELL | 一个指示子shell(subshell)等级的变量。它是Bash版本3新加入的。 |
| $BASH_VERSINFO[n] | 这个数组含有6个元素，指示了安装的Bash版本的信息。它和$BASH_VERSION相似，但它们还是有一些小小的不同。|
| $BASH_VERSION | 安装在系统里的Bash版本。|
| $DIRSTACK | 在目录堆栈里面最顶端的值(它受pushd和popd的控制) |
| $EDITOR | 由脚本调用的默认的编辑器，一般是vi或是emacs。 |
| $EUID | 有效用户ID | 
| $FUNCNAME | 当前函数的名字 |
| $GLOBIGNORE | 由通配符(globbing)扩展的一列文件名模式。|
| $GROUPS | 目前用户所属的组|
| $HOME | 用户的家目录，通常是/home/username |
| $HOSTNAME | 在系统启动时由一个初始化脚本中用hostname命令给系统指派一个名字。然而，gethostname()函数能设置Bash内部变量E$HOSTNAME。|
| $HOSTTYPE | 机器类型，像$MACHTYPE一样标识系统硬件。|
| $IFS | 内部字段分隔符 |
| $IGNOREEOF | 忽略EOF：在退出控制台前有多少文件结尾标识（end-of-files,control-D）会被shell忽略。|
| $LC_COLLATE | 它常常在.bashrc或/etc/profile文件里被设置，它控制文件名扩展和模式匹配的展开顺序。|
| $LINENO | 这个变量表示在本shell脚本中该变量出现时所在的行数。它只在脚本中它出现时有意义，它一般可用于调试。|
| $MACHTYPE | 机器类型，识别系统的硬件类型。|
| $OLDPWD | 上一次工作的目录("OLD-print-working-directory",你上一次进入工作的目录)|
| $TZ | 时区 |
| $MAILCHECK | 每隔多少秒检查是否有新的信件 |
| $OSTYPE | 操作系统类型 |
| $MANPATH man | 指令的搜寻路径 |
| $PATH | 可执行程序文件的搜索路径。一般有/usr/bin/, /usr/X11R6/bin/, /usr/local/bin,等等。|
| $PIPESTATUS | 此数组变量保存了最后执行的前台管道的退出状态。相当有趣的是，它不一定和最后执行的命令的退出状态一样。|
| $PPID | 一个进程的$PPID变量保存它的父进程的进程ID(pid)。用这个变量和pidof命令比较。|
| $PROMPT_COMMAND | 这个变量在主提示符前($PS1显示之前)执行它的值里保存的命令。|
| $PS1 | 这是主提示符（第一提示符），它能在命令行上看见。|
| $PS2 | 副提示符（第二提示符），它在期望有附加的输入时能看见。它显示像">"的提示。|
| $PS3 | 第三提示符。它在一个select循环里显示 (参考例子 10-29)。|
| $PS4 | 第四提示符，它在用-x选项调用一个脚本时的输出的每一行开头显示。它通常显示像"+"的提示。|
| $PWD | 工作目录(即你现在所处的目录) ，它类似于内建命令pwd。|
| $REPLY | 没有变量提供给read命令时的默认变量．这也适用于select命令的目录，但只是提供被选择的变量项目编号而不是变量本身的值。 |
| $SECONDS | 脚本已运行的秒数。|
| $SHELLOPTS | 已经激活的shell选项列表，它是一个只读变量。|
| $SHLVL | SHELL的嵌套级别．指示了Bash被嵌套了多深．在命令行里，$SHLVL是1，因此在一个脚本里，它是2 |
| $TMOUT | 如果$TMOUT环境变量被设为非零值时间值time，那么经过time这么长的时间后，shell提示符会超时．这将使此shell退出登录 |
| $UID | 用户ID号，这是当前用户的用户标识号，它在/etc/passwd文件中记录。|

### 位置参数

| 参数处理 | 说明 |
| ---- | ---- |
| `$#` | 传递到脚本的参数个数 |
| `$*` | 以一个单字符串显示所有向脚本传递的参数。如"∗"用「"」括起来的情况、以"1 2…n"的形式输出所有参数。 |
| `$$` | 脚本运行的当前进程ID号 |
| `$!` | 后台运行的最后一个进程的ID号 |
| `$@` | 与∗相同，但是使用时加引号，并在引号中返回每个参数。如"@"用「"」括起来的情况、以"1""2" … "$n" 的形式输出所有参数。 |
| `$-` | 显示Shell使用的当前选项，与set命令功能相同。 |
| `$?` | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |

### 参数替换

**${parameter}**：和$parameter是相同的，都是表示变量parameter的值，可以把变量和字符串连接。

```shell
your_id=${USER}-on-${HOSTNAME}
echo "$your_id"
#
echo "Old \$PATH = $PATH"
PATH=${PATH}:/opt/bin  #在脚本的生存期内，能额外增加路径/opt/bin到环境变量$PATH中去.
echo "New \$PATH = $PATH"
```

**${parameter-default}, ${parameter:-default}**：如果变量没有被设置，使用默认值。`${parameter-default}`和`${parameter:-default}`几乎是相等的。它们之间的差别是：当一个参数已被声明，但是值是NULL的时候两者不同。

```shell
echo ${username-`whoami`}
# 如果变量$username还没有被设置，则把命令`whoami`的结果赋给该变量
```

**${parameter=default}, ${parameter:=default}**：如果变量parameter没有设置，把它设置成默认值。除了引起的当变量被声明且值是空值时有些不同外，两种形式几乎相等。

```shell
echo "===== \${parameter+alt_value} ====="
echo

a=${param1+xyz}; echo "a = $a"      # a =

param2=
a=${param2+xyz}
echo "a = $a"      # a = xyz

param3=123
a=${param3+xyz}
echo "a = $a"      # a = xyz

echo
echo "====== \${parameter:+alt_value} ======"
a=${param4:+xyz}
echo "a = $a"      # a =

param5=
a=${param5:+xyz}
echo "a = $a"      # a =
# 产生与a=${param5+xyz}不同。

param6=123
a=${param6+xyz}
echo "a = $a"      # a = xyz
```

**${parameter?err_msg}, ${parameter:?err_msg}**：如果变量parameter已经设置，则使用该值，否则打印err_msg错误信息。[demo20](./example/demo20)

```shell
#!/bin/bash
# 变量替换和"usage"信息

: ${1?"Usage: $0 ARGUMENT"}
#  如果没有提供命令行参数则脚本在这儿就退出了,
#+ 并打印了错误信息.
#    usage-message.sh: 1: Usage: usage-message.sh ARGUMENT

echo "These two lines echo only if command-line parameter given."
echo "command line parameter = \"$1\""

exit 0  # 仅在命令行参数提供时，才会在这儿退出.

# 分别检查有命令行参数和没有命令行参数时的退出状态。
# 如果有命令行参数,则"$?"为0.
# 否则, "$?"为1.
```

**${#var}**：字符串长度（即变量$var的字符个数）。对于数组来说，${#array}是数组的第一个元素的升序。`${#*}和${#@}` 表示位置参数的个数。对于一个数组来说，`${#array[*]}`和`${#array[@]}`表示数组中元素的个数。

```shell
E_NO_ARGS=65

if [ $# -eq 0 ]  # 必须要有命令行参数给这个演示程序.
then
  echo "Please invoke this script with one or more command-line arguments."
  exit $E_NO_ARGS
fi  

var01=abcdEFGH28ij
echo "var01 = ${var01}"
echo "Length of var01 = ${#var01}"
# 现在,让我们在变量值内嵌入一个空格.
var02="abcd EFGH28ij"
echo "var02 = ${var02}"
echo "Length of var02 = ${#var02}"

echo "Number of command-line arguments passed to script = ${#@}"
echo "Number of command-line arguments passed to script = ${#*}"

exit 0
```

**${var#Pattern}, ${var##Pattern}**：删除从$var前端开始的最短或最长匹配$Pattern的字符串。[demo22](./example/demo22)

```shell
echo `basename $PWD`        # 当前工作目录的基本名字.
echo "${PWD##*/}"           # 当前工作目录的基本名字.
echo
echo `basename $0`          # 脚本名.
echo $0                     # 脚本名.
echo "${0##*/}"             # 脚本名.
echo
filename=test.data
echo "${filename##*.}"      # data
                            # 文件的扩展名.
```

### declare/typeset

declare或typeset内建命令(它们是完全相同的)可以用来限定变量的属性。这是在某些编程语言中使用的定义类型不严格的方式。

- 命令declare是bash版本2之后才有的。
- 命令typeset也可以在ksh脚本中运行。

**选项**

[用declare来声明变量类型](./example/demo23)

| 参数 | 说明 | 例子 |
| ---- | ---- | ---- |
| `-r` | 只读 | `declare -r var1` |
| `-i` | 整数 | `declare -i number;number=3;` |
| `-a` | 数组 | `declare -a indices` |
| `-f` | 函数 | `declare -f` 会列出所有在此脚本前面已定义的函数|
| `-x export` | 函这样将声明一个变量作为脚本的环境变量而被导出。 | `declare -x var3` |
| `-x var=$value` | declare命令允许在声明变量类型的时候同时给变量赋值。| `declare -x var3=373` |

### 变量间接引用

假设一个变量的值是第二个变量的名字。这样要如何才能从第一个变量处重新获得第二个变量的值？例如，`a=letter_of_alphabet`和`letter_of_alphabet=z`，是否能由a引用得到z ? 这确实可以办到，这种技术被称为间接引用。

```shell
a=letter_of_alphabet   # 变量"a"保存着另外一个变量的名字.
letter_of_alphabet=z
# 直接引用.
echo "a = $a"          # a = letter_of_alphabet

# 间接引用.
eval a=\$$a
echo "Now a = $a"      # 现在 a = z
exit 0
```

### $RANDOM

$RANDOM是Bash的一个返回伪随机整数(范围为0 - 32767)的内部函数(而不是一个常量或变量)，它不应该用于产生加密的密钥。[demo25](./example/demo25)

```shell
# 模拟掷骰子.
SPOTS=6   # 模除 6 会产生 0 - 5 之间的值.
          # 结果增1会产生 1 - 6 之间的值.
          # 多谢Paulo Marcel Coelho Aragao的简化.
die1=0
die2=0
# 这会比仅设置SPOTS=7且不增1好?为什么会好？为什么会不好?

# 单独地掷每个骰子，然后计算出正确的机率.

    let "die1 = $RANDOM % $SPOTS +1" # 掷第一个.
    let "die2 = $RANDOM % $SPOTS +1" # 掷第二个.
    #  上面的算术式中，哪个操作符优先计算 --
    #+ 取模 (%) 还是 加法 (+)?

let "throw = $die1 + $die2"
echo "Throw of the dice = $throw"

exit 0
```

### 双括号结构

用`((...))`结构来使用C风格操作符来处理变量。[demo26](./example/demo26)

```shell
(( a = 23 ))  # 以C风格来设置一个值，在"="两边可以有空格.
echo "a (initial value) = $a"

(( a++ ))     # C风格的计算后自增.
echo "a (after a++) = $a"

(( a-- ))     # C风格的计算后自减.
echo "a (after a--) = $a"


(( ++a ))     # C风格的计算前自增.
echo "a (after ++a) = $a"

(( --a ))     # C风格的计算前自减.
echo "a (after --a) = $a"
```

## 转义字符

在单个字符前面的转义符`\`告诉shell不必特殊解释这个字符，只把它当成字面上的意思。但在一些命令和软件包里，比如说echo和sed,转义一个字符可能会引起一个相反的效果－－因为它们可能触发那个字符的特殊意思。[demo13](./example/demo13)

`\r` 回车  
`\n` 换行  
`\c` 不换行  
`\t` 水平制表符  
`\v` 垂直制表符  
`\a` 表示“警告”（蜂鸣或是闪动）  
`\\` 反斜杠字符  
`\0ddd` 将自负表示成1到3的八进制数值  


## 退出/退出状态

`$?` 变量用于测试脚本中的命令执行结果非常的有用。[demo14](./example/demo14)

```shell
echo hello
echo $?    # 因为上一条命令执行成功，打印0。

lskdf      # 无效命令。
echo $?    # 因为上面的无效命令执行失败，打印一个非零的值。

exit 113   # 返回113状态码给shell。
           # 可以运行脚本结束后立即执行命令"echo $?" 检验。

#  依照惯例,命令'exit 0'表示执行成功,
#+ 当产生一个非零退出值时表示一个错误或是反常的条件。
```

下面这些退出状态码，用于保留(reserved meanings) 的含义，不应该在用户脚本使用。

| Exit Code Number | Meaning | Example | Comments |
| ---- | ---- | ---- | ---- |
| 1 | Catchall for general errors | `let "var1 = 1/0"` | Miscellaneous errors, such as "divide by zero" |
| 2 | Misuse of shell builtins (according to Bash documentation)  | - |  Seldom seen, usually defaults to exit code 1 |
| 126 | Command invoked cannot execute  | - |  Permission problem or command is not an executable |
| 127 | "command not found" | - |  Possible problem with $PATH or a typo |
| 128 | Invalid argument to exit | `exit 3.14159` |  exit takes only integer args in the range 0 - 255 (see footnote) |
| 128+n | Fatal error signal "n" | `kill -9 $PPID` | of script $? returns 137 (128 + 9) |
| 130 | Script terminated by Control-C  | - |  Control-C is fatal error signal 2, (130 = 128 + 2, see above) |
| 255* | Exit status out of range | `exit -1` | exit takes only integer args in the range 0 - 255 |


## 测试

### 测试结构

一个if/then结构能包含嵌套的比较和测试。

```shell
echo "Testing \"false\""
if [ "false" ]              #  "false"是一个字符串.
then
  echo "\"false\" is true." #+ 它被测试为真.
else
  echo "\"false\" is false."
fi            # "false"为真.
```

Else if 和 elif

elif是else if的缩写。作用是在一个if/then里嵌入一个内部的if/then结构。

`[[]]`结构比Bash版本的`[]`更通用。用`[[ ... ]]`测试结构比用`[ ... ]`更能防止脚本里的许多逻辑错误。比如说，`&&`,`||`,`<`和`>`操作符能在一个`[[]]`测试里通过，但在`[]`结构会发生错误。

`(( ))`结构扩展并计算一个算术表达式的值。如果表达式值为0，会返回1或假作为退出状态码。一个非零值的表达式返回一个0或真作为退出状态码。这个结构和先前test命令及`[]`结构的讨论刚好相反。

### 文件测试操作符

如果下面的条件成立返回真。[demo15](./example/demo15)

| 操作符 | 描述 |
| ---- | ---- |
| -e | 文件存在 |
| -a | 文件存在，这个和-e的作用一样. 它是不赞成使用的，所以它的用处不大。 |
| -f | 文件是一个普通文件(不是一个目录或是一个设备文件) |
| -s | 文件大小不为零 |
| -d | 文件是一个目录 |
| -b | 文件是一个块设备(软盘，光驱，等等。) |
| -c | 文件是一个字符设备(键盘，调制解调器，声卡，等等。) |
| -p | 文件是一个管道 |
| -h | 文件是一个符号链接 |
| -L | 文件是一个符号链接 |
| -S | 文件是一个socket |
| -t | 文件(描述符)与一个终端设备相关。|
| -r | 文件是否可读 (指运行这个测试命令的用户的读权限) |
| -w | 文件是否可写 (指运行这个测试命令的用户的读权限) |
| -x | 文件是否可执行 (指运行这个测试命令的用户的读权限) |
| -g | 文件或目录的设置-组-ID(sgid)标记被设置。 |
| -u | 文件的设置-用户-ID(suid)标志被设置 |
| -k | 粘住位设置 |
| -N | 文件最后一次读后被修改 |
| f1 -nt f2 | 文件f1比f2新 |
| f1 -ot f2 | 文件f1比f2旧 |
| f1 -ef f2 | 文件f1和f2 是相同文件的硬链接 |
| ! | "非" -- 反转上面所有测试的结果(如果没有给出条件则返回真)。|

**注意⚠️**

1. `-t` 这个测试选项可以用于检查脚本中是否标准输入 ([ -t 0 ])或标准输出([ -t 1 ])是一个终端。
1. `-g` 如果一个目录的sgid标志被设置，在这个目录下创建的文件都属于拥有此目录的用户组，而不必是创建文件的用户所属的组。这个特性对在一个工作组里的同享目录很有用处。

### 比较操作符

二元比较操作符比较两个变量或是数值。注意整数和字符串比较的分别。

**整数比较**

[demo16](./example/demo16)

| 比较操作符 | 描述 | 例子 |
| ---- | ---- | ---- |
| `-eq` | 等于 | `if [ "$a" -eq "$b" ]` |
| `-ne` | 不等于 | `if [ "$a" -ne "$b" ]` | 
| `-gt` | 大于 | `if [ "$a" -gt "$b" ]` |
| `-ge` | 大于等于 | `if [ "$a" -ge "$b" ]` |
| `-lt` | 小于 | `if [ "$a" -lt "$b" ]` |
| `-le` | 小于等于 | `if [ "$a" -le "$b" ]` |
| `<` | 小于(在双括号里使用) | `(("$a" < "$b"))` |
| `<=` | 小于等于 (在双括号里使用) | `(("$a" <= "$b"))` |
| `>` | 大于 (在双括号里使用) | `(("$a" > "$b"))` |
| `>=` | 大于等于(在双括号里使用) | `(("$a" >= "$b"))` |

**字符串比较**

| 比较操作符 | 描述 | 例子 |
| ---- | ---- | ---- |
| = | 等于 | `if [ "$a" = "$b" ]` |
| == | 等于，它和=是同义词。 | `if [ "$a" == "$b" ]` |
| != | 不相等，操作符在[[ ... ]]结构里使用模式匹配. | `if [ "$a" != "$b" ]` |
| < | 小于，依照ASCII字符排列顺序，注意"<"字符在[ ] 结构里需要转义 | `if [[ "$a" < "$b" ]]` `if [ "$a" \< "$b" ]` |
| > | 大于，依照ASCII字符排列顺序，注意">"字符在[ ] 结构里需要转义. | `if [[ "$a" > "$b" ]]` `if [ "$a" \> "$b" ]`| 
| -z | 字符串为"null"，即是指字符串长度为零。 | - |
| -n | 字符串不为"null"，即长度不为零。 | - |

**混合比较**

| 比较操作符 | 描述 | 例子 |
| ---- | ---- | ---- |
| -a | 逻辑与，如果exp1和exp2都为真，则exp1 -a exp2返回真。 | `if [ "$exp1" -a "$exp2" ]` |
| -o | 逻辑或，只要exp1和exp2任何一个为真，则exp1 -o exp2 返回真。 | `if [ "$exp1" -o "$exp2" ]` |

在一个混合测试中，把一个字符串变量引号引起来可能还不够。如果$string变量是空的话，表达式`[ -n "$string" -o "$a" = "$b" ]`在一些Bash版本中可能会引起错误。安全的办法是附加一个外部的字符串给可能有空字符串变量比较的所有变量，`[ "x$string" != x -o "x$a" = "x$b" ]` (x字符可以互相抵消)

## 操作字符串

Bash已经支持了令人惊讶的字符串操作的数量。不一致的命令语法和冗余的功能，导致真的学起来有困难。

### 字符串长度

```shell
String=abcABC123ABCabc

echo ${#String}                 # 15
echo `expr length $String`      # 15
echo `expr "$String" : '.*'`    # 15
```

匹配字符串开头的字串的长度，下面两种方法的 $substring 是一个正则表达式。

`expr match "$string" '$substring'`   
`expr "$string" : '$substring'`   

```shell
String=abcABC123ABCabc
#       └------┘
#       
echo `expr match "$String" 'abc[A-Z]*.2'`   # 8
echo `expr "$String" : 'abc[A-Z]*.2'`       # 8
```

### 索引

`expr index $string $substring` 在字符串$string中$substring第一次出现的数字位置

```shell
String=abcABC123ABCabc
echo `expr index "$String" C12`             # 6
                                             # C 字符的位置.

echo `expr index "$String" 1c`              # 3
# 'c' (in #3 position) matches before '1'.
```

### 字串提取

`${string:position}` 把$string中从第$postion个字符开始字符串提取出来。如果$string是"*"或"@"，则表示从位置参数中提取第$postion后面的字符串。  
`${string:position:length}` 把$string中$postion个字符后面的长度为$length的字符串提取出来。[demo18](./example/demo18)

```shell
# 字串提取
String=abcABC123ABCabc
#       0123456789.....
#       以0开始计算.

echo ${String:0}                            # abcABC123ABCabc
echo ${String:1}                            # bcABC123ABCabc
echo ${String:7}                            # 23ABCabc
echo ${String:7:3}                          # 23A
                                            # 提取的字串长为3

# 有没有可能从字符串的右边结尾处提取?
    
echo ${String:-4}                           # abcABC123ABCabc
# 默认是整个字符串，就相当于${parameter:-default}.
# 然而. . .

echo ${String:(-4)}                         # Cabc 
echo ${String: -4}                          # Cabc
# 这样,它可以工作了.
# 圆括号或附加的空白字符可以转义$position参数.
```

### 字串移动

`${string#substring}`从$string左边开始，剥去最短匹配$substring子串。  
`${string##substring}`从$string左边开始，剥去最长匹配$substring子串。  
`${string%substring}` 从$string结尾开始，剥去最短匹配$substring子串。  
`${string%%substring}`从$string结尾开始，剥去最长匹配$substring子串。  

```shell
String=abcABC123ABCabc
#       ├----┘     ┆
#       └----------┘

echo ${String#a*C}      # 123ABCabc
# 剥去匹配'a'到'C'之间最短的字符串.

echo ${String##a*C}     # abc
# 剥去匹配'a'到'C'之间最长的字符串.


String=abcABC123ABCabc
#       ┆           ||
#       └------------┘

echo ${String%b*c}      # abcABC123ABCa
# 从$String后面尾部开始，剥去匹配'a'到'C'之间最短的字符串.

echo ${String%%b*c}     # a
# 从$String后面尾部开始，剥去匹配'a'到'C'之间最长的字符串.
```

### 用awk处理字符串

Bash脚本可以调用awk的字符串操作功能来代替它自己内建的字符串操作符

```shell
String=23skidoo1
#      012345678    Bash
#      123456789    awk
# 注意上面两个程序对索引的不同处理:
# Bash把字符串的第一个字符的标号称为'0'。
# Awk把字符串的第一个字符的标号称为'1'。

echo ${String:2:4} # position 3 (0-1-2), 4 characters long
                                         # skid

# 在awk中与Bash的${string:pos:length}等同的是substr(string,pos,length)。
echo | awk '{ 
  print substr("'"${String}"'",3,4)      # skid
}'
#  用一个空的"echo"由管道传一个空的输入给awk,
#+ 这样就不必提供一个文件名给awk。
exit 0
```

## for/while

重复一些命令的代码块,如果条件不满足就退出循环。

### for

在循环的每次执行中，arg将顺序的存取list中列出的变量，下面是一个基本的循环结构。[demo27](./example/demo27)

> for arg in [list]  
> do   
>    command(s)...   
> done  

每个`[list]`中的元素都可能包含多个参数，在处理参数组时，这是非常有用的，使用set命令来强制解析每个`[list]`中的元素。并且分配每个解析出来的部分到一个位置参数中。

循环的一个简单例子

```shell
for planet in Mercury Venus Earth Mars Jupiter Saturn Uranus Neptune Pluto
do
  echo $planet  # 每个行星被单独打印在一行上.
done
```

### while

一个while循环可以有多个判断条件，但是只有最后一个才能决定是否退出循环。然而这需要一种有点不同的循环语法。

> while [condition]  
> do   
>   command...   
> done  

```shell
# --------------------------
# 简单的while循环
# --------------------------
var0=0
LIMIT=10

while [ "$var0" -lt "$LIMIT" ]
do
  echo -n "$var0 "        # -n 将会阻止产生新行。
  #             ^           空格,数字之间的分隔。
  var0=`expr $var0 + 1`   # var0=$(($var0+1))  也可以。
                          # var0=$((var0 + 1)) 也可以。
                          # let "var0 += 1"    也可以。
done                      # 使用其他的方法也行。
# --------------------------
# 多条件的while循环
# --------------------------
var1=unset
previous=$var1

while echo "previous-variable = $previous"
      echo
      previous=$var1
      [ "$var1" != end ] # 记录之前的$var1.
      # 这个"while"循环中有4个条件, 但是只有最后一个能控制循环.
      # 退出状态由第4个条件决定.
do
echo "Input variable #1 (end to exit) "
  read var1
  echo "variable #1 = $var1"
done 
exit 0
```

### until

这个结构在循环的顶部判断条件，并且如果条件一直为false那就一直循环下去。(与while相反)。

> until [condition-is-true]  
> do   
>   command...   
> done  

**注意⚠️**

1. until循环的判断在循环的顶部，这与某些编程语言是不同的。
2. 与for循环一样，如果想把do和条件放在一行里，就使用";"。

> until [condition-is-true] ; do

```shell
END_CONDITION=end
until [ "$var1" = "$END_CONDITION" ]
# 在循环的顶部判断条件.
do
  echo "Input variable #1 "
  echo "($END_CONDITION to exit)"
  read var1
  echo "variable #1 = $var1"
done
exit 0
```

### 嵌套循环

嵌套循环就是在一个循环中还有一个循环，内部循环在外部循环体中。[demo28](./example/demo28)

```shell
outer=1             # 设置外部循环计数.
# 开始外部循环.
for a in 1 2 3 4 5
do
  echo "Pass $outer in outer loop."
  echo "---------------------"
  inner=1           # 重设内部循环的计数.

  # ===============================================
  # 开始内部循环.
  for b in 1 2 3 4 5
  do
    echo "Pass $inner in inner loop."
    let "inner+=1"  # 增加内部循环计数.
  done
  # 内部循环结束.
  # ===============================================

  let "outer+=1"    # 增加外部循环的计数.
  echo              # 每次外部循环之间的间隔.
done               
# 外部循环结束.

exit 0
```

### 循环控制

影响循环行为的命令 `break`， `continue`， break命令将会跳出循环，continue命令将会跳过本次循环下边的语句，直接进入下次循环。[demo29](./example/demo29)

**continue：** continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

```shell
LIMIT=19  # 上限

echo "Printing Numbers 1 through 20 (but not 3 and 11)."

a=0

while [ $a -le "$LIMIT" ]
do
  a=$(($a+1))
  if [ "$a" -eq 3 ] || [ "$a" -eq 11 ]  # Excludes 3 and 11.
  then
    continue      # 跳过本次循环剩下的语句.
  fi
  echo -n "$a "   # 在$a等于3和11的时候,这句将不会执行.
done 
```

**break：** break命令允许跳出所有循环（终止执行后面的所有循环）。

下面的例子中，脚本进入死循环直至用户输入数字大于5。要跳出这个循环，返回到shell提示符下，就要使用break命令。

```shell
while :
do
    echo -n "Input a number between 1 to 5: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "Your number is $aNum!"
        ;;
        *) echo "You do not select a number between 1 to 5, game is over!"
            break
        ;;
    esac
done
```

⚠️ 在嵌套循环中，break 命令后面还可以跟一个整数，表示跳出第几层循环。例如：

```shell
break n #表示跳出第 n 层循环。
```

## case/select

case/select依靠在代码块的顶部或底部的条件判断来决定程序的分支。

### case

case它允许通过判断来选择代码块中多条路径中的一条。它的作用和多个if/then/else语句相同，是它们的简化结构，特别适用于创建目录。[demo30](./example/demo30)

> ```shell
> case "$variable" in   
>   ?"$condition1" )   
>   ?command...   
>   ?;;   
>   ?"$condition2" )   
>   ?command...   
>   ?;;   
> esac  
> ```

- 对变量使用`""`并不是强制的，因为不会发生单词分离。
- 每句测试行，都以右小括号`)`结尾。
- 每个条件块都以两个分号结尾`;;`。
- case块的结束以esac(case的反向拼写)结尾。

```shell
clear # 清屏.

echo "          我的简历"
echo "          ------- "
echo "下面通过shell脚本输出我的简历" 
echo
echo "[B]asicinfo, 基本信息"
echo "[E]ducation, 教育经历"
echo "[I]tskill, IT 技能"
echo
read person
case "$person" in
# 注意,变量是被引用的.
  "B" | "b" )
  # 接受大写或小写输入.
  echo
  echo "小弟调调"
  echo "手  机 : 136*****13"
  echo "E-mail :wowohoo@qq.com"
  echo "首  页 : http://JSLite.io"
  ;;
  # 注意,在每个选项后边都需要以;;结尾.

  "E" | "e" )
  # 接受大写或小写输入.
  echo "■ 2003年9月 到 2006年8月"
  echo "----------------------------"
  echo "› 学校 : 野鸟高中"
  echo "› 专业 : 艺术类"
  echo "› 学历 : 高中"
  ;;
  # 后边的[I]tskill的信息在这里就省略了.
          * )
   # 默认选项.
   # 空输入(敲RETURN).
   echo
   echo "没有数据！"
  ;;
esac
exit 0
```


### select

select结构是建立菜单的另一种工具，这种结构是从ksh中引入的。

> select variable [in list]  
> do   
>   ?command...   
>   ?break   
> done  

用select来创建菜单

```shell
PS3='选择你喜欢的蔬菜: ' # 设置提示符字串.

echo
select vegetable in "豆" "胡萝卜" "土豆" "洋葱" "芜菁甘蓝"
do
  echo
  echo "你最喜欢的蔬菜是 $vegetable 。"
  echo "讨厌!"
  echo
  break  # 如果这里没有'break'会发生什么?
done
exit 0
```

如果忽略了in list列表,那么select命令将使用传递到脚本的命令行参数($@),或者是函数参数(当select是在函数中时）与忽略in list时的for语句相比较：**for variable [in list]**

```shell
PS3='选择你喜欢的蔬菜:  '
echo
choice_of(){
  select vegetable
  # [in list] 被忽略, 所以'select'用传递给函数的参数.
  do
    echo
    echo "你最喜欢的蔬菜是  $vegetable。"
    echo "讨厌!"
    echo
    break
  done
}

choice_of "豆" "米饭" "胡萝卜" "土豆" "洋葱" "芜菁甘蓝"
#         $1   $2     $3      $4    $5     $6
#         传递给choice_of() 函数的参数

exit 0
```

## 函数

和"真正的"编程语言一样，Bash也有函数，虽然在某些实现方面稍有些限制。 一个函数是一个子程序，用于实现一串操作的代码块(code block)，它是完成特定任务的"黑盒子"。 当有重复代码，当一个任务只需要很少的修改就被重复几次执行时, 这时你应考虑使用函数。 [demo33](./example/demo33)

```shell
function function_name { 
  command... 
} 
# 或
function_name () { 
  command... 
} 
```

在一个函数内嵌套另一个函数也是可以的，但是不常用。

```shell
f1 (){
  f2 (){ # nested
    echo "Function \"f2\", inside \"f1\"."
  }
}  
f2  #  引起错误.
    #  就是你先"declare -f f2"了也没用.

f1  #  什么也不做,因为调用"f1"不会自动调用"f2".
f2  #  现在,可以正确的调用"f2"了,
    #+ 因为之前调用"f1"使"f2"在脚本中变得可见了.
```

### 局部变量

如果变量用local来声明，那么它只能在该变量声明的代码块(block of code)中可见，这个代码块就是局部"范围"。

```shell
# 在函数内部的全局和局部变量.
func ()
{
  local loc_var=23       # 声明为局部变量.
  echo                   # 使用内建的'local'关键字.
  echo "\"loc_var\" in function = $loc_var"
  global_var=999         # 没有声明为局部变量.
                         # 默认为全局变量. 
  echo "\"global_var\" in function = $global_var"
}  

func
# 现在，来看看是否局部变量"loc_var"能否在函数外面可见.
echo "\"loc_var\" outside function = $loc_var"
                                   # $loc_var outside function = 
                                   # 不, $loc_var不是全局可访问的.
echo "\"global_var\" outside function = $global_var"
                                      # $global_var outside function = 999
                                      # $global_var 是全局可访问的.
exit 0
#  与In contrast to C相比, 在函数内声明的Bash变量只有在
#+ 它被明确声明成局部的变量时才是局部的
```

⚠️ ：在函数调用之前，所有在函数内声明且没有明确声明为local的变量都可在函数体外可见

```shell
func (){
  global_var=37    #  在函数还没有被调用前
                   #+ 变量只在函数内可见. 
}                  #  函数结束
echo "global_var = $global_var"  # global_var =
                                 #  函数"func"还没有被调用,
                                 #+ 所以变量$global_var还不能被访问.
func
echo "global_var = $global_var"  # global_var = 37
                                 # 已经在函数调用时设置了值.
```

### 函数参数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 `$n` 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数

```shell
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

### 函数返回值

定义一个带有return语句的函数。函数返回值在调用该函数后通过 `$?` 来获得。

```shell
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"

# 这个函数会对输入的两个数字进行相加运算...
# 输入第一个数字: 
# 1
# 输入第二个数字: 
# 2
# 两个数字分别为 1 和 2 !
# 输入的两个数字之和为 3 !
```

⚠️ `$10` 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

**特殊字符用来处理参数：**

| 参数处理 | 说明 |
| ---- | ----  |
| $# | 传递到脚本的参数个数  |
| $* | 以一个单字符串显示所有向脚本传递的参数  |
| $$ | 脚本运行的当前进程ID号  |
| $! | 后台运行的最后一个进程的ID号  |
| $@ | 与$*相同，但是使用时加引号，并在引号中返回每个参数。  |
| $- | 显示Shell使用的当前选项，与set命令功能相同。  |
| $? | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。  |
