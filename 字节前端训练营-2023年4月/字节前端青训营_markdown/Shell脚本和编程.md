# Shell脚本和编程

### 学习shell的价值：

1. Linux服务器的基本操作和管理
2. 前端Node.js服务的进程管理、问题排查、资源监控等运维操作
3. 使用shell编写TCE、SCM、Docker脚本，完成服务编译和部署

### 课程准备

- 一台安装了 linux 系统的物理机或者云主机，可运行 shell 脚本
- 本地的 vscode 安装 Bash Debug 插件，并升级 bash 到4.x以上
- Npm 全局安装 zx 依赖

## Shell基本概念

### 概念

> - Shell（也称为命令行界面或终端）是一种通过文本输入和输出进行交互的计算机用户界面。它允许用户通过输入命令来执行操作、运行程序和管理系统
>
> 1. 命令（Command）：Shell通过输入命令来执行不同的操作或任务，例如创建文件、复制文件、移动文件、运行程序等。命令通常由命令名和参数组成，命令名指定要执行的操作，参数用于指定命令的具体操作方式或作用对象。
> 2. 提示符（Prompt）：Shell在等待用户输入命令时会显示一个提示符，通常是一个特殊的字符或字符串，表示Shell已经准备好接受命令输入。
> 3. 文件系统（File System）：Shell可以用于管理计算机上的文件和目录，包括创建、复制、移动、删除、重命名、查看等操作。Shell使用文件系统的路径来定位文件和目录，例如绝对路径（从根目录开始的完整路径）和相对路径（从当前目录开始的相对路径）。
> 4. 管道（Pipeline）：Shell允许将多个命令通过管道连接起来，其中一个命令的输出作为另一个命令的输入。这样可以实现多个命令的组合和协作，从而更加灵活地完成复杂的任务。
> 5. 环境变量（Environment Variable）：Shell使用环境变量来存储系统级别的配置信息、用户配置和运行时参数。环境变量可以在命令中使用，并且可以通过设置和修改来影响Shell的行为和操作。
> 6. 脚本（Script）：Shell允许用户编写一系列命令的脚本文件，以便将一组命令作为一个单独的程序运行。脚本可以包含条件语句、循环、函数等控制结构，从而实现更加复杂的自动化任务。
> 7. 快捷键（Shortcut）：Shell提供了许多快捷键和命令行编辑功能，用于提高命令行操作的效率和便捷性。例如，可以使用上下箭头键来浏览历史命令，使用Tab键进行命令和文件名的自动补全，使用Ctrl+C来中断正在执行的命令等。

![image-20230417132310425](.\byte-images\image-20230417132310425.png)

- Shell``和`Bash`能够理解为类和实例的关系

### Bash的历程

![image-20230417132835674](.\byte-images\image-20230417132835674.png)

> 以下内容当作课外内容补充(当故事看都行)：
>
> 1. 1989年：Bash由布莱恩·福克斯（Brian Fox）开发，并首次发布。Bash基于Bourne Shell（sh），是Bourne Shell的一种改进版本，它添加了许多新的特性，例如命令历史、命令行编辑、作业控制等。
> 2. 1993年：Bash 1.12发布，这是Bash的第一个公开发布版本。Bash开始在Unix和Linux系统中广泛传播，并成为默认的命令行Shell。
> 3. 1994年：Bash 2.0发布，引入了更多的新特性，包括命令别名、条件语句、循环、函数、算术运算等，使Bash变得更加强大和灵活。
> 4. 2000年：Bash 2.05b发布，这是一个重要的版本，引入了很多新的特性，包括命令补全、目录堆栈、命令行参数处理等，进一步提升了Bash的用户友好性和交互性。
> 5. 2007年：Bash 3.0发布，引入了更多的新特性，包括新的扩展属性、数组、关联数组等，使Bash更加适用于复杂的脚本编程和系统管理任务。
> 6. 2011年：Bash 4.2发布，带来了许多新的特性和改进，包括新的内置命令、更强大的模式匹配、更灵活的历史命令管理、更高效的作业控制等。
> 7. 2016年：Bash 4.4发布，带来了许多新的特性和改进，包括新的参数扩展、新的运算符、更好的错误处理、更强大的命令历史管理等。
> 8. 2019年：Bash 5.0发布，引入了一些新特性，例如新的管道重定向符号、新的内置命令、新的变量扩展和运算符等，继续改进和增强了Bash的功能和性能。

