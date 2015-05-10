title: "Android AutoCompleteTextView&MultiAutoCompleteTextView"
date: 2015-05-09 16:30:19
tags:
---

在Android中提供了两种智能输入框，它们是AutoCompleteTextView、MultiAutoCompleteTextView。它们的功能大致一样。显示效果像Google搜索一样，当你在搜索框里输入一些字符时（至少两个字符），会自动弹出一个下拉框提示类似的结果。下面详细介绍一下。
### AutoCompleteTextView ###
1. 简介
一个继承自EditView的可编辑的文本视图，能够实现动态匹配输入的内容。如google搜索引擎当输入文本时可以根据内容显示匹配的热门信息。
2. 重要方法
clearListSelection()：清除选中的列表项
dismissDropDown()：如果存在关闭下拉菜单
getAdapter()：获取适配器

### MultiAutoCompleteTextView ###
1. 简介
一个继承自AutoCompleteTextView的可编辑的文本视图，能够对用户键入的文本进行有效地扩充提示，而不需要用户输入整个内容。（用户输入一部分内容，剩下的部分系统就会给予提示）。
用户必须提供一个MultiAutoCompleteTextView.Tokenizer用来区分不同的子串。
2. 重要方法
enoughToFilter()：当文本长度超过阈值时过滤
performValidation()：代替验证整个文本，这个子类方法验证每个单独的文字标记
setTokenizer(MultiAutoCompleteTextView.Tokenizer t)：用户正在输入时，tokenizer设置将用于确定文本相关范围内
以下为案例
main.xml布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <AutoCompleteTextView android:id="@+id/autoCompleteTextView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
    <MultiAutoCompleteTextView android:id="@+id/multiAutoCompleteTextView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
</LinearLayout>
```

TextViewActivity类

```java
package com.ljq.activity;

import android.app.Activity;
import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.AutoCompleteTextView;
import android.widget.MultiAutoCompleteTextView;

public class TextViewActivity extends Activity {
    //福建九地市
    private static final String[] cities=new String[]
        {"FuZhou", "XiaMen", "NiDe", "PuTian","QuanZhou", "ZhangZhou", "LongYan", "SanMing","NanPing"};
    private AutoCompleteTextView autoCompleteTextView=null;
    private MultiAutoCompleteTextView multiAutoCompleteTextView=null;
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        autoCompleteTextView=(AutoCompleteTextView)findViewById(R.id.autoCompleteTextView);
        multiAutoCompleteTextView=(MultiAutoCompleteTextView)findViewById(R.id.multiAutoCompleteTextView);
        
        //创建适配器
        ArrayAdapter<String> adapter=new ArrayAdapter<String>(
                this, 
                android.R.layout.simple_dropdown_item_1line, 
                cities);
        autoCompleteTextView.setAdapter(adapter);
        //设置输入多少字符后提示，默认值为2
        autoCompleteTextView.setThreshold(2);
        
        multiAutoCompleteTextView.setAdapter(adapter);
        multiAutoCompleteTextView.setThreshold(2);
        //用户必须提供一个MultiAutoCompleteTextView.Tokenizer用来区分不同的子串。
        multiAutoCompleteTextView.setTokenizer(new MultiAutoCompleteTextView.CommaTokenizer());
    }
}
```

运行结果
{% asset_img screentshort.png %}