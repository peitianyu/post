---
title: cpp学习
date: 2021-10-24 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp 
---

# 参考网址:

[C++ 教程 (github.com)](https://github.com/gyshgx868/cpp-tutorial)

[C++ 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/cplusplus/cpp-tutorial.html)

# c++基础

## const关键字

声明指定类型的常量,此类型不会发生变化.

比如常用的字符串`const char* str`,例如

```c++
const char* str = "hello world!";
```

当然也会有常数,常指针等.只需要记住它修饰的是它后边的那个.例如

```c++
const char* str #值不能改
char const *str #值不能改
char* const str #修饰的是常数指针,该指针不能修改
```

## static修饰

静态变量,在程序的生命周期内保持局部变量的存在,用于保存上一次运行数据,比如计数等

```c++
static int i = 5;
```

## #define

预处理器,例如

```c++
#define PORT 7070
```

## extern储存类

**extern** 存储类用于提供一个全局变量的引用,全局变量对所有的程序文件都是可见的.例如

```c++
// main.cpp
#include <iostream>
int count ;
extern void write_extern();
 
int main()
{
   count = 5;
   write_extern();
}
```

```c++
// support.cpp
#include <iostream>
extern int count;
 
void write_extern(void)
{
   std::cout << "Count is " << count << std::endl;
}
```

两个文件之间传递参数.

## register 存储类

寄存器局部变量,用于快速访问.因此也意味着最大尺寸只有寄存器尺寸.

```c++
register int a;
```

## thread_local 存储类

量在创建线程时创建，并在销毁线程时销毁。可以将 thread_local `仅应用于数据声明和定义`，thread_local `不能用于函数声明或定义`。thread_local 说明符可以与 static 或 extern 合并。

```c++
thread_local int x;  // 命名空间下的全局变量
class X
{
    static thread_local std::string s; // 类的static成员变量
};
static thread_local std::string X::s;  // X::s 是需要定义的
 
void foo()
{
    thread_local std::vector<int> v;  // 本地变量
}
```

## 成员运算符

```c++
struct Employee {
  char first_name[16];
  int  age;
} emp;
```

### （.）点运算符

下面的代码把值 "zara" 赋给对象 emp 的 **first_name** 成员

```
strcpy(emp.first_name, "zara");
```

### （->）箭头运算符

如果 p_emp 是一个指针，指向类型为 Employee 的对象，则要把值 "zara" 赋给对象 emp 的 **first_name** 成员，需要编写如下代码：

```c++
strcpy(p_emp->first_name, "zara");
```

## 条件运算符 ? :

```c++
if(y < 10){ 
   var = 30;
}else{
   var = 40;
}
```

可写为:

```c++
var = (y < 10) ? 30 : 40;
```

## sizeof 运算符

sizeof 运算符可用于获取类、结构、共用体和其他用户自定义数据类型的大小。

```c++
sizeof (data type)
```

## 指针运算符（& 和 *）

### 取地址运算符 & 间接寻址运算符 *

顾名思义,挺形象的.

```c++
#include <iostream>
 
using namespace std;
 
int main ()
{
   int  var;
   int  *ptr;
   int  val;
   var = 3000;
   // 获取 var 的地址
   ptr = &var;
   // 获取 ptr 的值
   val = *ptr;
   cout << "Value of var :" << var << endl;
   cout << "Value of ptr :" << ptr << endl;
   cout << "Value of val :" << val << endl;

   return 0;
}
```

## 传参三种方式

### **传值、传址、传引用**

```c++
#include<iostream>
#include <stdint.h>
using namespace std;
int  fun1(int a)
    return a*a;
void  man(int &a)
    a=a*a;
void cubeByReference(int *a)
    *a=*a * *a;
void fun2(uint8_t* *buffer)
{
    uint8_t buf[10]={0,1,2,3,4,5,6,7,8,9};
    *buffer = buf;
}
int main()
{
    int a=5,b=5,number=5;
    cout<<fun1(a)<<endl;
 
    man(b);
    cout<<b<<endl;
    
    cubeByReference(&number);
    cout<<number<<endl;
    
    return 0;
}
```

## Lambda 函数与表达式

```
[capture](parameters)->return-type{body}
```

例如:

```c++
[](int x, int y) -> int { int z = x + y; return z + x; }
```

```c++
[]      // 沒有定义任何变量。使用未定义变量会引发错误。
[x, &y] // x以传值方式传入（默认），y以引用方式传入。
[&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
[=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
[&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
[=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。
```

值得注意的是`this`指针

```c++
[this]() { this->someFunc(); }();
```

## 数学运算 #include <cmath

```c++
#include <iostream>
#include <cmath>
using namespace std;
 
int main ()
{
   // 数字定义
   short  s = 10;
   int    i = -1000;
   long   l = 100000;
   float  f = 230.47;
   double d = 200.374;
 
   // 数学运算
   cout << "sin(d) :" << sin(d) << endl;
   cout << "abs(i)  :" << abs(i) << endl;
   cout << "floor(d) :" << floor(d) << endl;
   cout << "sqrt(f) :" << sqrt(f) << endl;
   cout << "pow( d, 2) :" << pow(d, 2) << endl;
 
   return 0;
}
```

## 通过time实现的伪随机数

```c++
#include <iostream>
#include <ctime>
#include <cstdlib>
 
using namespace std;
 
int main ()
{
   int i,j;
   // 设置种子
   srand( (unsigned)time( NULL ) );
   /* 生成 10 个随机数 */
   for( i = 0; i < 10; i++ )
   {
      // 生成实际的随机数
      j= rand();
      cout <<"随机数： " << j << endl;
   }
 
   return 0;
}
```

## setw() 函数

```c++

#include <iomanip>
using std::setw;
cout << setw( 7 )<< j << setw( 13 ) << n[ j ] << endl;
# 结果
Element        Value
      0          100
      1          101
      2          102
      3          103
      4          104
      5          105
      6          106
      7          107
      8          108
      9          109
```

## 字符串操作 \#include <string

```c++
strcpy(s1, s2); // s2复制到s1
strcat(s1, s2); // s2拼接到s1后边,类似于:string str = str1 + str2;
strlen(s1);     // 字符串长度
strcmp(s1, s2); // 比较两字符串,相同回0,s1<s2返回小于0,反之,大于0
strchr(s1, ch); // 返回字符ch第一次在s1中出现得位置指针
strstr(s1, s2); // 返回字符串s2在s1中出现的位姿指针
// 复制 str1 到 str3
	str3 = str1;
// 连接 str1 和 str2
   	str3 = str1 + str2;
// 连接后，str3 的总长度
   	len = str3.size();
```

## 时间 \#include <ctime

```c++
time_t time(time_t *time); 						// 返回当前时间
char *ctime(const time_t *time); 				// 返回 day month year hours:minutes:seconds year\n\0
struct tm *localtime(const time_t *time); 		// 返回tm时间结构体
clock_t clock(void);                			// 该函数返回程序执行起,处理器时钟所使用的时间
char * asctime ( const struct tm * time );		// 返回 day month date hours:minutes:seconds year\n\0
struct tm *gmtime(const time_t *time); 			// 用协调世界时（UTC）也被称为格林尼治标准时间（GMT）表示
time_t mktime(struct tm *time);					// 该函数返回日历时间
double difftime ( time_t time2, time_t time1 ); // 该函数返回 time1 和 time2 之间相差的秒数
size_t strftime();								// 该函数可用于格式化日期和时间为指定的格式
```

```c++
#include <iostream>
#include <ctime>
using namespace std;

int main( )
{
   // 基于当前系统的当前日期/时间
   time_t now = time(0);
   // 把 now 转换为字符串形式
   char* dt = ctime(&now);
   cout << "本地日期和时间：" << dt << endl;
   // 把 now 转换为 tm 结构
   tm *gmtm = gmtime(&now);
   dt = asctime(gmtm);
   cout << "UTC 日期和时间："<< dt << endl;
}
```

## **cin、cout、cerr** 和 **clog** 标准输入输出

```c++
cin :一个istream对象,用来从标准输入读取数据。
cout:一个ostream对象，经过缓冲区而直接输出.
cerr:一个ostream对象,写到cerr数据是不缓冲
clog:一个ostream对象,被缓冲的
```

## 结构体

```c++
// 模板
// 使用 Books Book;
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
};

// 使用 books Book;
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
}books;

// 取别名 pin32
typedef long int pint32;
pint32 x, y, z;
```

结构体指针

```c++
struct Books *struct_pointer;
// 取地址
struct_pointer = &Book1;
// 访问成员
struct_pointer->title;
```

# 面向对象

## c++对象

```c++
class Line
{
    // 访问修饰符
   public:
     // 类成员函数 
      double getLength( void );
      Line();   				// 构造函数声明
      friend void printWidth( Box box ); // 友元函数
      Line( const Line &obj); 	//拷贝构造函数
      ~Line();  				// 析构函数声明
 	
   private:
      double length;
}; 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
Line::Line(const Line &obj)
{
    cout << "调用拷贝构造函数并为指针 ptr 分配内存" << endl;
    ptr = new int;
    *ptr = *obj.ptr; // 拷贝值
}
double Line::getLength( void )
{
    return length;
}
// 请注意：printWidth() 不是任何类的成员函数
void printWidth( Box box )
{
   /* 因为 printWidth() 是 Box 的友元，它可以直接访问该类的任何成员 */
   cout << "Width of box : " << box.width <<endl;
}
```

## 内联函数inline

引入内联函数的目的是为了解决程序中函数调用的效率问题，这么说吧，程序在编译器编译的时候，编译器将程序中出现的内联函数的调用表达式用内联函数的函数体进行替换，而对于其他的函数，都是在运行时候才被替代。这其实就是个`空间代价换时间的节省`。所以内联函数一般都是1-5行的小函数。在使用内联函数时要留神:

- 1.在内联函数内不允许使用循环语句和开关语句；
- 2.内联函数的定义必须出现在内联函数第一次调用之前；
- 3.类结构中所在的类说明内部定义的函数是内联函数。

结论: 一个较为合理的经验准则是, **不要内联超过 10 行的函数**. **谨慎对待析构函数**, 析构函数往往比其表面看起来要更长, 因为有隐含的成员和基类析构函数被调用!

```c++
#include <iostream>
using namespace std;

inline int Max(int x, int y)
{
   return (x > y)? x : y;
}
// 程序的主函数
int main( )
{
   cout << "Max (20,10): " << Max(20,10) << endl;
   cout << "Max (0,200): " << Max(0,200) << endl;
   cout << "Max (100,1010): " << Max(100,1010) << endl;
   return 0;
}
```

## this指针

友元函数没有 **this** 指针，因为友元不是类的成员。只有成员函数才有 **this** 指针。

**this** 指针的类型可理解为 **Box\***

```c++
# 实际上就是指针引用结构体成员
this->Volume();
```

## 继承

```c++
#include <iostream>
 
using namespace std;
 
// 基类
class Shape 
{
   public:
      void setWidth(int w)
      {
         width = w;
      }
      void setHeight(int h)
      {
         height = h;
      }
   protected:
      int width;
      int height;
};
 
// 派生类
class Rectangle: public Shape
{
   public:
      int getArea()
      { 
         return (width * height); 
      }
};
 
int main(void)
{
   Rectangle Rect;
 
   Rect.setWidth(5);
   Rect.setHeight(7);
 
   // 输出对象的面积
   cout << "Total area: " << Rect.getArea() << endl;
 
   return 0;
}
```

### 多继承

另外多继承(环状继承),A->D, B->D, C->(A，B)，例如：

```c++
class D{......};
class B: public D{......};
class A: public D{......};
class C: public B, public A{.....};
```

## 重载运算符

类内重调用

```c++
Box operator+(const Box& b)
      {
         Box box;
         box.length = this->length + b.length;
         box.breadth = this->breadth + b.breadth;
         box.height = this->height + b.height;
         return box;
      }
```

|   算术运算符   |            + (加)，-(减)，*(乘)，/(除)，% (取模)             |
| :------------: | :----------------------------------------------------------: |
|   关系运算符   | ==(等于)，!= (不等于)，< (小于)，> (大于)，<=(小于等于)，>=(大于等于) |
|   逻辑运算符   |             \|\|(逻辑或)，&&(逻辑与)，!(逻辑非)              |
|   单目运算符   |              + (正)，-(负)，*(指针)，&(取地址)               |
| 自增自减运算符 |                      ++(自增)，--(自减)                      |
|    位运算符    | \| (按位或)，& (按位与)，~(按位取反)，^(按位异或),，<< (左移)，>>(右移) |
|   赋值运算符   |       =, +=, -=, *=, /= , % = , &=, \|=, ^=, <<=, >>=        |
| 空间申请与释放 |                new, delete, new[ ] , delete[]                |
|   其他运算符   | **()**(函数调用)，**->**(成员访问)，**,**(逗号)，**[]**(下标) |

## 多态

```c++
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0)
      {
         width = a;
         height = b;
      }
      // 虚函数
      virtual int area()
      {
         cout << "Parent class area :" <<endl;
         return 0;
      }
      // 纯虚数
      virtual int area1() = 0;
};
class Rectangle: public Shape{
   public:
      Rectangle( int a=0, int b=0):Shape(a, b) { }
      int area ()
      { 
         cout << "Rectangle class area :" <<endl;
         return (width * height); 
      }
};
```

## 文件和流

头文件:**iostream** 和 **fstream**

### 读写文件模板

```c++
#include <fstream>
#include <iostream>
using namespace std;
 
int main ()
{
   char data[100];
   // 以写模式打开文件
   ofstream outfile;
   outfile.open("afile.dat");
   cout << "Writing to the file" << endl;
   cout << "Enter your name: "; 
   cin.getline(data, 100);
   // 向文件写入用户输入的数据
   outfile << data << endl;
   cout << "Enter your age: "; 
   cin >> data;
   cin.ignore();
   // 再次向文件写入用户输入的数据
   outfile << data << endl;
   // 关闭打开的文件
   outfile.close();
   // 以读模式打开文件
   ifstream infile; 
   infile.open("afile.dat"); 
   cout << "Reading from the file" << endl; 
   infile >> data; 
   // 在屏幕上写入数据
   cout << data << endl;
   // 再次从文件读取数据，并显示它
   infile >> data; 
   cout << data << endl; 
   // 关闭打开的文件
   infile.close();
   return 0;
}
```

### 文件位置指针

```c++
// 定位到 fileObject 的第 n 个字节（假设是 ios::beg）
fileObject.seekg( n );
// 把文件的读指针从 fileObject 当前位置向后移 n 个字节
fileObject.seekg( n, ios::cur );
// 把文件的读指针从 fileObject 末尾往回移 n 个字节
fileObject.seekg( n, ios::end );
```

## 异常处理

```c++
#include <iostream>
using namespace std;

double division(int a, int b)
{
   if( b == 0 )
   {
      throw "Division by zero condition!";
   }
   return (a/b);
}
int main ()
{
   int x = 50;
   int y = 0;
   double z = 0;
   try {
     z = division(x, y);
     cout << z << endl;
   }catch (const char* msg) {
     cerr << msg << endl;
   }
   return 0;
}
```

## 动态内存

### 变量动态内存

```c++
double* pvalue  = NULL; // 初始化为 null 的指针
pvalue  = new double;   // 为变量请求内存
delete pvalue;        // 释放 pvalue 所指向的内存
```

### 数组动态内存

```c++
char* pvalue  = NULL;   // 初始化为 null 的指针
pvalue  = new char[20]; // 为变量请求内存

for(int i = 0; i < COL; i++) {
    delete[] pvalue[i];
}
delete [] pvalue; 
```

### 对象的动态内存分配

```c++
Box* myBoxArray = new Box[4];
delete [] myBoxArray; // Delete array
```

### 模板

```
#include <iostream>
using namespace std;

int main ()
{
   double* pvalue  = NULL; // 初始化为 null 的指针
   pvalue  = new double;   // 为变量请求内存
 
   *pvalue = 29494.99;     // 在分配的地址存储值
   cout << "Value of pvalue : " << *pvalue << endl;

   delete pvalue;         // 释放内存

   return 0;
}
```

## 命名空间

```c++
namespace namespace_name {
	func();
   // 代码声明
}
name::func();  // code 可以是变量或函数
```

## 模板

```c++
#include <iostream>
using namespace std;
template<typename T1,typename T2>
class Test{
public:
    Test(T1 i,T2 j):a(i),b(j){cout<<"模板类"<<endl;}
private:
    T1 a;
    T2 b;
};
template<>   //全特化，由于是全特化，参数都指定了，参数列表故为空。
class Test<int ,char>{
public:
    Test(int i,char j):a(i),b(j){cout<<"全特化"<<endl;}
private:
    int a;
    int b;
};
template<typename T2> //由于只指定了一部分参数，剩下的未指定的需在参数列表中，否则报错。
class Test<char,T2>{
public:
    Test(char i,T2 j):a(j),b(j){cout<<"个数偏特化"<<endl;}
private:
    char a;
    T2 b;
};
template<typename T1,typename T2> //这是范围上的偏特化
class Test<T1*,T2*>{
public:
    Test(T1* i,T2* j):a(i),b(j){cout<<"指针偏特化"<<endl;}
private:
    T1* a;
    T2* b;
};
template<typename T1,typename T2>//同理这也是范围上的偏特化
class Test<T1 const,T2 const>{
public:
    Test(T1 i,T2 j):a(i),b(j){cout<<"const偏特化"<<endl;}
private:
    T1 a;
    T2 b;
};
int main()
{
    int a;
    Test<double,double> t1(0.1,0.2);
    Test<int,char> t2(1,'A');
    Test<char,bool> t3('A',true);
    Test<int*,int*> t4(&a,&a);
    Test<const int,const int> t5(1,2);
    return 0;
}
```

## 预处理器

```c++
#define PI 3.14159
#define MIN(a,b) (((a)<(b)) ? a : b)
```

### 条件编译

```c++
#define DEBUG
#ifdef DEBUG
   cerr <<"Trace: Coming out of main function" << endl;
#endif
```

### # 和 ## 运算符

```c++
# 将x转换为字符串
#define MKSTR( x ) #x
cout << MKSTR(HELLO C++) << endl;
## 将x,y连接
#define CONCAT( x, y )  x ## y
int xy = 100;
cout << concat(x, y);
```

### 预定义宏

```c++
cout << "Value of __LINE__ : " << __LINE__ << endl; //当前行号
cout << "Value of __FILE__ : " << __FILE__ << endl; //当前文件名
cout << "Value of __DATE__ : " << __DATE__ << endl; //当前年月日
cout << "Value of __TIME__ : " << __TIME__ << endl; //当前时分秒
```

## 信号处理

### 信号类型

```
SIGINT、SIGABRT、SIGFPE、SIGILL、SIGSEGV、SIGTERM、SIGHUP
```

```c++
#include <iostream>
#include <csignal>

using namespace std;

void signalHandler( int signum )
{
    cout << "Interrupt signal (" << signum << ") received.\n";
    // 清理并关闭
    // 终止程序 
   exit(signum);  
}
int main ()
{
    int i = 0;
    // 注册信号 SIGINT 和信号处理程序
    signal(SIGINT, signalHandler);  
    while(++i){
       cout << "Going to sleep...." << endl;
       if( i == 3 ){
          // 信号抬起
          raise( SIGINT);
       }
       sleep(1);
    }
    return 0;
}
```

## 多线程

```c++
#include <pthread.h>
pthread_create (thread, attr, start_routine, arg) 
// 参数:线程指针,线程属性,线程函数,函数参数
// 线程显性退出
pthread_exit (NULL); 
```

### 向线程传递参数

```c++
struct thread_data{
   int  thread_id;
   char *message;
};
struct thread_data td[NUM_THREADS];
rc = pthread_create(threadid, NULL,PrintHello, (void *)&td[i]);
```

### 连接和分离线程

```c++
pthread_join (threadid, status) 
pthread_detach (threadid) 
```

```c++
#include <iostream>
#include <cstdlib>
#include <pthread.h>
#include <unistd.h>

using namespace std;

#define NUM_THREADS     5

void *wait(void *t)
{
   int i;
   long tid;
    
   tid = (long)t;
   sleep(1);
   cout << "Sleeping in thread " << endl;
   cout << "Thread with id : " << tid << "  ...exiting " << endl;
   pthread_exit(NULL);
}
int main ()
{
   int rc;
   int i;
   pthread_t threads[NUM_THREADS];
   pthread_attr_t attr;
   void *status;
   // 初始化并设置线程为可连接的（joinable）
   pthread_attr_init(&attr);
   pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

   for( i=0; i < NUM_THREADS; i++ ){
      cout << "main() : creating thread, " << i << endl;
      rc = pthread_create(&threads[i], NULL, wait, (void *)i );
      if (rc){
         cout << "Error:unable to create thread," << rc << endl;
         exit(-1);
      }
   }
   // 删除属性，并等待其他线程
   pthread_attr_destroy(&attr);
   for( i=0; i < NUM_THREADS; i++ ){
      rc = pthread_join(threads[i], &status);
      if (rc){
         cout << "Error:unable to join," << rc << endl;
         exit(-1);
      }
      cout << "Main: completed thread id :" << i ;
      cout << "  exiting with status :" << status << endl;
   }

   cout << "Main: program exiting." << endl;
   pthread_exit(NULL);
}
```

## vector与iterator

```c++
#include <iostream>
#include <vector>
using namespace std;
 
int main()
{
   // 创建一个向量存储 int
   vector<int> vec; 
   int i;
   // 显示 vec 的原始大小
   cout << "vector size = " << vec.size() << endl;
   // 推入 5 个值到向量中
   for(i = 0; i < 5; i++){
      vec.push_back(i);
   }
   // 显示 vec 扩展后的大小
   cout << "extended vector size = " << vec.size() << endl;
   // 访问向量中的 5 个值
   for(i = 0; i < 5; i++){
      cout << "value of vec [" << i << "] = " << vec[i] << endl;
   }
   // 使用迭代器 iterator 访问值
   vector<int>::iterator v = vec.begin();
   while( v != vec.end()) {
      cout << "value of v = " << *v << endl;
      v++;
   }
   return 0;
}
```