### 构成

> Unix shell既是一个命令解释器也是一种编程语言。作为命令解释器，shell为丰富的GNU工具集提供了用户接口。

![image-20230417133238749](.\byte-images\image-20230417133238749.png)

## 命令和语法

|     类型     |        作用域        |          声明方式           |             规范             |
| :----------: | :------------------: | :-------------------------: | :--------------------------: |
|  自定义变量  |      当前shell       |         =(隐式声明)         | 字符串、整型、浮点型、日期型 |
|   环境变量   | 当前shell及其子shell | export declare -x(显示声明) |                              |
| 系统环境变量 |      所有shell       |          启动加载           |                              |

![image-20230417133926082](.\byte-images\image-20230417133926082.png)

### 自定义变量

```shell
#!/bin/bash
# 这是脚本文件的shebang，它告诉系统脚本文件使用哪种解释器来执行。在这个例子中，使用的是bash解释器

#变量名=变量值（等号左右不能有空格），定义了两个变量page_size和page_num，并分别给它们赋值为1和2
page size=1
page_num=2

#将命令复制给变量，这样可以通过变量名来执行命令。
_ls=ls

#将命令结果赋值给变量，可以通过变量名来访问命令结果
file_list=$(ls -a)

#默认字符串，不会进行 + 运算。所以total的值为"page_size*page_num"字符串
total=page_size*page_num

#声明变量为整型(-i)，后续的数值计算中，total会被当作整型来处理
let total=page_size*page_num
declare -i total=page_size*page_num

#导出环境变量，使得其在子进程中也可用
export total
declare -x total
```

### declare选项的含义

| 选项 |            含义            |
| :--: | :------------------------: |
|  -   |     给变量设定类型属性     |
|  +   |     取消变量的类型属性     |
|  -a  |    将变量声明为数组类型    |
|  -i  |     将变量声明为整数型     |
|  -x  |    将变量声明为环境变量    |
|  -r  |    将变量声明为只读变量    |
|  -p  | 显示指定变量的被声明的类型 |

### 系统环境变量

| 变量名 |          含义          |                         常见操作                          |
| :----: | :--------------------: | :-------------------------------------------------------: |
|   $0   | 当前shell名称/脚本名称 |                $1、$2等可以获取到传入参数                 |
|   $#   |   传入脚本的参数数量   |                      if[ $# -gt 1 ]                       |
|   $*   |   传入脚本的所有参数   |                                                           |
|   $?   |  上条命令执行的状态码  |                     if [ $? -eq 0 ];                      |
|  $PS1  |       命令提示符       | export PS1="\u@\h \w"<br>(对应当前的主机名、用户名、目录) |
| $HOME  |  用户主文件夹(主目录)  |                           cd ~                            |
| $PATH  |   全局命令的搜索路径   |                   PATH=$PATH:[新增路径]                   |

1. `$#`表示当前脚本或函数的参数个数。在shell脚本中，可以通过`$#`来获取当前脚本或函数的参数个数，并根据参数个数执行相应的操作。
2. `$*`表示当前脚本或函数的所有参数列表。在shell脚本中，可以通过`$*`来获取当前脚本或函数的所有参数，并根据参数列表执行相应的操作。
3. `$?`表示上一个命令的退出状态码。在shell脚本中，可以通过`$?`来获取上一个命令的退出状态码，并根据状态码执行相应的操作。
4. `$PS1`表示shell提示符的格式。在shell中，可以通过修改`$PS1`来自定义shell提示符的格式，以适应个人习惯或需求。
5. `$HOME`表示当前用户的主目录。在shell脚本中，可以通过`$HOME`来获取当前用户的主目录，并根据主目录执行相应的操作。
6. `$PATH`表示系统的可执行文件路径列表。在shell中，可以通过修改`$PATH`来添加或删除系统的可执行文件路径，以便更方便地执行系统命令或自定义命令。

