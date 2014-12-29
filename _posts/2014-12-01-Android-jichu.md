---
layout : post
title : "Android的发展史"
category : Android基础
duoshuo: true
date : 2014-12-01
tags : [Android基础 , Android版本 , 体系结构 , 环境搭建]
---


##1G-4G的概念
		1. Generation 时代  
		2. wap : wait and pay  等待并且要付费  wap.baidu.com www.baidu.com
		3. LTE ： Long term evolution  长期演进的 
		4. 面试问题 ：  买手机型号 	 4G 支持的型号 我们要背下来  移动/联通  和 电信 不能混用 
		5. 最大的区别： 速度的区别 *  面试也可能会问
		6. 2G ： 12.2K/s
		7. 3G  ：  384K/s
		8. 3.9G ： 10m/s
		9. 4G  : 100M /s


##安卓的概念与历史

**面试：**

		1. 1.6 版本 甜甜圈
		2. 2.0 松饼
		3. 2.3 姜饼
		4. 4.0 冰淇淋三明治
		5. 4.1 果冻豆
		6. 扁平化 

##安卓体系结构（重要）

		1. Android是一个完整的操作系统，包含了中间件，同样的，包含了一些关键的应用程序
		2. Library： 函数库
		3. 如果觉得更高端一些，变成五层，Android Runtime 
		4. App Framework: 应用框架层 
		5. Android 有四层架构 ，第一层应用层，第二层，应用框架层，第三层，函数库，第四层，Linux内核

##DVM和JVM的区别
		1.  编译文件格式不同 JVM: .java  ---> .class  ----> .jar   DVM : .java ---->.class ----> .dex
		2.  基于架构不同 ：  JVM： 基于栈的架构 DVM： 基于寄存器的架构



##Android开发环境搭建

		1.ADT : Android development tools ： 安卓开发工具集  记一下
		2.SDK : Standard Devlope kits : 默认开发工具集 记一下
		3.Platform-tools ： 平台工具
		4.Extars ： 扩展
		5. Android support : 安卓的依赖包， 去支持其他的版本号的API


##创建虚拟机

		1. AVD　：　Android Virural Device 安卓虚拟设备
		2. VGA ： 640*480  标准的屏幕大小 （面试）
		3. HVGA : 320*480  一般的VGA
		4. QVGA ： 320*240  4分之一的VGA 
		5. Genymotion ： 模拟器 
		6. 默认情况下，Android会给每个应用的内存堆，分配16M
##DDMS
		1. DDMS： Device Definition monitor Service : 设备信息监听服务
		2. File explore ：   文件管理器 ， 
		3. DATA/app   装三方应用的。
		4. data/data/包名   区分应用程序的唯一标志

##SDK目录简介

		1. DOCS： 没事儿应该多看看
		2. ADB : Android  Debug Brige 安卓调试桥
		3. Samples: 里面有模拟器代码，



##创建HelloWorld
		1. adb kill-server : 杀死ADB服务
		2. adb start-server : 开启ADB服务


##Android目录结构
		1. RES目录下 ：drawble目录，用来存放图片资源，Layout目录，放置布局的，menu，防止设置界面布局，Values目录，防止字符串，用来做国际化
		2. 屏幕适配，1套图。
		3. R文件，可以当作一个词典，通过R文件，会把我们存入的资源文件等，转化成电脑看的懂的16进制语言。

##Android打包过程
		1. 签名： 对你的文件打了一个唯一的标识码，任何人除了你自己，是无法更改这个程序的。
		2. APK ： Android package

##ADB常用指令（练习一下）


    	1. adb kill-server 杀死adb服务  (练习一下)
		2. adb start-server 开启adb服务  (练习一下)
		3. adb devices 显示已经连接的设备 (练习一下)
		4. adb install xxx.apk   安装一个APK到设备上
		5. adb connect 192.168.1.12  （在同一网段下）
		6. adb shell 挂载到linux空间下。
		7. adb pull  : 把文件放到指定文件夹里(练习一下)
		8. adb push : 把指定文件放到模拟器里。(练习一下)


##开发电话拨号器 （练习一下）

		1. 了解需求
		2. 编写布局（xml）
		3. 编写逻辑
		4. www.baidu.com  http://www.baidu.com   tel:+phone
		5. cause by : 
		
##四种点击事件（重要）

	1. 作业，无论效果是否成功，点击事件都写出来

##常用的布局和单位的简介（重要，作业）

		1.  Layout_width ： 布局宽度 ， warp_content , match_parent == fill_parent(注意：填充的是父窗体)
		2. Layout_Height: 布局高度
		3. Layout_gravity: 布局的位置
		4. android:orientation="vertical"  垂直摆列
		5. android:orientation="horizontal"  水平摆列
		6. 相对布局：
		可以以，值为true/false，的一种属性：
	 	 android:layout_centerInParent="true" 在父窗体的中间

      	android:layout_centerHorizontal="true"  在父窗体水平居中
		android:layout_centerVertical="true"   在父窗体内垂直居中


		可以以，值为300dip等，为值的一种属性：

		 android:layout_marginLeft="50dip"
        android:layout_marginRight="50dip"
        android:layout_marginTop="50dip"
        android:layout_marginBottom="50dip"
	
		距离上下左右的大小


		可以以，值为控件ID的，为值的一种属性：
		android:layout_above="@id/center"     在指定空间的上方， 注意ID 
		android:layout_below="@id/center"     在指定控件的下方
		android:layout_toRightOf="@id/center"  在指定控件的右方
		android:layout_toLeftOf="@id/center"  在指定控件的左方
		android:layout_alignLeft="@id/left"  和指定空间左对其
		android:layout_alignRight="@id/right"  与指定控件右对齐	

		android:layout_alignParentLeft="true"  
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"

		和父窗体上下左右对其



##单位的概念
		1. DIP ： 概念， === DP    无论使用多大的屏幕 都会按比例的去放置
		2. PX  :  像素            无论使用多大的屏幕，都会按他自己的像素值去摆放