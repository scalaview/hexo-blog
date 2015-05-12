title: "Android ToggleButton"
date: 2015-01-19 16:10:48
tags:
---
ToggleButton的状态只能是选中和未选中，并且需要为不同的状态设置不同的显示文本。
以下案例为ToggleButton的用法
目录结构
{% asset_img package.png %}
main.xml布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <ImageView android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/bulb_off"
        android:layout_gravity="center_horizontal" />
    <ToggleButton android:id="@+id/toggleButton"
        android:layout_width="140dip"
        android:layout_height="wrap_content"
        android:textOn="开灯"
        android:textOff="关灯"
        android:layout_gravity="center_horizontal" />
</LinearLayout>
```
ToggleButtonActivity类

```java
package com.ljq.tb;

import android.app.Activity;
import android.os.Bundle;
import android.widget.CompoundButton;
import android.widget.ImageView;
import android.widget.ToggleButton;
import android.widget.CompoundButton.OnCheckedChangeListener;
​
public class ToggleButtonActivity extends Activity {
    private ImageView imageView=null;
    private ToggleButton toggleButton=null;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        imageView=(ImageView) findViewById(R.id.imageView);
        toggleButton=(ToggleButton)findViewById(R.id.toggleButton);
        toggleButton.setOnCheckedChangeListener(new OnCheckedChangeListener(){

            public void onCheckedChanged(CompoundButton buttonView,
                    boolean isChecked) {
                toggleButton.setChecked(isChecked);
                imageView.setImageResource(isChecked ? R.drawable.bulb_on : R.drawable.bulb_off);
            }

        });
    }
}
```

运行效果：
{% asset_img screentshort.png %}

