---
layout : post
title : "ImageLoader的基本使用"
category : Android开源框架
duoshuo: true
date : 2014-08-20
tags : [ImageLoader, 开源框架, OOM,基本使用]
---
##ImageLoader的基本使用

**特征**

> 1. 多线程下载图片，图片可以来源于网络，文件系统，项目文件夹assets中以及drawable中等
> 2. 支持随意的配置ImageLoader，例如线程池，图片下载器，内存缓存策略，硬盘缓存策略，图片显示选项以及其他的一些配置
> 3. 支持图片的内存缓存，文件系统缓存或者SD卡缓存
> 4. 支持图片下载过程的监听
> 5. 根据控件(ImageView)的大小对Bitmap进行裁剪，减少Bitmap占用过多的内存
> 6. 较好的控制图片的加载过程，例如暂停图片加载，重新开始加载图片，一般使用在ListView,GridView中，滑动过程中暂停加载图片，停止滑动的时候去加载图片
> 7. 提供在较慢的网络下对图片进行加载

**项目地址**

	https://github.com/nostra13/Android-Universal-Image-Loader	

**简单使用**

	新建一个Android项目，下载JAR包添加到工程libs目录下
	新建一个MyApplication继承Application，并在onCreate()中创建ImageLoader的配置参数，并初始化到ImageLoader中代码如下:

{% highlight java %}
package com.example.uil;
import com.nostra13.universalimageloader.core.ImageLoader;
import com.nostra13.universalimageloader.core.ImageLoaderConfiguration;
	
import android.app.Application;
	
public class MyApplication extends Application {
	@Override
	public void onCreate() {
		super.onCreate();

		//创建默认的ImageLoader配置参数
		ImageLoaderConfiguration configuration = ImageLoaderConfiguration
				.createDefault(this);
		
		//Initialize ImageLoader with configuration.
		ImageLoader.getInstance().init(configuration);
	}
}
{% endhighlight %}

---

ImageLoaderConfiguration是图片加载器ImageLoader的配置参数，使用了建造者模式，这里是直接使用了createDefault()方法创建一个默认的ImageLoaderConfiguration，当然我们还可以自己设置ImageLoaderConfiguration，设置如下

{% highlight java %}
File cacheDir = StorageUtils.getCacheDirectory(context);
ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(context)
        .memoryCacheExtraOptions(480, 800) // default = device screen dimensions
        .diskCacheExtraOptions(480, 800, CompressFormat.JPEG, 75, null)
        .taskExecutor(...)
        .taskExecutorForCachedImages(...)
        .threadPoolSize(3) // default
        .threadPriority(Thread.NORM_PRIORITY - 1) // default
        .tasksProcessingOrder(QueueProcessingType.FIFO) // default
        .denyCacheImageMultipleSizesInMemory()
        .memoryCache(new LruMemoryCache(2 * 1024 * 1024))
        .memoryCacheSize(2 * 1024 * 1024)
        .memoryCacheSizePercentage(13) // default
        .diskCache(new UnlimitedDiscCache(cacheDir)) // default
        .diskCacheSize(50 * 1024 * 1024)
        .diskCacheFileCount(100)
        .diskCacheFileNameGenerator(new HashCodeFileNameGenerator()) // default
        .imageDownloader(new BaseImageDownloader(context)) // default
        .imageDecoder(new BaseImageDecoder()) // default
        .defaultDisplayImageOptions(DisplayImageOptions.createSimple()) // default
        .writeDebugLogs()
        .build();
{% endhighlight %}

---

上面的这些就是所有的选项配置，我们在项目中不需要每一个都自己设置，一般使用createDefault()创建的ImageLoaderConfiguration就能使用，然后调用ImageLoader的init（）方法将ImageLoaderConfiguration参数传递进去，ImageLoader使用单例模式。

**配置Android Manifest文件**

{% highlight java %}
<manifest>
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- Include next permission if you want to allow UIL to cache images on SD card -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    ...
    <application android:name="MyApplication">
        ...
    </application>
</manifest>
{% endhighlight %}

---

接下来我们就可以来加载图片了，首先我们定义好Activity的布局文件
{% highlight java %}
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <ImageView
        android:layout_gravity="center"
        android:id="@+id/image"
        android:src="@drawable/ic_empty"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</FrameLayout>
{% endhighlight %}

---

里面只有一个ImageView，很简单，接下来我们就去加载图片，我们会发现ImageLader提供了几个图片加载的方法，主要是这几个displayImage(), loadImage(),loadImageSync()，loadImageSync()方法是同步的，android4.0有个特性，网络操作不能在主线程，所以loadImageSync()方法我们就不去使用

