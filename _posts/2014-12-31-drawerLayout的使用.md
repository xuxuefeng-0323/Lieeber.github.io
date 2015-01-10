---
layout : post
title : "DrawerLayout的使用"
category : Android开源框架
duoshuo: true
date : 2014-08-15
tags : [DrawerLayout, 开源框架,github]
---

**基本介绍**

> 1. 侧拉菜单作为常见的导航交互控件，最开始在没有没有android官方控件时，很多时候都是使用开源的SlidingMenu.
> 2. DrawerLayout直译为抽屉布局的意思，作为视窗内的顶层容器，它允许用户通过抽屉式的推拉操作，从而把视图视窗外边缘拉到屏幕内.
> 3. DrawerLayout这个类是在Support Library里的，使用时需要加上android-support-v4.jar这个包。
> 4. drawerLayout分为侧边菜单和主内容区两部分，侧边菜单可以根据手势展开与隐藏（drawerLayout自身特性），主内容区的内容可以随着菜单的点击而变化（自己实现）。

<!-- more -->

**创建抽屉布局**

> 1. 主内容视图一定要是DrawerLayout的第一个子视图
> 2. 主内容视图宽度和高度匹配父视图，即“match_parent”
> 3. 必须显示指定抽屉视图（如ListView）的 android:layout_gravity 属性    
	1）、 android:layout_gravity=“start”时，从左向右滑出菜单    
 	2）、 android:layout_gravity=“end”  时，从右向左滑出菜单    
	3）、不推荐使用 “left”和“right”    
> 4. 抽屉视图的宽度以dp为单位，请不要超过320dp（为了总能看到一些主内容视图）

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

> 1. 通过listView来填充侧拉栏页面,点击每个条目时,通过Fragment来填充主页面的FrameLayout,并且侧拉栏要消失

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

> 1. 左侧侧拉栏的布局

{% highlight java %}
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

> 1. mDrawerLayout.setDrawerListener(DrawerLayout.DrawerListener);
> 2. ActionBarDrawerToggle是DrawerLayout.DrawerListener的具体实现类    
	1）、改变android.R.id.home图标(构造方法)    
	2）、Drawer拉出、隐藏，带有android.R.id.home动画效果(syncState())    
	3）、监听Drawer拉出、隐藏事件    
> 3. 覆写ActionBarDrawerToggle的onDrawerOpened()和onDrawerClosed()以监听抽屉拉出或隐藏事件
> 4. 覆写Activity的onPostCreate()和onConfigurationChanged()方法

{% highlight java %}
public class MainActivity extends Activity implements OnItemClickListener {

	private DrawerLayout mDrawerLayout;
	private ListView mDrawerList;
	private ArrayList<String> menuLists;
	private ArrayAdapter<String> adapter;
	private ActionBarDrawerToggle mDrawerToggle;
	private String mTitle;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		mTitle = (String) getTitle();

		mDrawerLayout = (DrawerLayout) findViewById(R.id.drawer_layout);
		mDrawerList = (ListView) findViewById(R.id.left_drawer);
		menuLists = new ArrayList<String>();
		for (int i = 0; i < 5; i++)
			menuLists.add("左侧滑栏" + i);
		adapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, menuLists);
		mDrawerList.setAdapter(adapter);
		mDrawerList.setOnItemClickListener(this);

		mDrawerToggle = new ActionBarDrawerToggle(this, mDrawerLayout,
				R.drawable.ic_drawer, R.string.drawer_open,
				R.string.drawer_close) {
			@Override
			public void onDrawerOpened(View drawerView) {
				super.onDrawerOpened(drawerView);
				getActionBar().setTitle("请选择");
				invalidateOptionsMenu(); // Call onPrepareOptionsMenu()
			}

			@Override
			public void onDrawerClosed(View drawerView) {
				super.onDrawerClosed(drawerView);
				getActionBar().setTitle(mTitle);
				invalidateOptionsMenu();
			}
		};
		mDrawerLayout.setDrawerListener(mDrawerToggle);
		
		//开启ActionBar上APP ICON的功能
		getActionBar().setDisplayHomeAsUpEnabled(true);
		getActionBar().setHomeButtonEnabled(true);

	}

	@Override
	public boolean onPrepareOptionsMenu(Menu menu) {
		boolean isDrawerOpen = mDrawerLayout.isDrawerOpen(mDrawerList);
		menu.findItem(R.id.action_websearch).setVisible(!isDrawerOpen);
		return super.onPrepareOptionsMenu(menu);
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}

	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		//将ActionBar上的图标与Drawer结合起来
		if (mDrawerToggle.onOptionsItemSelected(item)){
			return true;
		}
		switch (item.getItemId()) {
		case R.id.action_websearch:
			Intent intent = new Intent();
			intent.setAction("android.intent.action.VIEW");
			Uri uri = Uri.parse("http://www.baidu.com");
			intent.setData(uri);
			startActivity(intent);
			break;
		}
		return super.onOptionsItemSelected(item);
	}
	
	@Override
	protected void onPostCreate(Bundle savedInstanceState) {
		super.onPostCreate(savedInstanceState);
		//需要将ActionDrawerToggle与DrawerLayout的状态同步
		//将ActionBarDrawerToggle中的drawer图标，设置为ActionBar中的Home-Button的Icon
		mDrawerToggle.syncState();
	}
	
	@Override
	public void onConfigurationChanged(Configuration newConfig) {
		super.onConfigurationChanged(newConfig);
		mDrawerToggle.onConfigurationChanged(newConfig);
	}

	@Override
	public void onItemClick(AdapterView<?> arg0, View arg1, int position,
			long arg3) {
		// 动态插入一个Fragment到FrameLayout当中
		Fragment contentFragment = new ContentFragment();
		Bundle args = new Bundle();
		args.putString("text", menuLists.get(position));
		contentFragment.setArguments(args);

		FragmentManager fm = getFragmentManager();
		fm.beginTransaction().replace(R.id.content_frame, contentFragment).commit();

		mDrawerLayout.closeDrawer(mDrawerList);
	}

}
{% endhighlight %}

---

{% highlight java %}
<menu xmlns:android="http://schemas.android.com/apk/res/android" >

    <item
        android:id="@+id/action_websearch"
        android:icon="@drawable/action_search"
        android:showAsAction="ifRoom|withText"
        android:title="webSearch"/>
</menu>
{% endhighlight %}

---