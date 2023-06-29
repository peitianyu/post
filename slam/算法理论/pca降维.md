---
title: pca降维
date: 2022-4-21 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

[PCA算法的原理以及c++实现_chengwei0019的博客-CSDN博客_c++ pca算法](https://blog.csdn.net/u012700322/article/details/104822390?ops_request_misc=&request_id=&biz_id=102&utm_term=pca c++&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-4-104822390.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

[ihar/EigenPCA: A class for performing principal component analysis using Eigen library (github.com)](https://github.com/ihar/EigenPCA)

[(PCA降维原理及其代码实现](https://blog.csdn.net/kobeyu652453/article/details/107058229)

# 步骤

```txt
PCA主成分分析，是模式识别中常见的特征降维的算法，其大体步骤可以分为以下几个部分：
（1）原始特征矩阵归一化处理（假设M和样本，每个样本n个特征，则对M*N的X数据，进行零均值化，即减去这一列的均值）
（2）求取归一化处理后特征矩阵的协方差矩阵
（3）计算协方差矩阵的特征值及其对应的特征向量
（4）将特征向量按对应特征值大小从上到下按行排列成矩阵，取前k行组成矩阵P；
（5）Y=PX即为降维到k维后的数据;
```

test_data.txt

```txt
10 2
2.5 2.4
0.5 0.7
2.2 2.9
1.9 2.2
3.1 3.0
2.3 2.7
2.0 1.6
1.0 1.1
1.5 1.6
1.1 0.9
```

```cpp
#include<iostream>
#include<algorithm>
#include<cstdlib>
#include<fstream>
#include "Eigen/Dense"
using namespace std;
using namespace Eigen;
void featurenormalize(MatrixXd &X)
{
	//计算每一维度均值
	MatrixXd meanval = X.colwise().mean();
	RowVectorXd meanvecRow = meanval;
	//样本均值化为0
	X.rowwise() -= meanvecRow;
}
void computeCov(MatrixXd &X, MatrixXd &C)
{
	//计算协方差矩阵C = XTX / n-1;
	C = X.adjoint() * X;
	C = C.array() / (X.rows() - 1);
}
void computeEig(MatrixXd &C, MatrixXd &vec, MatrixXd &val)
{
	//计算特征值和特征向量，使用selfadjont按照对阵矩阵的算法去计算，可以让产生的vec和val按照有序排列
	SelfAdjointEigenSolver<MatrixXd> eig(C);
 
	vec = eig.eigenvectors();
	val = eig.eigenvalues();
}
int computeDim(MatrixXd &val)
{
	int dim;
	double sum = 0;
	for (int i = val.rows() - 1; i >= 0; --i)
	{
		sum += val(i, 0);
		dim = i;
 
		if (sum / val.sum() >= 0.95)
			break;
	}
	return val.rows() - dim;
}
int main()
{
	ifstream fin("test_data");
	ofstream fout("output.txt");
	//读取数据
	double a, b; fin >> a; fin >> b;
	std::cout << "pass " << a << "  " << b << std::endl;
 
	const int m = a, n = b;
	MatrixXd X(m, n), C(n, n);
	MatrixXd vec, val;
 
	
	double in[200];
	for (int i = 0; i < m; ++i)
	{
		for (int j = 0; j < n; ++j)
			fin >> in[j];
		for (int j = 1; j <= n; ++j)
			X(i, j - 1) = in[j-1];
	}
	//pca
 
	//零均值化
	featurenormalize(X);
	//计算协方差
	computeCov(X, C);
	std::cout << "cov: \n" << C << std::endl;
	//计算特征值和特征向量
	computeEig(C, vec, val);
	//计算损失率，确定降低维数
	int dim = computeDim(val);
	std::cout << val << std::endl;
	std::cout << vec << std::endl;
	//计算结果
	MatrixXd res = X * vec.rightCols(n);
	//输出结果
	fout << "the result is " << res.rows() << "x" << res.cols() << " after pca algorithm." << endl;
	fout << res;
	fout.close();
	system("pause");
	return 0;
}
```

