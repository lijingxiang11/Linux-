## Makefile
### 定义
Makefile 文件定义了一系列的规则来指定哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为 Makefile 文件就像一个 Shell 脚本一样，也可以执行操作系统的命令
### 命名规则
文件命名: makefile或者Makefile
makefile规则：
```
目标 ... : 依赖 ...
    命令(Shell 命令)
    ...
```
目标：最终要生成的文件（伪目标除外）
依赖：生成目标所需要的文件或是目标
命令：通过执行命令对依赖操作生成目标（命令前必须 Tab 缩进）
Makefile 中的其它规则一般都是为第一条规则服务的。

```cpp
//效率更高，针对某一个依赖文件发生改变，只需要重新编译对应的依赖文件
app:sub.o add.o mult.o div.o main.o
        gcc sub.o add.o mult.o div.o main.o -o app
sub.o:sub.c
        gcc -c sub.c -o sub.o
add.o:add.c
        gcc -c add.c -o add.o
mult.o:mult.c
        gcc -c mult.c -o mult.o
div.o:div.c
        gcc -c div.c -o div.o 
main.o:main.c
        gcc -c main.c -o main.o
```

#### 变量
##### 自定义变量
变量名=变量值 val=hello
##### 预定义变量
(部分)
AR : 归档维护程序的名称，默认值为 ar
CC : C 编译器的名称，默认值为 cc(即gcc)
CXX : C++ 编译器的名称，默认值为 g++

自动变量：只能在规则的命令中使用
\$@ : 目标的完整名称
\$< : 第一个依赖文件的名称
\$^ : 所有的依赖文件
```c
app:main.c a.c b.c
        gcc-c main.c a.c b.c
#使用自动变量
app:main.c a.c b.c
        $(CC) -c $^ -o $@
```
##### 获取变量的值
\$(变量名)
##### 模式匹配
%.o:%.c
%: 通配符，匹配一个字符串
两个%匹配的是同一个字符串
`
```cpp
//优化了命名规则那部分的代码
//定义变量
src=sub.o add.o mult.o div.o main.o
target=app

$(target):$(src)
        $(CC) $(src) -o $(target)
%.o:%.c
        $(CC) -c $< -o $@
```
### 函数
\$(wildcard PATTERN...)
功能：获取指定目录下指定类型的文件列表
参数：PATTERN 指的是某个或多个目录下的对应的某种类型的文件，如果有多个目录，一般使用空格间隔
返回：得到的若干个文件的文件列表，文件名之间使用空格间隔
\$(wildcard .c ./sub/.c)//返回当前目录以及子目录下的.c文件
返回值格式: a.c b.c c.c d.c e.c f.c
```cpp
src=sub.o add.o mult.o div.o main.o
//简写成如下格式
src=$(wildcard ./*.o)//表示获取当前目录下所有.o文件
```
\$(patsubst \<pattern>,\<replacement>,\<text>)
功能：查找\<text>中的单词(单词以“空格”、“Tab”或“回车”“换行”分隔)是否符合模式，如果匹配的话，则以替换。
\<pattern>可以包括通配符 % ，表示任意长度的字串。\<replacement>如果中也包含 % ，那么，\<replacement>中的这个 % 将是\<pattern>中的那个%所代表的字串。(可以用 \ 来转义，以 \% 来表示真实含义的 % 字符)
返回：函数返回被替换过后的字符串
示例：
\$(patsubst %.c, %.o, x.c bar.c)
返回值格式: x.o bar.o

```cpp
#
src=$(wildcard .c)
obj=$(patsubst %.c,%.o $(src))
target=app

$(target):$(src)
        $(CC) $((obj) -o $(target)
%.o:%.c
        $(CC) -c $< -o $@
```