**loadimage()加载图片**

我们先使用ImageLoader的loadImage()方法来加载网络图片

{% highlight java %}
final ImageView mImageView = (ImageView) findViewById(R.id.image);
		String imageUrl = "https://lh6.googleusercontent.com/-55osAWw3x0Q/URquUtcFr5I/AAAAAAAAAbs/rWlj1RUKrYI/s1024/A%252520Photographer.jpg";
		ImageLoader.getInstance().loadImage(imageUrl, new ImageLoadingListener() {
			
			@Override
			public void onLoadingStarted(String imageUri, View view) {
			}
			
			@Override
			public void onLoadingFailed(String imageUri, View view,
					FailReason failReason) {
			}
			
			@Override
			public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
				mImageView.setImageBitmap(loadedImage);
			}
			
			@Override
			public void onLoadingCancelled(String imageUri, View view) {
				
			}
		});
{% endhighlight %}

---

传入图片的url和ImageLoaderListener, 在回调方法onLoadingComplete()中将loadedImage设置到ImageView上面就行了，如果你觉得传入ImageLoaderListener太复杂了，我们可以使用SimpleImageLoadingListener类，该类提供了ImageLoaderListener接口方法的空实现，使用的是缺省适配器模式


{% highlight java %}
final ImageView mImageView = (ImageView) findViewById(R.id.image);
		String imageUrl = "https://lh6.googleusercontent.com/-55osAWw3x0Q/URquUtcFr5I/AAAAAAAAAbs/rWlj1RUKrYI/s1024/A%252520Photographer.jpg";
		
		ImageLoader.getInstance().loadImage(imageUrl, new SimpleImageLoadingListener(){

			@Override
			public void onLoadingComplete(String imageUri, View view,
					Bitmap loadedImage) {
				super.onLoadingComplete(imageUri, view, loadedImage);
				mImageView.setImageBitmap(loadedImage);
			}
			
		});
{% endhighlight %}

---

如果我们要指定图片的大小该怎么办呢，这也好办，初始化一个ImageSize对象，指定图片的宽和高，代码如下

{% highlight java %}
final ImageView mImageView = (ImageView) findViewById(R.id.image);
		String imageUrl = "https://lh6.googleusercontent.com/-55osAWw3x0Q/URquUtcFr5I/AAAAAAAAAbs/rWlj1RUKrYI/s1024/A%252520Photographer.jpg";
		
		ImageSize mImageSize = new ImageSize(100, 100);
		
		ImageLoader.getInstance().loadImage(imageUrl, mImageSize, new SimpleImageLoadingListener(){

			@Override
			public void onLoadingComplete(String imageUri, View view,
					Bitmap loadedImage) {
				super.onLoadingComplete(imageUri, view, loadedImage);
				mImageView.setImageBitmap(loadedImage);
			}
			
		});
{% endhighlight %}

---

上面只是很简单的使用ImageLoader来加载网络图片，在实际的开发中，我们并不会这么使用，那我们平常会怎么使用呢？我们会用到DisplayImageOptions，他可以配置一些图片显示的选项，比如图片在加载中ImageView显示的图片，是否需要使用内存缓存，是否需要使用文件缓存等等，可供我们选择的配置如下

{% highlight java %}
DisplayImageOptions options = new DisplayImageOptions.Builder()
        .showImageOnLoading(R.drawable.ic_stub) // resource or drawable
        .showImageForEmptyUri(R.drawable.ic_empty) // resource or drawable
        .showImageOnFail(R.drawable.ic_error) // resource or drawable
        .resetViewBeforeLoading(false)  // default
        .delayBeforeLoading(1000)
        .cacheInMemory(false) // default
        .cacheOnDisk(false) // default
        .preProcessor(...)
        .postProcessor(...)
        .extraForDownloader(...)
        .considerExifParams(false) // default
        .imageScaleType(ImageScaleType.IN_SAMPLE_POWER_OF_2) // default
        .bitmapConfig(Bitmap.Config.ARGB_8888) // default
        .decodingOptions(...)
        .displayer(new SimpleBitmapDisplayer()) // default
        .handler(new Handler()) // default
        .build();
{% endhighlight %}

---


我们将上面的代码稍微修改下