### 配置文件加载

![image-20230417141521350](.\byte-images\image-20230417141521350.png)

### 运算符和引用

![image-20230417141652895](.\byte-images\image-20230417141652895.png)

### 管道

> 管道与管道符 | ，作用是将前一个命令的结果传递给后面的命令
> 语法：cmd1 | cmd2
>
> **要求**：管道右侧的命令必须能接受标准输入才行，比如grep命令，ls、mv等不能直接使用，可以使用xargs预处理
>
> **注意**：管道命令仅仅处理stdout,对于stderr会予以忽略，可以使用set -o pipefail设置shell遇到管道错误退出

```SHELL
#!/bin/bash

#该命令将文件platform.access.log的内容通过管道符|传递给grep命令，用于查找文件中包含字符串ERROR的行。这个命令的作用是在platform.access.log文件中查找错误信息
cat platform.access.log | grep ERROR

#该命令使用netstat命令来显示所有的网络连接状态，并通过管道符|将结果传递给grep命令，用于查找所有处于ESTABLISHED状态的连接。最后，通过管道符|将结果传递给wc -l命令，用于统计行数。这个命令的作用是统计当前系统中所有处于ESTABLISHED状态的网络连接数量
netstat -an grep ESTABLISHED | wc -l

#该命令使用find命令在当前目录中查找所有扩展名为.sh的文件，并通过管道符|将结果传递给xargs命令，将查找到的文件名作为参数传递给ls -l命令，用于显示这些文件的详细信息。这个命令的作用是列出当前目录中所有扩展名为.sh的文件的详细信息
find . -maxdepth l -name "*.sh" | xargs ls -l
```

### 重定向

![image-20230417142859062](.\byte-images\image-20230417142859062.png)

- 修改了默认的关系

![image-20230417142935531](.\byte-images\image-20230417142935531.png)

1. `>`：将命令的输出重定向到一个文件中，如果文件不存在则创建，如果文件已经存在则覆盖。
2. `>>`：将命令的输出重定向到一个文件中，如果文件不存在则创建，如果文件已经存在则在文件末尾追加。
3. `2>`：将命令的错误输出重定向到一个文件中，如果文件不存在则创建，如果文件已经存在则覆盖。
4. `&>`：将命令的输出和错误输出重定向到一个文件中，如果文件不存在则创建，如果文件已经存在则覆盖。
5. `<`：将一个文件的内容作为命令的输入。
6. `<<`：将一段字符串作为命令的输入。

以下是一些例子：

1. `ls > file.txt`：将`ls`命令的输出重定向到`file.txt`文件中，如果文件不存在则创建，如果文件已经存在则覆盖。
2. `ls >> file.txt`：将`ls`命令的输出重定向到`file.txt`文件中，如果文件不存在则创建，如果文件已经存在则在文件末尾追加。
3. `ls 2> error.txt`：将`ls`命令的错误输出重定向到`error.txt`文件中，如果文件不存在则创建，如果文件已经存在则覆盖。
4. `ls &> output.txt`：将`ls`命令的输出和错误输出重定向到`output.txt`文件中，如果文件不存在则创建，如果文件已经存在则覆盖。
5. `sort < file.txt`：将`file.txt`文件的内容作为`sort`命令的输入。
6. `cat << EOF`：将下面的一段字符串作为`cat`命令的输入直到遇到`EOF`为止。

### 判断命令

