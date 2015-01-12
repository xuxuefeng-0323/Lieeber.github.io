---
layout : post
title : "AsyncTask的基本使用"
category : Android基础巩固
duoshuo: true
date : 2014-11-16
tags : [AsyncTask, 异步处理]
---

**简单介绍**

AsyncTask是Android中轻量级的异步操作类，使用比较简单，直接用一个类继承至AsyncTask，然后实现实现相应的操作。

> 其中必须实现的方法有:doInBackground(),该方法是一个后台运行的方法，用来处理比较耗时的操作。

另外常用的方法有:
> onPostExecute(Result):相当于Handler 处理UI的方式，在这里面可以使用在doInBackground得到的结果处理操作UI。 此方法在主线程执行，任务执行的结果作为此方法的参数返回。
>
> onProgressUpdate(Progress…):可以使用进度条增加用户体验度。 此方法在主线程执行，用于显示任务执行的进度。
> 
> onPreExecute():这里是最终用户调用Excute时的接口，当任务执行之前开始调用此方法，可以在这里显示进度对话框。
> 
> onCancelled():用户调用取消时，要做的操作。
	
AsyncTask中比较容易弄混的就是三种泛型类型：Params，Progress和Result。

> Params:启动任务执行的输入参数，比如HTTP请求的URL。
> 
> Progress:后台任务执行的百分比。
> 
> Result:后台执行任务最终返回的结果，比如String。

<!-- more -->

下面是一个小案例

{% highlight java %}
public class MainActivity extends ActionBarActivity {

    private TextView textView;
    private ProgressBar progressBar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        textView = (TextView) findViewById(R.id.text_view);
        progressBar = (ProgressBar)findViewById(R.id.pb);
        MyAsyncTask myAsyncTask = new MyAsyncTask();
        //100对应的是泛型中的第一个参数
        myAsyncTask.execute(100);
    }


    private class MyAsyncTask extends AsyncTask<Integer, Integer, String> {
        @Override
        protected void onPreExecute() {
            textView.setText("开始执行异步线程");

        }

        /**
         * s对应于AsyncTask中的第三个参数,也就是doInBackground方法的返回值
         * @param s
         */
        @Override
        protected void onPostExecute(String s) {
            textView.setText("异步操作执行结束" + s);
        }

        /**
         * 这里的Integer对应AsyncTask中的第二个参数  
         * 在doInBackground方法当中，，每次调用publishProgress方法都会触发onProgressUpdate执行  
         */
        @Override
        protected void onProgressUpdate(Integer... values) {
            super.onProgressUpdate(values);
            int vlaue = values[0];
            progressBar.setProgress(vlaue);

        }

        /**
         * 这里的Integer参数对应AsyncTask中的第一个参数   
         * 这里的String返回值对应AsyncTask的第三个参数  
         */
        @Override
        protected String doInBackground(Integer... params) {
            int i = 0;
            BackgroundTask task = new BackgroundTask();
            for (; i < 100; i++) {
                task.operator();
                
                //该方法将参数传递给onProgressUpdate()方法
                publishProgress(i);
            }
             //该返回值传递给onPostExecute,即传递给主线程进行处理
            return i + params[0].intValue() + "";
        }
    }

    private class BackgroundTask {
        public void operator() {
            try {
                //休眠1秒  
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
{% endhighlight %}

---

布局文件为:

{% highlight java %}
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingLeft="@dimen/activity_horizontal_margin"
                android:paddingRight="@dimen/activity_horizontal_margin"
                android:paddingTop="@dimen/activity_vertical_margin"
                android:paddingBottom="@dimen/activity_vertical_margin"
                tools:context=".MainActivity">

    <TextView
        android:id="@+id/text_view"
        android:text="@string/hello_world"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
    <ProgressBar
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_below="@+id/text_view"
        android:layout_width="fill_parent"
        android:id="@+id/pb"
        android:layout_height="wrap_content"/>

</RelativeLayout>
{% endhighlight %}

---

效果图:

![图片链接](/res/img/blog/2014/11/16/cc.gif)

