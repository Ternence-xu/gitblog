-----
title: 单例模式(饿汉和懒汉)C++
tags: C++,单例模式
-----
<font face="微软雅黑" size="3" color ="black">
普通的单例模式(线程不安全)
----
下面的这个单例模式可以说是教科书上的标准模式，但是我们可以从代码上看出创建实例的时间，他是在有这个需求的时候才会创建，但是在多线程中如果有多个线程同时检测这个实例是否存在并创建这个实例的话,就会出现混乱,实例的创建顺序就会产生影响，甚至导致程序崩溃。所以我们说这种模式是一种线程不安全的。
<!-- more -->
```C++
class singleton
{
protected:
	singleton(){}
private:
	static singleton* g_singleton;
public:
	static singleton* GetInstance();
}
singleton* singleton::g_singleton = NULL;
singleton* singleton::GetInstance()
{
	if(p_singleton==NULL)
	{
		p = new singleton();
	}
	return p;
}
```
懒汉和饿汉
---
单例模式大致有两种实现方式：懒汉和饿汉
-  懒汉模式： 懒汉模式就是说不到万不得已就不会去实例化类，也就是说在第一次用到类实例的时候才会去实例化，所以上边的经典方法被归为懒汉实现；
-  饿汉模式： 饿汉模式就是在单例类定义的时候进行实例化。</br>
<font color="red">特点和选择</font>
-  线程比较多的时候,适合使用饿汉模式,以空间换时间
-  线程比较少的时候,适合使用懒汉模式,以时间换空间

懒汉(线程安全)
---

- no.1 加锁

```C++
class singleton
{
protected:
	singleton()
	{
		pthread_mutex_init(&mutex);
	}
private:
	static singleton* p;
public:
	static pthread_mutex_t mutex;
	static singleton* initance();
};
pthread_mutex_t singleton::mutex;
singleton* singleton::p = NULL;
singleton* singleton::initance()
{
	if (p == NULL)
	{
		pthread_mutex_lock(&mutex);
		if (p == NULL)
			p = new singleton();
		pthread_mutex_unlock(&mutex);
	}
	return p;
}
```

- no.2 静态变量

```C++
class singleton
{
protected:
    singleton()
    {
        pthread_mutex_init(&mutex);
    }
public:
    static pthread_mutex_t mutex;
    static singleton* initance();
    int a;
};

pthread_mutex_t singleton::mutex;
singleton* singleton::initance()
{
    pthread_mutex_lock(&mutex);
    static singleton obj;
    pthread_mutex_unlock(&mutex);
    return &obj;
}
```
饿汉
---

饿汉其实本身就是线程安全的,因为在程序运行之前就已经实例化了</font>
```c++
class singleton
{
protected:
	singleton()
	{}
private:
	static singleton* p;
public:
	static singleton* GetInstance();
};

singleton* singleton::p = new singleton;
singleton* singleton::GetInstance()
{
	return p;
}
```