**shell中提供了test、[、[[三种判断符号，可用于：**

- 整数测试
- 字符串测试
- 文件测试

**语法：**

- test condition
- [ condition ]
- [[ condition ]]

**注意**：

- 中括号前后要有空格符
-  [ 和test是命令，只能使用自己支持的标志位，<、>、=只能用来比较字符串
- 中括号内的变量，最好都是用引号括起来
- [[更丰富，在整型比较中支持<、>、=,在字符串比较中支持=~正则

> 在shell中，`test`命令用于进行各种测试操作。在这段代码中，`test`命令被用于进行整数、字符串和文件的测试。
>
> 整数测试使用`-eq`、`-lt`和`-gt`选项来比较两个整数的大小关系，返回结果为真或假。
>
> 字符串测试使用`-z`、`-n`和`=``选项来测试字符串的长度、非空和相等关系，返回结果为真或假。
>
> 文件测试使用`-e`和`-f`选项来测试文件是否存在和是否为普通文件，返回结果为真或假。在文件测试中，还可以使用`-d`选项来测试目录是否存在，`-r`选项来测试文件是否可读，`-w`选项来测试文件是否可写，`-x`选项来测试文件是否可执行等等。

```shell
#!/bin/bash

# 整数测试
test $nl -eq $n2   # 测试 $nl 是否等于 $n2
test $nl -lt $n2   # 测试 $nl 是否小于 $n2
test $nl -gt $n2   # 测试 $nl 是否大于 $n2

# 字符串测试
test -z $str_a     # 测试 $str_a 是否为空
test -n $str_a     # 测试 $str_a 是否非空
test $str_a = $str_b   # 测试 $str_a 是否等于 $str_b

# 文件测试
test -e /dmt && echo "exist"   # 测试 /dmt 是否存在，并在存在时输出 "exist"
test -f /usr/bin/npm && echo "file exist"   # 测试 /usr/bin/npm 是否存在，并在存在时输出 "file exist"
```

- 还有就是在进行比较的时候，需要注意的点：
  - 左边是写法，右边是写法实际上变成的内容样子

![image-20230417150226204](.\byte-images\image-20230417150226204.png)

#### shell中的分支语句

> 在shell中，分支语句主要有if语句和case语句。

1. if语句

- if语句用于判断条件是否成立，如果成立则执行相应的命令或者代码块。if语句的基本语法如下：

```shell
if [ condition ]
then
    command1
    command2
    ......
    commandN
fi

# condition是需要判断的条件，可以是变量、字符串、数值等，command1到commandN是需要执行的命令或者代码块
```

- if语句可以带有else和elif语句，用于在条件不成立时执行相应的命令或者代码块。if语句的完整语法如下：

```shell
if [ condition ]
then
    command1
    command2
    ......
    commandN
elif [ condition2 ]
then
    command1
    command2
    ......
    commandN
else
    command1
    command2
    ......
    commandN
fi

#condition是需要判断的条件，condition2是第二个需要判断的条件，command1到commandN是需要执行的命令或者代码块
```

案例演示：

> 需求：假设我们需要判断一个数是否为偶数，如果是偶数则输出“偶数”，否则输出“奇数”。
>
> 解析：在这个例子中，我们使用`read`命令获取用户输入的整数，然后使用`$((num % 2))`计算出余数，再判断余数是否等于0来判断这个数是否为偶数。如果是偶数则输出“偶数”，否则输出“奇数”

```shell
#!/bin/bash

echo "请输入一个整数："
read num

if [ $((num % 2)) -eq 0 ]
then
    echo "$num 是偶数"
else
    echo "$num 是奇数"
fi
```

2. case语句

   case语句用于根据不同的条件执行相应的命令或者代码块

```shell
case variable in
pattern1)
    command1
    ;;
pattern2)
    command2
    ;;
......
*)
    commandN
    ;;
esac

# variable是需要判断的变量，pattern1到patternN是需要判断的模式，command1到commandN是需要执行的命令或者代码块。如果没有任何一个模式匹配，那么就会执行*后面的命令或者代码块。esac是用来结尾的
```

案例演示：

> 需求：需要根据用户的输入来执行不同的命令
>
> 解析：我们使用`read`命令获取用户的选择，然后使用case语句根据不同的选择执行相应的命令。如果用户选择了1，则执行`uname -a`命令，如果选择了2，则执行`df -h`命令，如果选择了3，则执行`free -m`命令，如果选择了4，则退出脚本。如果用户输入了无效的选择，则输出“无效的选择

```shell
#!/bin/bash

echo "请选择一个操作："
echo "1. 显示系统信息"
echo "2. 显示磁盘使用情况"
echo "3. 显示内存使用情况"
echo "4. 退出"

read choice

case $choice in
1)
    uname -a
    ;;
2)
    df -h
    ;;
3)
    free -m
    ;;
