---
title: adb 使用
date: 2024-9-11 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

1、添加调试信息
1.1、编译时添加调试信息
1.1、-g级别
2、常用指令
3、gdb常用参数
4、示例
4.1、调试已运行进程
4.2、调试线程
4.3、查看相关信息
4.4、打印数组
5、使用gdb调试so文件
示例：
参考：https://blog.csdn.net/Luckiers/article/details/124568399

https://blog.csdn.net/goo__gle/article/details/90751853

1、添加调试信息
1.1、编译时添加调试信息
gcc 使用 -g 或者 -ggdb 生成调试信息，区别如下：

选项	说明
-g	利用操作系统的“原生格式”生成调试信息。gdb和一些其他调试器都可以使用
-ggdb	使 gcc 为 gdb 生成专用的更加丰富的调试信息，其他调试其无法使用，如ddx
1.1、-g级别
选项	说明
-g1	不包含局部变量和与行号有关的调试信息，因此只能用于回溯和堆栈转储之用。
回溯跟踪指的是监视程序在运行过程中的函数调用历史，堆栈转储则是一种以原始的16进制格式保存程序执行环境的方法，两者都是常用的调试手段
-g2	默认级别。调试信息包括：扩展的符号表、行号、局部或者外部变量信息
-g3	包含级别2中的所有调试信息，以及源码中定义的宏
2、常用指令
开始调试：

gdb ./test_main #使用gdb开始调试 test_main 二进制
开始调试后，程序并未执行，使用以下指令调试或者运行程序

运行和退出：

run     #启动程序并开始调试
run args    #启动程序并传入参数args
continue/c    #继续运行程序，知道遇到下一个断点
setp/s    #逐行执行程序，遇到函数，则进入函数内部，和nexti的区别是，停在下一条源码上
next/n    #逐行执行程序，遇到函数，则执行并跳过函数
nexti    #逐行执行程序，遇到函数，进入函数内部，和step的区别是，停在下一条汇编指令上

file ./test_main  #装入想要调试的可执行文件
kill ./test_main  #终止正在调试的程序

finish    #继续执行，直到当前函数返回
return    #强制从当前函数返回

quit/q    #退出调试
断点操作：

#设置断点
break main    #在主函数设置断点
break test.cpp:12  #在test.cpp文件的12行设置断点
break test.cpp::TestLib::testFunc #在test.cpp的TestLib::testFunc函数中设置一个断点
break TestLib::testFunc #在函数TestLib::testFunc的第一行设置断点

info breakpoints #查看当前设置的所有断点，断点有编号
delete 1   #删除编号为1的断点，先用info breakpoints查看断点编号，再删除
#清除断点，和设置断点方法相同，如何设置，即可如何清除
clear     #清除所有断点
clear 12    #清除12行的断点
clear TestLib::testFunc #清除在函数TestLib::testFunc上设置的断点
clear test.cpp:12  #清除test.cpp的12行的断点
调试打印：

print/p num   #打印变量num的值，也可以打印表达式的值
backtrace/bt  #显示函数调用栈信息
list    #显示源码片段
list 5    #显示第5行上下代码
list TestLib::testFunc #显示函数TestLib::testFunc上下代码
list test.cpp:12 #显示test.cpp的12行上下代码
list +5    #显示当前所在的代码的后5行代码
list -5    #显示当前所在的代码的前5行代码
set num=12   #设置变量num的值是12
whatis num   #显示变量的类型
其他：

make  #不退出gdb就重新生成可执行文件
shell  #不推出gdb就可以执行shell指令和老婆
3、gdb常用参数
参数	说明
-e	指定可执行文件名
-c	指定coredump文件
-d	指定目录加入到源文件搜索路径
-cd	指定目录作为路径运行gdb
-s	指定文件读取符号表
-p	指定attach进程
4、示例
4.1、调试已运行进程
(gdb) gdb -p  进程名 #对指定进程进行调试
(gdb) gdb attach　　进程名
4.2、调试线程
(gdb) info thread  #调试已运行的进程下再列出线程
(gdb) thread　　线程号   #切换至线程
4.3、查看相关信息
(gdb) info thread  #列出线程
(gdb) info register #列出寄存器
(gdb) info frame    #列出栈帧
(gdb) info files   #列出当前文件
(gdb) info share    #列出当前共享库
4.4、打印数组
查看一段连续的内存空间的值。

比如数组的一段，或是动态分配的数据的大小。

你可以使用GDB的“@”操作符，“@”的左边是第一个内存的地址的值，“@”的右边则你你想查看内存的长度

int *array = (int *) malloc (len * sizeof (int)); 
gdb调试

(gdb) p *array@len
@的左边是数组的首地址的值，也就是变量array所指向的内容，右边则是数据的长度，其
保存在变量len中，其输出结果，大约是下面这个样子的：

(gdb) p *array@len 
$1 = {2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40} 
如果是静态数组的话，可以直接用print数组名，就可以显示数组中所有数据的内容了

5、使用gdb调试so文件
步骤如下：

使用gdb调试二进制文件；
在二进制文件调用so库的地方打断点（因为二进制不带有调试信息，所以无法调试）；
开始运行（这里已经已经加载so了，下面不需要再手动加载so文件，如果手动再次加载，会出现段错误）；
在so的代码中打断点
继续运行到so的断点，进入so代码
这里有两处容易出现错误：

调试so，需要从二进制入口，先在二进制中打断点，即使无用；
运行时，不需要再加载so文件（这里不清楚的可能会使用file指令加载so，导致无法调试）；
示例：
二进制文件 test_main，编译时不带调试信息

so文件 libtest_lib.so，编译时带有调试信息

代码如下：

//二进制，编译出的二进制文件test_main
int main()
{
    setbuf(stdout, nullptr);

    TestLib test_lib;
    int res = test_lib.testFunc();
    cout << res << endl;

    return 0;
}

so代码如下：

//lib.h
class TestLib
{
public:
    TestLib();

    int testFunc();

private:
    int num;
};  
//lib.cpp
#include "lib.h"

TestLib::TestLib()
    : num(9)
{

}

int TestLib::testFunc()
{
    num++;//此处11行
    return num + 10;
}
gdb调试：

$ gdb test_main    #使用gdb指令开始调试
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from test_main...
(No debugging symbols found in test_main)
(gdb) b main      #在调用so接口的入口函数打断点，以方便进入so调试
Breakpoint 1 at 0x1231
(gdb) r      #开始运行代码
Starting program: /media/sherlock/software/code/c++/untitled2/build/test_main 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x0000555555555231 in main ()  #代码运行至断点，因为二进制没有调试信息，所以这里没有打印代码出来
(gdb) b lib.cpp:11   #在so的代码的11行打断点
Breakpoint 2 at 0x7ffff7fb7120: file /media/sherlock/software/code/c++/untitled2/lib.cpp, line 11.
(gdb) continue    #继续运行至断点
Continuing.

Breakpoint 2, TestLib::testFunc (this=0x7fffffffdcd0) at /media/sherlock/software/code/c++/untitled2/lib.cpp:11
11	    num++;    #此处是断点处代码
(gdb) n     #继续运行一行代码
12     return num + 10;#下一行代码
(gdb) print num    #打印num的值
$1 = 10
(gdb) continue    #继续运行至下个断点
Continuing.
20       #cout打印内容
[Inferior 1 (process 3325102) exited normally]
(gdb)

 gdb map_manage map_manage-10192.1726057270.core

 (gdb) bt