---
layout : post
title : "SpannableString的使用"
category : AndroidUI设计
duoshuo: true
date : 2014-11-12
tags : [TextView, SpannableString]
---

**简单介绍**

今天因为有一个需求,需要给一行文字显示为不同的颜色,于是上网查,看到了SpannableString这个类,发现其非常强大,不仅可以给一个TextView中指定的文字设置不同的样式,而且还可以在TextView商密昂添加图片,因为代码比较简单,就不做过多的介绍了.

<!-- more -->

{% highlight java %}
public class MainActivity extends ActionBarActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
//        requestWindowFeature(Window.FEATURE_NO_TITLE);
        super.onCreate(savedInstanceState);
//        setContentView(R.layout.activity_main);
        TextView txtInfo = new TextView(this);
        SpannableString ss = new SpannableString("红色打电话斜体删除线绿色下划线图片:.");
        ss.setSpan(new ForegroundColorSpan(Color.RED), 0, 2,
                Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        ss.setSpan(new URLSpan("tel:4155551212"), 2, 5,
                Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        ss.setSpan(new StyleSpan(Typeface.BOLD_ITALIC), 5, 7,
                Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        ss.setSpan(new StrikethroughSpan(), 7, 10,
                Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        ss.setSpan(new UnderlineSpan(), 10, 16,
                Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        ss.setSpan(new ForegroundColorSpan(Color.GREEN), 10, 15,
                Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        Drawable d = getResources().getDrawable(R.drawable.ic_launcher);
        d.setBounds(0, 0, d.getIntrinsicWidth(), d.getIntrinsicHeight());
        ImageSpan span = new ImageSpan(d, ImageSpan.ALIGN_BASELINE);
        ss.setSpan(span, 18, 19, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
        txtInfo.setText(ss);
        txtInfo.setMovementMethod(LinkMovementMethod.getInstance());
        setContentView(txtInfo);
    }
{% endhighlight %}

---

最终项目出来的效果图为:

![图片链接](/res/img/blog/2014/11/12/cc.png)