4)
    exit 0
    ;;
*)
    echo "无效的选择"
    ;;
esac
```

> 在shell中，很多语句都有相应的结束标志，比如`if`语句有`fi`，`for`语句有`done`，`while`语句有`done`等等。这些结束标志的作用是用于标识语句的结束位置，避免语法错误

### 三种循环

#### while循环

> `while`循环用于在条件成立的情况下重复执行一段代码块

```shell
while [ condition ]
do
    command1
    command2
    ......
    commandN
done
#condition是需要判断的条件，command1到commandN是需要重复执行的命令或者代码块。每次执行完command1到commandN后，都会再次判断condition是否成立，如果成立则继续执行，否则退出循环
```

案例演示：

> 需求：从0加到10

```shell
#!/bin/bash

sum=0
i=1

while [ $i -le 10 ]
do
    sum=$((sum + i))
    i=$((i + 1))
done

echo "1+2+3+...+10=$sum"
```

#### unitil循环

> `until`循环与`while`循环类似，不同之处在于`until`循环在条件不成立的情况下重复执行一段代码块

```shell
until [ condition ]
do
    command1
    command2
    ......
    commandN
done

#condition是需要判断的条件，command1到commandN是需要重复执行的命令或者代码块。每次执行完command1到commandN后，都会再次判断condition是否成立，如果不成立则继续执行，否则退出循环
```

案例演示：

> 一样是从0加到10

```shell
#!/bin/bash

sum=0
i=1

until [ $i -gt 10 ]
do
    sum=$((sum + i))
    i=$((i + 1))
done

echo "1+2+3+...+10=$sum"
```

#### for循环

> `for`循环用于遍历一组数据，并对每个数据执行相同的命令或者代码块

```shell
for variable in range
do
    command1
    command2
    ......
    commandN
done

#variable是用于存储数据的变量名，range是需要遍历的数据范围，可以是数字、字符串、数组等。command1到commandN是需要重复执行的命令或者代码块，每次执行前，variable会被赋值为当前遍历的数据
```

案例演示：

> 遍历数组
>
> 使用`for`循环遍历数组`array`中的每个元素，并使用`echo`命令输出每个元素

```shell
#!/bin/bash

array=("apple" "banana" "orange" "watermelon")

for fruit in ${array[@]}
do
    echo $fruit
done
```

- 除了遍历数组，`for`循环还可以遍历数字范围、文件列表等

```shell
#!/bin/bash

sum=0

for i in {1..100}#或者使用视频案例中的for((i=0;i<10;i++))
do
    sum=$((sum + i))
done

echo "1+2+3+...+100=$sum"
```

- 除了使用数字范围，`for`循环还可以使用通配符遍历文件列表

```shell
#!/bin/bash

for file in *.txt
do
    echo $file
done

#使用for循环遍历当前目录下所有.txt文件，并使用echo命令输出每个文件的文件名
```

###  函数

> 在shell中，函数是一段可以重复使用的代码块，可以在脚本中定义并调用

```shell
function_name () {
    command1
    command2
    ......
    commandN
}
#function_name是函数名，command1到commandN是需要执行的命令或者代码块。定义完函数后，可以在脚本中通过函数名来调用函数
```

案例演示：

> 定义了一个名为`hello`的函数，函数的代码块中只有一条命令，用于输出“Hello, World!”。在定义完函数后，我们在脚本中通过函数名`hello`来调用函数，从而输出“Hello, World!”

```shell
#!/bin/bash

hello() {#除了这个语法之外，还有视频中的语法2：function hello(){}
    echo "Hello, World!"
}

hello
```

- **在函数中，可以使用`return`语句来返回一个值**

> 返回两数之和

```shell
#!/bin/bash

add() {
    sum=$(( $1 + $2 ))
    return $sum
}

