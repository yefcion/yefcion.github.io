---
title: OpenCV 读取视频并跳帧保存为图片
date: 2018-07-31 12:45:17
tags:
- OpenCV
- C++
- 图像处理
- 视频
- 帧
categories: 技术向
description: opencv
cover_img: http://m.qpic.cn/psb?/V10z9tn33iz4ON/u6ymMuEDp0bc3Rnll7kFZt0jXZWd4XnpY2z4R2m4tK0!/b/dAcBAAAAAAAA&bo=3AXoAwAAAAADBxA!&rf=viewer_4
---



# OpenCV读取视频并跳帧保存为图片

[TOC]

### 介绍几个有关视频读取的函数：

#### **VideoCapture::VideoCapture**

​	VideoCapture可以从文件中或者摄像头中读取视频，这是提供给C++的接口的，C的接口是CvCapture结构。

```C++
 VideoCapture::VideoCapture(conststring& filename)
 VideoCapture::VideoCapture(int device)
```

上面是初始化VideoCapture对象的，第一个是从文件中读取，第二个是从设备中读取，默认设备在这里是0；

例子：

```c++
VideoCapturecapture(“sample.avi”); /*读取sample.avi文件*/
VideoCapturecapture(0); /*如果只有一个摄像头，capture会得到摄像头的视频*/
```



#### **VideoCapture::Open**

​	打开视频文件或者视频设备（例如摄像头）。

```c++
bool VideoCapture::open(const string& filename)
bool VideoCapture::open(int device)
```

例子：

```c++
VideoCapturecapture;
capture.open("sample.avi");
/*这里的两句等效于上面的VideoCapturecapture("sample.avi")，capture.open(0)*/
```

> 上面的视频文件打开，用完后，最好调用 **capture.release()** 来关闭刚刚打开的文件
>

 

#### **VideoCapture::release**

```c++
void VideoCapture::release()
```

例子：

```c++
capture.release(); /*释放打开的视频*/
```

 

#### **VideoCapture::isOpened**

判断视频是否被打开，成功打开返回ture，否则false。

```c++
bool VideoCapture::open(const string& filename)
bool VideoCapture::open(int device)
```

 

#### **VideoCapture::grab**

从视频文件中或者设备中获取下一帧，该方法成功调用返回ture；主要用于多摄像头情况下，特别当那些摄像头没有实现硬件同步。

```c++
bool VideoCapture::grab()
```

grab之后，需要调用retrive对获取的帧进行解码。

#### **VideoCapture::retrieve**

对grab()得到的帧进行解码。

```c++
bool VideoCapture::retrieve(Mat& image, int channel=0)
```

 

#### **VideoCapture::read**

获取、解码，这个方法结合了grab和retrieve，比较方便，

```c++
VideoCapture& VideoCapture::operator>>(Mat& image)
bool VideoCapture::read(Mat& image)
```

例子1：

```c++
// 方法一
if(!capture.read(frame))
{
    cout << "读取视频失败" << endl;
    return-1;
}
//方法二
capture >>frame;
```

 这两个方法都可以，不过第一个能够判断，建议使用第一个，程序更健壮。

 

#### **VideoCapture::get**

返回VideoCapture的一些属性

```c++
double VideoCapture::get(int propId)
```

probId可以是下面的：

·  **CV_CAP_PROP_POS_MSEC** Current position of the video file in milliseconds or video capture timestamp.

·  **CV_CAP_PROP_POS_FRAMES** 0-basedindex of the frame to be decoded/captured next.

·  **CV_CAP_PROP_POS_AVI_RATIO** Relativeposition of the video file: 0 - start of the film, 1 - end of the film.

·  **CV_CAP_PROP_FRAME_WIDTH** Width of theframes in the video stream.

·  **CV_CAP_PROP_FRAME_HEIGHT** Height ofthe frames in the video stream.

·  **CV_CAP_PROP_FPS** Frame rate.

·  **CV_CAP_PROP_FOURCC** 4-character code of codec.

·  **CV_CAP_PROP_FRAME_COUNT** Number of frames in the video file.

·  **CV_CAP_PROP_FORMAT** Format ofthe Mat objects returned by retrieve() .

·  **CV_CAP_PROP_MODE** Backend-specificvalue indicating the current capture mode.

·  **CV_CAP_PROP_BRIGHTNESS** Brightness of the image (only for cameras).

·  **CV_CAP_PROP_CONTRAST** Contrast of the image (only for cameras).

·  **CV_CAP_PROP_SATURATION** Saturation of the image (only for cameras).

