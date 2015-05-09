title: "Android DatePicker&TimePicker"
date: 2015-05-09 10:52:09
tags:
---

一、DatePicker继承自FrameLayout类，日期选择控件的主要功能是向用户提供包含年、月、日的日 期数据并允许用户对其修改。如果要捕获用户修改日期选择控件中的数据事件，需要为DatePicker添加OnDateChangedListener监 听器。
二、TimePicker也继承自FrameLayout类。时间选择控件向用户显示一天中的时 间（可以为24小时，也可以为AM/PM制），并允许用户进行选择。如果要捕获用户修改时间数据的事件，便需要为TimePicker添加 OnTimeChangedListener监听器
以下模拟日期与时间选择控件的用法
目录结构

{% asset_img package.png %}

main.xml布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <DatePicker android:id="@+id/datePicker" 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"/>
    <EditText android:id="@+id/dateEt"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:cursorVisible="false"
        android:editable="false"/>
    <TimePicker android:id="@+id/timePicker" 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"/>
    <EditText android:id="@+id/timeEt"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:cursorVisible="false"
        android:editable="false"/>
</LinearLayout>
```

DpTpActivity类

``` java
package com.ljq.activity;

import java.util.Calendar;

import android.app.Activity;
import android.os.Bundle;
import android.widget.DatePicker;
import android.widget.EditText;
import android.widget.TimePicker;
import android.widget.DatePicker.OnDateChangedListener;
import android.widget.TimePicker.OnTimeChangedListener;

public class DpTpActivity extends Activity {
    private EditText dateEt=null;
    private EditText timeEt=null;
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        dateEt=(EditText)findViewById(R.id.dateEt);
        timeEt=(EditText)findViewById(R.id.timeEt);
        DatePicker datePicker=(DatePicker)findViewById(R.id.datePicker);
        TimePicker timePicker=(TimePicker)findViewById(R.id.timePicker);
        
        Calendar calendar=Calendar.getInstance();
        int year=calendar.get(Calendar.YEAR);
        int monthOfYear=calendar.get(Calendar.MONTH);
        int dayOfMonth=calendar.get(Calendar.DAY_OF_MONTH);
        datePicker.init(year, monthOfYear, dayOfMonth, new OnDateChangedListener(){

            public void onDateChanged(DatePicker view, int year,
                    int monthOfYear, int dayOfMonth) {
                dateEt.setText("您选择的日期是："+year+"年"+(monthOfYear+1)+"月"+dayOfMonth+"日。");
            }
            
        });
        
        timePicker.setOnTimeChangedListener(new OnTimeChangedListener(){

            public void onTimeChanged(TimePicker view, int hourOfDay, int minute) {
                timeEt.setText("您选择的时间是："+hourOfDay+"时"+minute+"分。");
            }
            
        });
    }
}
```
运行结果
{% asset_img screentshort.png %}


