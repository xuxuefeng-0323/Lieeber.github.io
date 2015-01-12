---
layout : post
title : "PreferenceActivity的使用"
category : AndroidUI设计
duoshuo: true
date : 2014-11-23
tags : [PreferenceActivity, Preference]
---

**简单介绍**

今天要介绍的是一中比较神奇的activity，不过这种activity已经不常用了，在api11之后就已经过时,但是他能够自动保存我们activity退出之前的数据，下次进入页面时，页面上的控件依旧为退出时候的状态，比如一个checkBox,当前为选中状态，则退出后下次进入依旧是选中状态。还有一点不不同的是,PreferenceActivity的页面布局不是通过setContentView()来加载的，而是通过addPreferencesFromResource(R.xml.pref_general_activity)方法，可以看到，该方法加载的布局也不是写在layout目录下，而是在根目录下面建立一个目录名为xml的文件夹,然后创建一个布局文件。下面上代码。

<!-- more -->

首先是MainActivity,

{% highlight java %}
public class MainActivity extends PreferenceActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //   setContentView(R.layout.activity_main);
        addPreferencesFromResource(R.xml.pref_general_activity);

        Preference clickPreference = findPreference("click");
        if (clickPreference != null) {
            clickPreference.setOnPreferenceClickListener(new Preference.OnPreferenceClickListener() {
                @Override
                public boolean onPreferenceClick(Preference preference) {
                    Toast.makeText(MainActivity.this, "click this button", 0).show();
                    return false;
                }
            });
        }

        final CheckBoxPreference firstCheckBox = (CheckBoxPreference) findPreference("firstCheckBox");
        if(firstCheckBox != null){
            firstCheckBox.setOnPreferenceClickListener(new Preference.OnPreferenceClickListener() {
                @Override
                public boolean onPreferenceClick(Preference preference) {
                    if(firstCheckBox.isChecked()){
                        Toast.makeText(MainActivity.this, "isChecked", 0).show();
                    }else{
                        Toast.makeText(MainActivity.this, "isUnChecked", 0).show();
                    }
                    return false;
                }
            });
        }

        CheckBoxPreference secondCheckBox = (CheckBoxPreference) findPreference("secondCheckBox");
        
        ListPreference listPreference = (ListPreference) findPreference("listPreference");

        listPreference.setOnPreferenceChangeListener(new Preference.OnPreferenceChangeListener() {
            @Override
            public boolean onPreferenceChange(Preference preference, Object newValue) {
                Toast.makeText(MainActivity.this, newValue + " isChecked", 0).show();
                return false;
            }
        });

    }
}
{% endhighlight %}

---

布局文件为,在activity中通过控件指定的key来获得相应的控件,ListPreference是一个单选列表Dialog,CheckBoxPreference是一个CheckBox,Preference能够实现按钮的效果,这些控件都能够在activity中添加相应的事件监听,来完成对应的功能。


{% highlight java %}
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android" >

    <ListPreference
            android:defaultValue="one"
            android:dialogTitle="show list"
            android:entries="@array/pref_list_titles"
            android:entryValues="@array/pref_list_values"
            android:key="listPreference"
            android:title="click to show list"/>
    
    <CheckBoxPreference
        android:defaultValue="true"
        android:key="firstCheckBox"
        android:title="first CheckBox" />

    <CheckBoxPreference
        android:defaultValue="false"
        android:key="secondCheckBox"
        android:title="second CheckBox" />
    
    <Preference 
        android:title="click"
        android:key="click"/>

</PreferenceScreen>
{% endhighlight %}

---

项目的效果图为:

![图片链接](/res/img/blog/2014/11/23/cc.gif)
