---
title: lambda表达式
date: 2022-5-5 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
hidden: true
---

# 参考网址:

[(131条消息) C++11——lambda表达式_两片空白的博客-CSDN博客_lambda表达式c++11](https://blog.csdn.net/weixin_57023347/article/details/120999277?ops_request_misc=&request_id=&biz_id=102&utm_term=lambda表达式c++&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-120999277.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

# 使用

```c++
int main(){
	//最简单的lambda表达式
	[]{};
 
	//捕捉当前作用域的变量，没有参数，编译器推导返回值类型。
	int a = 1;
	int b = 2;
	[=]{return a + b; };
 
	//使用和仿函数差不多
	auto fun1 = [&](int c){b = a + c; };
	fun1(10);
	cout << a << " " << b << endl;
 
	auto fun2 = [&](int c)->int{return a + c; };
	fun2(20);
	cout << fun2(20) << endl;
 
	//传值捕捉
	int x = 1;
	int y = 2;
	auto add0 = [x, y]()mutable->int{ x *= 2;//捕捉传递传值具有常性
									return x + y; };
	cout << add0() << endl;
 
	auto add1 = [&x, y]()->int{ x *= 2;//捕捉传递引用不具有常性
							return x + y; };
	cout << add1() << endl;
 
	auto add2 = [](int s, int m)->int{ s *= 2;//参数不具有常性
								return s + m; };
	system("pause");
	return 0;
}
```

```cc
int main(){
	//最简单的lambda表达式
	[]{};
 
	//捕捉当前作用域的变量，没有参数，编译器推导返回值类型。
	int a = 1;
	int b = 2;
 
	//auto fun1 = [x, y]()->int{return x + y; };//编译错误，要和捕捉参数名相同
	//传值传递是捕捉变量的拷贝，实际外面的a，b没有交换
	auto swap1 = [a, b]()mutable{int z = a; a = b; b = z; };
	swap1();//注意还需要调用
	cout << a << " " << b << endl;
 
	//传引用才能真正修改
	auto swap2 = [&a, &b]{int z = a; a = b; b = z; };
	swap2();
	cout << a << " " << b << endl;
	return 0;
}
```

```cc
void (*PF)();
int main(){
 
 
	auto f1 = []{cout << "hello world" << endl; };
	auto f2 = []{cout << "hello world" << endl; };
	// 此处先不解释原因，等lambda表达式底层实现原理看完后，大家就清楚了
	//f1 = f2; // 编译失败--->提示找不到operator=()
 
	// 允许使用一个lambda表达式拷贝构造一个新的副本
	auto f3(f2);
	f3();
 
	// 可以将lambda表达式赋值给相同类型的函数指针
	PF = f2;
	PF();
	return 0;
 
}
```