add 3 5
result=$?
echo "3 + 5 = $result"

#定义了一个名为`add`的函数，函数的代码块中计算了两个数之和，并使用`return`语句返回了这个和。在调用函数时，我们传递了两个参数3和5，函数返回的值被存储到了变量`result`中，并使用`echo`命令输出了这个和
```

#### 注意点

- shell自上而下执行，函数必须在使用前定义
- 函数获取变量和shell script类似，$0代表函数名，后续参数通过$1、$2..获取
- 函数内return仅仅表示函数执行状态，不代表函数执行结果
- 返回结果一般使用echo、printf，在外面使用$()、``获取结果
- 如果没有return,函数状态是上一条命令的执行状态，存储在$？中

```shell
function test() {  # 定义一个名为test的函数
  local word="hello world"  # 定义一个名为word的本地变量，值为"hello world"
  echo $word  # 输出word的值到标准输出
  return 10  # 返回值为10
  unset word  # 没有执行到这里，因为前面已经执行了return语句

}  # 函数定义结束

content=`test`  # 执行test函数，并将输出赋值给变量content
echo "状态码：$?"  # 打印上一条命令的状态码，应该是10
echo "执行结果：$content"  # 打印变量content的值，即"hello world"
```

### 模块化

**模块化的原理是在当前shell内执行函数文件，方式：**

- source[函数库的路径]

![image-20230417154352825](.\byte-images\image-20230417154352825.png)

#### 模块化命令

![image-20230417154443121](.\byte-images\image-20230417154443121.png)

- 视频中查看日志的后10条内容：`tail -n 10 cloundfun.log`
- 下图中这是一个Linux命令，用于查看文件的最后10行内容，其中：
  - `tail`是命令本身，表示显示文件尾部内容。
  - `-n`是一个选项，用于指定显示的行数。这里指定为10，即显示文件的最后10行。
  - `cloundfun.log`是要显示内容的文件名，可以替换为其他文件的名称。

![image-20230417154649103](.\byte-images\image-20230417154649103.png)

- 与之前的命令类似，用于查看文件的最后10行内容，但是它还有一个 `-f` 选项，表示持续监视该文件的变化并实时输出新添加的内容，直到手动中断为止
  - `-f`选项：用于持续监视该文件的变化并实时输出新添加的内容。在排查问题的时候经常用到

![image-20230417154900063](.\byte-images\image-20230417154900063.png)

## 执行过程和原理

1. shell脚本一般以.sh结尾，也可以没有，这是一个约定；第一行需要指定用什么命令解释器来执行

```shell
#! /bin/bash
```

```
#! /user/bin/env bash
```

2. 启动shell的三种方式

```shell
#文件名运行(子进程执行)
./filename.sh


#解释器运行(子进程执行)
bash ./filename.sh


