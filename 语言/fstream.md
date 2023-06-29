---
title: fstream
date: 2022-4-26 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

[(128条消息) C++ 深究fstream打开方式ios::app,ios::ate,ios::in,ios::out_悟名堂的博客-CSDN博客_fstream ios::app](https://blog.csdn.net/weixin_42333471/article/details/88093789?ops_request_misc=%7B%22request%5Fid%22%3A%22165093885916782425191425%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165093885916782425191425&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-88093789.142^v9^control,157^v4^control&utm_term=+std%3A%3Aios%3A%3Aout+|+std%3A%3Aios%3A%3Aapp&spm=1018.2226.3001.4187)

# 例程

## 默认(覆盖原文件)

```c++
#include<fstream>
#include<iostream>
using namespace std;
int main()
{
	char data[100];
	
	//第一次打开文件
	ofstream outfile;
	outfile.open("D:\\afile.dat");
	cout << "Writing to the file" << endl;
	cout << "Enter your name:";
	cin.getline(data, 100);
	outfile << data << endl;
	outfile.close();
	
	//第二次打开文件
	ofstream outfile2;
	outfile2.open("D:\\afile.dat");//**待修改语句**
	cout << "Enter your age:";
	cin >> data;
	outfile2 << data << endl;
	outfile2.close();//第二次关闭文件
	
	return 0;
}
```

# 使用iso::app(在文末尾添加)

```
	outfile2.open("D:\\afile.dat", ios::app);
```