{% highlight java %}
final ImageView mImageView = (ImageView) findViewById(R.id.image);
		String imageUrl = "https://lh6.googleusercontent.com/-55osAWw3x0Q/URquUtcFr5I/AAAAAAAAAbs/rWlj1RUKrYI/s1024/A%252520Photographer.jpg";
		ImageSize mImageSize = new ImageSize(100, 100);
		
		//显示图片的配置
		DisplayImageOptions options = new DisplayImageOptions.Builder()
				.cacheInMemory(true)
				.cacheOnDisk(true)
				.bitmapConfig(Bitmap.Config.RGB_565)
				.build();
		
		ImageLoader.getInstance().loadImage(imageUrl, mImageSize, options, new SimpleImageLoadingListener(){

			@Override
			public void onLoadingComplete(String imageUri, View view,
					Bitmap loadedImage) {
				super.onLoadingComplete(imageUri, view, loadedImage);
				mImageView.setImageBitmap(loadedImage);
			}
			
		});
{% endhighlight %}

---

我们使用了DisplayImageOptions来配置显示图片的一些选项，这里我添加了将图片缓存到内存中已经缓存图片到文件系统中，这样我们就不用担心每次都从网络中去加载图片了，是不是很方便呢，但是DisplayImageOptions选项中有些选项对于loadImage()方法是无效的，比如showImageOnLoading, showImageForEmptyUri等，

**displayImage()加载图片**

接下来我们就来看看网络图片加载的另一个方法displayImage()，代码如下:

{% highlight java %}
final ImageView mImageView = (ImageView) findViewById(R.id.image);
		String imageUrl = "https://lh6.googleusercontent.com/-55osAWw3x0Q/URquUtcFr5I/AAAAAAAAAbs/rWlj1RUKrYI/s1024/A%252520Photographer.jpg";
		
		//显示图片的配置
		DisplayImageOptions options = new DisplayImageOptions.Builder()
				.showImageOnLoading(R.drawable.ic_stub)
				.showImageOnFail(R.drawable.ic_error)
				.cacheInMemory(true)
				.cacheOnDisk(true)
				.bitmapConfig(Bitmap.Config.RGB_565)
				.build();
		
		ImageLoader.getInstance().displayImage(imageUrl, mImageView, options);
{% endhighlight %}

---

从上面的代码中，我们可以看出，使用displayImage()比使用loadImage()方便很多，也不需要添加ImageLoadingListener接口，我们也不需要手动设置ImageView显示Bitmap对象，直接将ImageView作为参数传递到displayImage()中就行了，图片显示的配置选项中，我们添加了一个图片加载中ImageVIew上面显示的图片，以及图片加载出现错误显示的图片，效果如下，刚开始显示ic_stub图片，如果图片加载成功显示图片，加载产生错误显示ic_error

![图片链接](/res/img/blog/2014/12/1.gif)
![图片链接](/res/img/blog/2014/12/2.gif)

这个方法使用起来比较方便，而且使用displayImage()方法 他会根据控件的大小和imageScaleType来自动裁剪图片，我们修改下MyApplication，开启Log打印

{% highlight java %}
public class MyApplication extends Application {

	@Override
	public void onCreate() {
		super.onCreate();

		//创建默认的ImageLoader配置参数
		ImageLoaderConfiguration configuration = new ImageLoaderConfiguration.Builder(this)
		.writeDebugLogs() //打印log信息
		.build();
		
		
		//Initialize ImageLoader with configuration.
		ImageLoader.getInstance().init(configuration);
	}

}
{% endhighlight %}

---

我们来看下图片加载的Log信息

![图片链接](/res/img/blog/2014/12/3.jpg)<center>

	第一条信息中，告诉我们开始加载图片，打印出图片的url以及图片的最大宽度和高度，图片的宽高默认是设备的宽高，当然如果我们很清楚图片的大小，我们也可以去设置这个大小，在ImageLoaderConfiguration的选项中memoryCacheExtraOptions(int maxImageWidthForMemoryCache, int maxImageHeightForMemoryCache)
	
	第二条信息显示我们加载的图片来源于网络
	
	第三条信息显示图片的原始大小为1024 x 682 经过裁剪变成了512 x 341 
	
	第四条显示图片加入到了内存缓存中，我这里没有加入到sd卡中，所以没有加入文件缓存的Log
	
	我们在加载网络图片的时候，经常有需要显示图片下载进度的需求，Universal-Image-Loader当然也提供这样的功能，只需要在displayImage()方法中传入ImageLoadingProgressListener接口就行了，代码如下

{% highlight java %}
imageLoader.displayImage(imageUrl, mImageView, options, new SimpleImageLoadingListener(), new ImageLoadingProgressListener() {
			
			@Override
			public void onProgressUpdate(String imageUri, View view, int current,
					int total) {
				
			}
		});
{% endhighlight %}

---