# source运行(当前进程中执行)
source ./filename.sh
```

### 执行过程

![image-20230417155343334](.\byte-images\image-20230417155343334.png)

1. **字符解析**
   - 识别换行符、分号(；)做行的分割
   - 识别命令连接符(||&&管道)做命令的分割
   - 识别空格、tab符，做命令和参数的分割
2. **shell展开，例如{1...3}解析为1 2 3**
3. **重定向，将stdin、stdout、stderr的文件描述符进行指向变更**
4. 执行命令
   - builtin直接执行
   - 非builtin使用$PATH查找，然后启动子进程执行
5. 收集状态并返回给脚本

### shell展开

> Shell 展开是指在执行命令之前，Shell 预处理命令行中的各种特殊字符，将它们替换为实际的值或执行相应的操作

1. 大括号展开（Brace Expansion）：使用大括号 `{}` 将一组字符串包含在命令行中，Shell 将展开大括号内的所有字符串，并将其作为命令行的一部分。例如：`echo file{1..3}.txt` 将展开为 `file1.txt file2.txt file3.txt`。
2. 波浪号展开（Tilde Expansion）：使用波浪号 `~` 将用户名或路径名包含在命令行中，Shell 将展开波浪号并将其替换为相应的用户名或路径名。例如：`cd ~/Documents` 将进入当前用户的 Documents 目录。
3. 参数展开（Parameter Expansion）：使用 `$` 将变量名包含在命令行中，并使用一些特殊符号来修改变量的值或进行字符串操作。例如：`${var:-default}` 表示如果变量 `$var` 未定义，则使用默认值 `default`。
4. 命令替换（Command Substitution）：使用反引号或 `$()` 将一个命令包括起来，Shell 将执行该命令并将其输出作为命令行的一部分。例如：`echo "The date is $(date)"` 将输出当前日期和时间。
5. 数学计算（Arithmetic Expansion）：使用 `$(( ))` 将一个算术表达式包含在命令行中，Shell 将计算该表达式并将其替换为计算结果。例如：`echo $((1+2))` 将输出 `3`。
6. 文件名展开（Filename Expansion）：使用通配符 `*`、`?`、`[]` 等来匹配文件名或路径名。Shell 将展开通配符并将其替换为匹配的文件名或路径名。例如：`ls *.txt` 将列出当前目录下所有扩展名为 `.txt` 的文件。

#### 大括号展开

- 一般由三部分构成，前缀、一对大括号、后缀，大括号内可以是逗号分割的字符串序列，也可以是序列表达式{x..y[..incr]}

```shell
#字符串序列
a(b,c,d)e => abe ace ade

#表达式序列，（数字可以使用incr调整增量，字母不行）
{1..5} => 1 2 3 4 5

{1..5..2} => 1 3 5

{a..e} => a b c d e
```

#### 波浪号展开~

```shell
#当前用户主目录
~ => $HOME

~/fo => $HOME/foo

#指定用户的主目录
-fred/foo=>用户fred的SHOEM/foo

#当前工作目录
~+/foo => $PWD/foo

#上一个工作目录
~-/foo => ${$OLDPWD-'~-')/foo
```

#### 参数展开

1. 间接参数扩展${!parameter},其中引用的参数并不是parameter而是parameter的实际的值

```shell
parameter="var"  # 定义一个名为 parameter 的变量，其值为字符串 "var"
var="hello"  # 定义一个名为 var 的变量，其值为字符串 "hello"
echo ${!parameter}  # 使用参数展开的形式，将变量 $parameter 的值 "var" 替换为 $var，从而得到变量 $var 的值 "hello"，并将其输出到标准输出

#输出 hello
```

2. 参数长度${#parameter}

3. 空参数处理

   S{parameter:-word}#为空替换
   ${parameter:=word#为空替换，并将值赋给$parameter变量
   $parameter:?word)#为空报错
   ${parameter:+word}#不为空替换

```SHELL
a=1  # 定义一个名为 a 的变量，其值为整数 1

echo ${a:-word} #1  # 输出变量 $a 的值，如果 $a 未定义或为空，则输出 "word"。由于 $a 已经定义并赋值为 1，因此输出为 1。

echo $(b:-word) #word  # 执行命令替换，执行命令 $b 并将其输出作为结果，如果 $b 未定义或为空，则输出 "word"。由于 $b 未定义，因此输出为 "word"。

echo $(par:=word) #word  # 执行命令替换，将变量 $par 的值设置为 "word"，并将其输出作为结果。由于 $par 未定义，因此输出为 "word"。

echo $(par:-hello) #word  # 执行命令替换，输出变量 $par 的值，如果 $par 未定义或为空，则输出 "hello"。由于 $par 未定义，因此输出为 "word"。

echo $(par:+foo) #foo  # 执行命令替换，如果 $par 已经定义且非空，则输出 "foo"，否则不输出任何内容。由于 $par 未定义，因此不输出任何内容。

#输出分别为:1 word word word foo
```

4. 参数切片

   ${parameter:offset)
   ${parameter:offset:length}

5. 参数部分删除

   ${parameter%word} #最小限度从后面截取word
   ${parameter%%word}#最大限度从后面截取word
   S{parameter#word}#最小限度从前面截取word
   ${parameter##word#最大限度从前面截取word

```SHELL
#!/bin/sh

