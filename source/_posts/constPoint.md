-----
title: const与指针
tags: C++,const,指针
-----
<font face="微软雅黑" size="4" color ="black">
之前关于const与指针的位置的差别，总是将指向常量的指针与常量指针混淆，说到底其实还是没有真的理解，下面做一个梳理
<!-- more -->
根据const和*的位置差别，总共有六种写法
1. const int p; 
2. const int* p; 
3. int const* p; 
4. int * const p;
5. const int * const p;
6. int const * const p;

第一种是常量整数，无疑问；第二种到第六种都有指针的参与，我们在读法种加上“point to”的读法(从右往左读)，将*替换成 point to ,因此就有了下面的几种读法：
第二种: p is a point to int const :即 一个指向整型常量的指针
第三种: p is a point to const int;和第二种意思一样，即一个指向常量的指针
第四种: p is a const point to int;即一个指向整型的常量指针
第五种: p is a const point to int const;即一个指向整型常量的常量指针
第六种: p is a const point to cont int;和第五种的意思差不多，即一个指向常量整型的常量指针
从上面的几种读法就应该知道什么是常量指针和指向常量的指针，网上有些人简单的称谓常量指针和指针常量，关于指针常量，C++primer中的一个术语就是指向常量的指针说的就是这个，所以我们以后还是别称为指针常量，应该说指向常量的指针。
指向常量的指针，可以更改指针指向的数据
```C++
int a = 10;
	int b = 1;
	const int *p = NULL;
	p = &a; //ok
	p = &b; //ok
	int const *p = NULL;
	p = &a; //ok
	p = &b; //ok
	int * const p = &a;
	*p = 4; //ok
	p = &b; //error
	const int * const p = &a;
	*p = 3; //error
	p = &b; //error
	int const * const p = &a;
	*p = 4; //error
	p = &b; //error
```


