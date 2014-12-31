---
layout : post
title : "DrawerLayout的使用"
category :Android源码分析
duoshuo: true
date : 2014-12-12
tags : [Android基础 , Android版本 , 体系结构 , 环境搭建]
---

**基本介绍**

> 1.侧拉菜单作为常见的导航交互控件，最开始在没有没有android官方控件时，很多时候都是使用开源的SlidingMenu.

> 1.DrawerLayout直译为抽屉布局的意思，作为视窗内的顶层容器，它允许用户通过抽屉式的推拉操作，从而把视图视窗外边缘拉到屏幕内.

> 1.DrawerLayout这个类是在Support Library里的，使用时需要加上android-support-v4.jar这个包。

> 1.drawerLayout分为侧边菜单和主内容区两部分，侧边菜单可以根据手势展开与隐藏（drawerLayout自身特性），主内容区的内容可以随着菜单的点击而变化（自己实现）。

<!--more-->

**创建抽屉布局**

> 1.主内容视图一定要是DrawerLayout的第一个子视图
> 1.主内容视图宽度和高度匹配父视图，即“match_parent”
> 1.必须显示指定抽屉视图（如ListView）的 android:layout_gravity 属性
>
	1）、 android:layout_gravity=“start”时，从左向右滑出菜单
 	2）、 android:layout_gravity=“end”  时，从右向左滑出菜单
	3）、不推荐使用 “left”和“right”

> 1.抽屉视图的宽度以dp为单位，请不要超过320dp（为了总能看到一些主内容视图）

{% highlight java %}
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/drawer_layout"
    tools:context=".MainActivity">

    <FrameLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/content" />

   <!-- android:layout_gravity="start"从左向右滑出菜单
         android:layout_gravity="start"从右向左滑出菜单
   -->
    <ListView
        android:layout_width="200dp"
        android:layout_height="fill_parent"
        android:id="@+id/list_menu"
        android:choiceMode="singleChoice"
        android:divider="@android:color/transparent"
        android:dividerHeight="0dp"
        android:layout_gravity="start"
        android:background="#ffffcc" />
</android.support.v4.widget.DrawerLayout>
{% endhighlight %}

---

**初始化导航列表**

> 1.通过listView来填充侧拉栏页面,点击每个条目时,通过Fragment来填充主页面的FrameLayout,并且侧拉栏要消失

{% highlight java %}
public class MainActivity extends Activity implements AdapterView.OnItemClickListener {
    private ListView list_menu;
    private DrawerLayout drawer_layout;
    private ArrayAdapter adapter;
    private List<String> mList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        list_menu = (ListView) findViewById(R.id.list_menu);
        drawer_layout = (DrawerLayout) findViewById(R.id.drawer_layout);
        mList = new ArrayList<>();

        for (int i = 0; i < 5; i++)
            mList.add("左侧条目栏" + i);
        adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, mList);
        list_menu.setAdapter(adapter);
        list_menu.setOnItemClickListener(this);
    }

    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        //点击条目,跳转到相应的内容页面
        // 动态插入一个Fragment到FrameLayout当中
        Fragment contentFragment = new ContentFragment();
        Bundle args = new Bundle();
        args.putString("text", mList.get(position));
        contentFragment.setArguments(args);

        FragmentManager fm = getFragmentManager();
        fm.beginTransaction().replace(R.id.content, contentFragment).commit();
        //关闭 做侧拉栏
        drawer_layout.closeDrawer(list_menu);
    }
}
{% endhighlight %}

---

> 1.左侧侧拉栏的布局

{% hightlight java %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="25sp" />

</LinearLayout>
{% endhighlight %}

---

**监听抽屉的打开关闭事件**

> 1、mDrawerLayout.setDrawerListener(DrawerLayout.DrawerListener);
> 2、ActionBarDrawerToggle是DrawerLayout.DrawerListener的具体实现类
> 
	1）、改变android.R.id.home图标(构造方法)
	2）、Drawer拉出、隐藏，带有android.R.id.home动画效果(syncState())
	3）、监听Drawer拉出、隐藏事件

> 3、覆写ActionBarDrawerToggle的onDrawerOpened()和onDrawerClosed()以监       听抽屉拉出或隐藏事件
> 4、覆写Activity的onPostCreate()和onConfigurationChanged()方法

