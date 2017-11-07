---
title: Linux下安装OpenCV（Redhat+OpenCV3.1）
tags: Linux,opencv
---

- 1.官网下载OpenCV最新版(version:3.1.0---Linux)
- 2.在home文件夹下创建一个openCV的文件夹，将下载好的zip格式的OpenCV放在此目录下  
- 3.确认是否有安装cmake,如果没有安装，请前往[https://cmake.org/download/](https://cmake.org/download/)下载安装，下载之后放在home文件夹下，输入
<!-- more -->
``` bash
tar zxvf cmake(文件名)
```
然后就进入解压目录,执行
```bash
./bootstrap 
```
完成之后执行
```bash
make
```
等待编译完成，完成之后执行
```bash
make  install
```
(cmake安装完成了)，执行
```bash
cmake -help
```
可以查看相关信息
- 4.进入opencv的解压目录之后，执行
```bash
cmake CMakeLists.txt
```
命令之后，(这个是必须要先执行的，不然后面那个downloads文件夹是不会出来的)会出现错误原因是有些包没有安装好。出现的错误是无法下载ippicv_linux_20151201.tgz.这个文件，(我估计如果是低版本的话，应该不会出现这种问题吧),将这个文件替换到相应的文件下（我的文件夹是在这个目录(/home/openCV/opencv-3.1.0/3rdparty/ippicv/downloads/linux-808b791a6eac9ed78d32a7666804320e)）,替换之后，重新执行
```bash
cmake CMakeLists.txt
```
命令，无错，接着执行make命令，进行编译，等到编译完成(漫长的编译过程)，编译完成之后，执行
```bash
make install
```
- 5.编译完成之后，Opencv的头文件在
```bash
/usr/local/include
```
目录下;库文件在
```bash
/usr/local/lib
```
目录下；执行
```bash
cd /etc/ld.so.conf.d
```
进入库文件环境变量配置文件夹；<br/>
新建文件
```bash
vi opencv.conf
```
添加内容
```bash
/usr/local/lib
```
- 6.加载库文件配置；执行
```bash
ldconfig
```
- 7.环境变量 在etc目录下，执行命令： vi bashrc；然后在文本的最后添加
``` bash
    PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
    export PKG_CONFIG_PATH
```
- 8.同样在etc目录下执行命令：
```bash
source /etc/bashrc
```
- 9.编写一个例子进行测试是否能使用<br/>
例子参照如下：

```C++
#include <iostream>
#include <string>
#include <opencv2/opencv.hpp>
using namespace std;
using namespace cv;
int main(int argc, char** argv)
{
	Mat image;
	image = imread("lena.jpg");

	if (!image.data)
	{
		cout << "No image data" << endl;
		return -1;
	}
	else {
		cout << "this is a image" << endl;
	}
	Mat img, dst;
	string rst(64, ' ');
	double dIdex[64];
	double mean = 0.0;
	int k = 0;
	if (image.channels() == 3)
	{
		cvtColor(image, image, CV_BGR2GRAY);
		img = Mat_<double>(image);
	}
	else
	{
		img = Mat_<double>(image);
	}

	/* 第一步，缩放尺寸，可以为Size(32,32)或Size(8,8)，也可以更高，主要是为了提高计算效率CV_BGR2GRAY*/
	resize(img, img, Size(32, 32));

	/* 第二步，离散余弦变换，DCT系数求取*/
	dct(img, dst);

	/* 第三步，求取DCT系数均值（左上角8*8区块的DCT系数）*/
	for (int i = 0; i < 8; ++i) {
		for (int j = 0; j < 8; ++j)
		{
			dIdex[k] = dst.at<double>(i, j);
			mean += dst.at<double>(i, j) / 64;
			++k;
		}
	}

	/* 第四步，计算哈希值。*/
	for (int i = 0; i < 64; ++i)
	{
		if (dIdex[i] >= mean)
		{
			rst[i] = '1';
		}
		else
		{
			rst[i] = '0';
		}
	}
	cout << rst << endl;

	return 0;
}


```