·  **CV_CAP_PROP_HUE** Hue of theimage (only for cameras).

·  **CV_CAP_PROP_GAIN** Gain of theimage (only for cameras).

·  **CV_CAP_PROP_EXPOSURE** Exposure(only for cameras).

·  **CV_CAP_PROP_CONVERT_RGB** Boolean flags indicating whether images should be converted to RGB.

·  **CV_CAP_PROP_WHITE_BALANCE** Currently not supported

·  **CV_CAP_PROP_RECTIFICATION** Rectification flag for stereo cameras (note: only supported by DC1394 v 2.x backendcurrently)

  

#### **VideoCapture::set**

VideoCapture类的set方法可以允许我们取出视频中某个位置的帧，它有一些参数，可以按时间，也可以按帧号，还可以按视频长短的比例。 

```c++
bool VideoCapture::set(int propId, double value)
```

例子：

```c++
// 第100帧
double position=100.0;
capture.set(CV_CAP_PROP_POS_FRAMES,position);
// 第1e6毫秒
double position=1e6;
capture.set(CV_CAP_PROP_POS_MSEC,position);
// 视频1/2位置
double position=0.5;
capture.set(CV_CAP_PROP_POS_AVI_RATIO,position);
```

 

### 实例：

下面的是视频读取和将视频内的画面转化为图片的代码，里面的注释应该足够解释了。

```c++
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <iostream>

using namespace std;
using namespace cv;

int main( )
{
	VideoCapture capture( "E:/Data/警戒线/@20180727105959_20180727115959_2502.avi" );	/*打开视频文件：其实就是建立一个VideoCapture结构*/
	
	if ( !capture.isOpened( ) )/*检测是否正常打开:成功打开时，isOpened返回ture*/
	{
		cout << "fail toopen!" << endl;
		return -1;
	}
	
	long totalFrameNumber = capture.get( CV_CAP_PROP_FRAME_COUNT );	/*获取视频帧数*/
	cout << "整个视频共" << totalFrameNumber << "帧" << endl;
	
	long frameToStart = 1;
	capture.set( CV_CAP_PROP_POS_FRAMES, frameToStart );/*设置开始帧*/
	cout << "从第" << frameToStart << "帧开始读" << endl;

	int frameToStop = 300;/*设置结束帧*/

	if ( frameToStop < frameToStart )
	{
		cout << "结束帧小于开始帧，程序错误，即将退出！" << endl;
		return -1;
	}
	else
	{
		cout << "结束帧为：第" << frameToStop << "帧" << endl;
	}
	
	double rate = capture.get( CV_CAP_PROP_FPS );/*获取帧率*/
	cout << "帧率为:" << rate << endl;


	Mat frame;/*承载每一帧的图像*/
	bool stop = false;	/*定义一个用来控制读取视频循环结束的变量*/
	double delay = 1000 / rate;/*两帧间的间隔时间:waitKey(intdelay=0)当delay≤ 0时会永远等待；当delay>0时会等待delay毫秒*/
	long currentFrame = frameToStart;/*currentFrame是在循环体中控制读取到指定的帧后循环结束的变量*/


	//利用while循环读取帧
	while ( !stop )
	{
		//读取下一帧
		if ( !capture.read( frame ) )
		{
			cout << "读取视频失败" << endl;
			return -1;
		}
		
		//cout << "正在读取第" << currentFrame << "帧" << endl;
		//namedWindow( "Extractedframe" );
		//imshow( "Extractedframe", frame );

		// 每隔50帧写一次图片
		if ( currentFrame % 50 == 0 )
		{
			cout << "正在写第" << currentFrame << "帧" << endl;
			stringstream str;
			str << "G:/test_pic/" << currentFrame << ".png";/*图片存储位置*/
			cout << str.str( ) << endl;
			imwrite( str.str( ), frame );
		}
		
		//当时间结束前没有按键按下时，返回值为-1；否则返回按键
		int c = waitKey( delay );
		if ( ( char )c == 27 || currentFrame > frameToStop )/*按下ESC或者到达指定的结束帧后退出读取视频*/
		{
			stop = true;
		}
		if ( c >= 0 )/*按下按键后会停留在当前帧，等待下一次按键*/
		{
			waitKey( 0 );
		}
		currentFrame++;

	}

	//关闭视频文件
	capture.release( );
	waitKey( 0 );
	return 0;
}
```

### 结果：

读取后的图片，因为图片很多，上面代码在设置的时候，只读取了300帧