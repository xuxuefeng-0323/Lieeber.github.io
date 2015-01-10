---
layout : post
title : "在Android studio上检出eclipse目录结构代码"
category : 程序员技能
duoshuo: true
date : 2014-12-31
tags : [eclipse, Android studio, svn]
---

在android studio中导入eclipse生成的项目是一件比较轻松的事情,直接import Module或者import project就可以做到,但是怎么才能检出服务器端eclipse生成的项目且能够使用android studio编译,并且能够commit到svn上不把原有服务器上的代码给打乱,就稍微要复杂一点了.这里面的应用场景就是公司一直用的eclipse,但是自己却想用android studio来工作该怎么办,下面是我的解决办法:

<!-- more -->

**svn上检出代码**
	
安装svn插件什么的就不说了,网上资料都很详尽.由于只是演示,我首先用eclipse创建了一个工程,然后用visualSVN Server在本地创建了一个仓库,再把eclipse中创建的工程分享到了仓库中.接下来要做的就是从android Studio中将项目检出

记住提交的时候一定要把不需要的提交的文件夹给ignore,比如bin目录,gen目录和.project文件

![图片链接](/res/img/blog/2014/12/31/1.png)

从仓库中检出你刚刚放上去的代码

![图片链接](/res/img/blog/2014/12/31/2.png)
![图片链接](/res/img/blog/2014/12/31/3.png)
![图片链接](/res/img/blog/2014/12/31/4.png)

然后让你选择是否创建一个android studio的项目,选择是

![图片链接](/res/img/blog/2014/12/31/5.png)

选择打开的模式,另外开启一个新窗口还是覆盖当前窗口

![图片链接](/res/img/blog/2014/12/31/6.png)

程序就会自动帮你检出了,检出之后可能你什么都看不到,你需要把试图切换到project模式

![图片链接](/res/img/blog/2014/12/31/8.png)

接下来是非常重要的一步,需要自己去创建一个build,gradle,我们就在根目录下面创建一个gradle

![图片链接](/res/img/blog/2014/12/31/9.png)

创建了gradle之后,需要自己手动配置gradle,下面是配置在gradle中的代码

{% highlight java %}
//声明项目是一个android构建
apply plugin: 'android'

buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.0.0'
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
}
android {
    compileSdkVersion 19
    buildToolsVersion "21.1.2"

    sourceSets {
        main {
            manifest.srcFile 'AndroidManifest.xml'
            java.srcDirs = ['src']

            resources.srcDirs = ['src']
            aidl.srcDirs = ['src']

            renderscript.srcDirs = ['src']

            res.srcDirs = ['res']
            assets.srcDirs = ['assets']
        }
        instrumentTest.setRoot('tests')
    }

    defaultConfig {
        minSdkVersion 9
        targetSdkVersion 19
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    tasks.withType(JavaCompile) { options.encoding = "UTF-8" }
}
{% endhighlight %}

---

上面的代码就是gradle里面的内容,与android studio生成的差别不是太大,主要就是sourceSets用来设置项目的目录结构,还有一点不同时只有一个gradle文件,而在android studio默认生成的项目是有两个gradle的,一个用来build project,还一个用来build你的项目.

把gradle构建配置好之后,点击一键检查要是不报错的话就可以了

![图片链接](/res/img/blog/2014/12/31/10.png)

生成的目录结构就是这样的, 和eclipse一模一样

![图片链接](/res/img/blog/2014/12/31/11.png)

至此,你已经成功将在android studio中建立了eclipse目录结构的项目,还有一点需要注意,因为是用gradle来构建的项目,所以除了需要提交的代码外,其他目录或文件一定要记得ignore,不然会commit到服务器.