str=abcdefg  # 定义一个名为 str 的变量，其值为字符串 "abcdefg"

spl=$(str##*d)  # 执行命令替换，删除变量 $str 开头到最后一个 "d" 之前的所有字符，并将剩余的字符串赋值给变量 $spl。由于变量 $str 的值为 "abcdefg"，因此 $spl 的值为 "efg"。
# * 是通配符的一种，表示匹配任意数量的任意字符（包括 0 个字符）。具体来说，*d 表示匹配以字母 “d” 结尾的字符串，并将匹配到的字符串删除。而 ## 表示从变量的开头开始匹配，删除最长的匹配项。

sp2=${str%%d*}  # 执行参数展开，删除变量 $str 最后一个 "d" 以及之后的所有字符，并将剩余的字符串赋值给变量 $sp2。由于变量 $str 的值为 "abcdefg"，因此 $sp2 的值为 "abc"。

echo $spl #输出efg  # 输出变量 $spl 的值，即字符串 "efg"。

echo $sp2 #输出abc  # 输出变量 $sp2 的值，即字符串 "abc"。

# echo的两个输出分别如下：efg abc
```

#### 命令替换

>  在子进程中执行命令，并用得到的结果替换包裹的内容，形式上有两种：$(...)或`...`

```shell
#! /bin/bash

echo ${whoimi}  # 输出变量 $whoimi 的值。由于 $whoimi 未定义，因此输出为空。

foo(){  # 定义一个名为 foo 的函数
echo "asdasd"  # 在函数中输出字符串 "asdasd"
}

a=`foo`  # 执行命令替换，执行函数 foo 并将其输出作为结果，将结果赋值给变量 $a。由于函数 foo 的输出为 "asdasd"，因此变量 $a 的值为 "asdasd"。
```

#### 数学计算

> 使用$( ( ) ) 包裹数学运算表达式，得到结果并替换

```shell
#! /bin/bash

echo $((1+2)) # 3
```

#### 文件名展开

当有单词没有被引号包裹，且其中出现了‘*’,‘?’,and ‘[’ 字符，则shell会去按照正则匹配的方式查找文件名进行替换，如果没找到则保持不变

```SHELL
#! /bin/bash

$ echo D*

# 输出当前目录下所有以 D字母开头的目录、文件
```

## 调试和前端集成

### 调试的方法

1. 普通log,使用echo、printf
2. 使用set命令
3. vscode debug插件

|   set配置   |                             作用                             |    补充    |
| :---------: | :----------------------------------------------------------: | :--------: |
|     -u      |            遇到不存在的变量就会报错，并停止执行。            | -o nounset |
|     -x      |            运行结果之前，先输出执行的那一行命令。            | -o xtrace  |
|     -e      |                   只要发生错误，就终止执行                   | -o errexit |
| -o pipefail | 管道符链接的，只要一个子命令失败整个管道命令就失败，脚本就会终止执行。 |            |

```shell
#! /bin/bash

#一般在最前面就进行配置
set -uxe -o pipefail

echo "hello world"
```

### Vscode插件配置

1. shellman：代码提示和自动补全
2. shellcheck：代码语法校验
3. shell-format：代码格式化
4. Bash Debug:支持单步调试
   - 安装vscode插件
   - 编写launch.json文件
   - 升级bash到4.x以上版本

![image-20230417163934847](.\byte-images\image-20230417163934847.png)

### 前端集成

1. **node中通过exec、spawn调用shell命令**

   ![image-20230417164211037](.\byte-images\image-20230417164211037-1681720931708-1.png)

2. **shell脚本中调用node命令**

   ![image-20230417164305772](.\byte-images\image-20230417164305772.png)

3. **借助zx等库进行javascript、shell script的融合**

   - 借助shell完成系统操作，文件io、内存、磁盘系统状态查看
   - 借助nodejs完成应用层能力，网络io、计算等

![image-20230417164432892](.\byte-images\image-20230417164432892.png)

## 最后的大纲

![image-20230417164457017](.\byte-images\image-20230417164457017.png)