由于displayImage()方法中带ImageLoadingProgressListener参数的方法都有带ImageLoadingListener参数，所以我这里直接new 一个SimpleImageLoadingListener，然后我们就可以在回调方法onProgressUpdate()得到图片的加载进度。

**加载其他来源的图片**

使用Universal-Image-Loader框架不仅可以加载网络图片，还可以加载sd卡中的图片，Content provider等，使用也很简单，只是将图片的url稍加的改变下就行了，下面是加载文件系统的图片

{% highlight java %}
//显示图片的配置
		DisplayImageOptions options = new DisplayImageOptions.Builder()
				.showImageOnLoading(R.drawable.ic_stub)
				.showImageOnFail(R.drawable.ic_error)
				.cacheInMemory(true)
				.cacheOnDisk(true)
				.bitmapConfig(Bitmap.Config.RGB_565)
				.build();
		
		final ImageView mImageView = (ImageView) findViewById(R.id.image);
		String imagePath = "/mnt/sdcard/image.png";
		String imageUrl = Scheme.FILE.wrap(imagePath);
		
//		String imageUrl = "http://img.my.csdn.net/uploads/201309/01/1378037235_7476.jpg";
		
		imageLoader.displayImage(imageUrl, mImageView, options);
{% endhighlight %}

---

当然还有来源于Content provider,drawable,assets中，使用的时候也很简单，我们只需要给每个图片来源的地方加上Scheme包裹起来(Content provider除外)，然后当做图片的url传递到imageLoader中，Universal-Image-Loader框架会根据不同的Scheme获取到输入流

{% highlight java %}
//图片来源于Content provider
		String contentprividerUrl = "content://media/external/audio/albumart/13";
		
		//图片来源于assets
		String assetsUrl = Scheme.ASSETS.wrap("image.png");
		
		//图片来源于
		String drawableUrl = Scheme.DRAWABLE.wrap("R.drawable.image");
{% endhighlight %}

---

**GirdView,ListView加载图片**

相信大部分人都是使用GridView，ListView来显示大量的图片，而当我们快速滑动GridView，ListView，我们希望能停止图片的加载，而在GridView，ListView停止滑动的时候加载当前界面的图片，这个框架当然也提供这个功能，使用起来也很简单，它提供了PauseOnScrollListener这个类来控制ListView,GridView滑动过程中停止去加载图片，该类使用的是代理模式

{% highlight java %}
listView.setOnScrollListener(new PauseOnScrollListener(imageLoader, pauseOnScroll, pauseOnFling));
gridView.setOnScrollListener(new PauseOnScrollListener(imageLoader, pauseOnScroll, pauseOnFling));
{% endhighlight %}

---

第一个参数就是我们的图片加载对象ImageLoader, 第二个是控制是否在滑动过程中暂停加载图片，如果需要暂停传true就行了，第三个参数控制猛的滑动界面的时候图片是否加载

**OutOfMemoryError**

虽然这个框架有很好的缓存机制，有效的避免了OOM的产生，一般的情况下产生OOM的概率比较小，但是并不能保证OutOfMemoryError永远不发生，这个框架对于OutOfMemoryError做了简单的catch,保证我们的程序遇到OOM而不被crash掉，但是如果我们使用该框架经常发生OOM，我们应该怎么去改善呢？
	
	•减少线程池中线程的个数，在ImageLoaderConfiguration中的（.threadPoolSize）中配置，推荐配置1-5
	•在DisplayImageOptions选项中配置bitmapConfig为Bitmap.Config.RGB_565，因为默认是ARGB_8888， 使用RGB_565会比使用ARGB_8888少消耗2倍的内存
	•在ImageLoaderConfiguration中配置图片的内存缓存为memoryCache(new WeakMemoryCache()) 或者不使用内存缓存
	•在DisplayImageOptions选项中设置.imageScaleType(ImageScaleType.IN_SAMPLE_INT)或者imageScaleType(ImageScaleType.EXACTLY)
	
通过上面这些，相信大家对Universal-Image-Loader框架的使用已经非常的了解了，我们在使用该框架的时候尽量的使用displayImage()方法去加载图片，loadImage()是将图片对象回调到ImageLoadingListener接口的onLoadingComplete()方法中，需要我们手动去设置到ImageView上面，displayImage()方法中，对ImageView对象使用的是Weak references，方便垃圾回收器回收ImageView对象，如果我们要加载固定大小的图片的时候，使用loadImage()方法需要传递一个ImageSize对象，而displayImage()方法会根据ImageView对象的测量值，或者android:layout_width and android:layout_height设定的值，或者android:maxWidth and/or android:maxHeight设定的值来裁剪图片

