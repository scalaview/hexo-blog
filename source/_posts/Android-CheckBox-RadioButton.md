title: "Android CheckBox&RadioButton"
date: 2015-01-22 16:16:16
tags:
---

CheckBox和RadioButton控件都只有选中和未选中状态，不同的是RadioButton是单选按钮，需要编制到一个RadioGroup中，同一时刻一个RadioGroup中只能有一个按钮处于选中状态。
以下为CheckBox和RadioButton常用方法及说明
{% asset_img table.png %}
以下为单选按钮和复选按钮的使用方法
目录结构
​{% asset_img package.png %}
main.xml布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:scrollbars="vertical">
    <LinearLayout android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
        <!-- RadioButton控件演示 -->
        <ImageView android:id="@+id/imageView01"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/bulb_on" 
            android:layout_gravity="center_horizontal" />
        <RadioGroup android:id="@+id/radioGroup"
            android:orientation="horizontal"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal">
            <RadioButton android:id="@+id/on"
                android:text="开灯"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:checked="true" />
            <RadioButton android:id="@+id/off"
                android:text="关灯"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" />
        </RadioGroup>
        
        <!-- CheckBox控件演示 -->
        <ImageView android:id="@+id/imageView02"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/bulb_on" 
            android:layout_gravity="center_horizontal" />
        <CheckBox android:id="@+id/checkBox"
            android:text="开灯"
            android:checked="true"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal" />
    </LinearLayout>
</ScrollView>
```

CbRbActivity类

```java
package com.ljq.activity;

import android.app.Activity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.ImageView;
import android.widget.RadioButton;
import android.widget.CompoundButton.OnCheckedChangeListener;

public class CbRbActivity extends Activity {
    private ImageView imageView01=null;
    private ImageView imageView02=null;
    private CheckBox checkBox=null;
    private RadioButton on=null;//开灯
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        imageView01=(ImageView)findViewById(R.id.imageView01);
        imageView02=(ImageView)findViewById(R.id.imageView02);
        checkBox=(CheckBox)findViewById(R.id.checkBox);
        on=(RadioButton)findViewById(R.id.on);
        
        on.setOnCheckedChangeListener(listener);
        checkBox.setOnCheckedChangeListener(listener);
    }
    
    OnCheckedChangeListener listener=new OnCheckedChangeListener(){

        public void onCheckedChanged(CompoundButton buttonView,
                boolean isChecked) {
            if(buttonView instanceof RadioButton){
                imageView01.setImageResource(isChecked?R.drawable.bulb_on:R.drawable.bulb_off);
            }else if(buttonView instanceof CheckBox){
                checkBox.setText(isChecked?"开灯":"关灯");
                imageView02.setImageResource(isChecked?R.drawable.bulb_on:R.drawable.bulb_off);
            }
        }
    };
}
```

运行结果
​{% asset_img screentshort.png %}
