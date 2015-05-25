title: "RecyclerView-tutorial"
date: 2015-05-25 22:04:21
tags:[Android, RecyclerView]
---

## RecyclerView 介绍 ##
官方文档：[RecyclerView](https://developer.android.com/intl/zh-cn/reference/android/support/v7/widget/RecyclerView.html)
RecycleView 是google的v7 包中用来替代ListView，其中直接拥有缓存模块ViewHolder。里面包含的LayoutManager 可以很方便的实现ListView，GridView,下拉刷新。ItemAnimation也很好的实现了每个Item的特效。

## RecyclerView 使用 ##

```xml
 <android.support.v7.widget.RecyclerView
                android:id="@+id/list"
                android:clipToPadding="false"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingRight="@dimen/activity_horizontal_margin"
                android:paddingLeft="@dimen/activity_horizontal_margin"
                android:paddingTop="@dimen/activity_horizontal_margin"
                android:paddingBottom="@dimen/activity_vertical_margin"
                />
```
##### Activity
```java
public class MainActivity extends AppCompatActivity {
    @InjectView(R.id.list)
    RecyclerView mRecyclerView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
      ButterKnife.inject(this);

      mRecyclerView = (RecyclerView) findViewById(R.id.list);
      mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
      mRecyclerView.setItemAnimator(new CustomItemAnimator());

      mAdapter = new ApplicationAdapter(new ArrayList<AppInfo>(), R.layout.row_application, MainActivity.this);
      mRecyclerView.setAdapter(mAdapter);
    }

}

```

上面的代码还需要两个class，一个是CustomItemAnimator，用来做现实item特效；另一个是Adapter；下面分别实现

##### Adapter
```java

public class ApplicationAdapter extends RecyclerView.Adapter<ApplicationAdapter.ViewHolder> {

    private List<AppInfo> applications;
    private int rowLayout;

    public ApplicationAdapter(List<AppInfo> applications, int rowLayout) {
        this.applications = applications;
        this.rowLayout = rowLayout;
    }

    @Override
    public ViewHolder onCreateViewHolder(final ViewGroup viewGroup, int i) {
        View v = LayoutInflater.from(viewGroup.getContext()).inflate(rowLayout, viewGroup, false);
        return new ViewHolder(v);
    }

    @Override
    public void onBindViewHolder(final ViewHolder viewHolder, int i) {
        final AppInfo appInfo = applications.get(i);
        viewHolder.name.setText(appInfo.getName());
        viewHolder.image.setImageDrawable(appInfo.getIcon());

        viewHolder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // your code
            }
        });
    }

    @Override
    public int getItemCount() {
        return applications == null ? 0 : applications.size();
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {  //内部类，ViewHolder 线程同步View变化
        public TextView name;
        public ImageView image;

        public ViewHolder(View itemView) {
            super(itemView);
            name = (TextView) itemView.findViewById(R.id.countryName);
            image = (ImageView) itemView.findViewById(R.id.countryImage);
        }

    }
}


```

#####  ItemAnimator
```java
public class ReboundItemAnimator extends RecyclerView.ItemAnimator {
    //hold the views to animate in runPendingAnimations
    private List<RecyclerView.ViewHolder> mViewHolders = new ArrayList<RecyclerView.ViewHolder>();

    @Override
    public void runPendingAnimations() {
        if (!mViewHolders.isEmpty()) {
            for (final RecyclerView.ViewHolder viewHolder : mViewHolders) {
                // your code about animation
            }
        }
    }

    @Override
    public boolean animateRemove(RecyclerView.ViewHolder viewHolder) {
        viewHolder.itemView.animate().alpha(0).scaleX(0).scaleY(0).setDuration(300).start();
        return false;
    }

    @Override
    public boolean animateAdd(RecyclerView.ViewHolder viewHolder) {
        //viewHolder.itemView.setAlpha(0.0f);
        viewHolder.itemView.setScaleX(0);
        viewHolder.itemView.setScaleY(0);
        return mViewHolders.add(viewHolder);
    }

    @Override
    public boolean animateMove(RecyclerView.ViewHolder viewHolder, int i, int i2, int i3, int i4) {
        return false;
    }

    @Override
    public boolean animateChange(RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder viewHolder2, int i, int i2, int i3, int i4) {
        return false;
    }

    @Override
    public void endAnimation(RecyclerView.ViewHolder viewHolder) {
    }

    @Override
    public void endAnimations() {
    }

    @Override
    public boolean isRunning() {
        return !mViewHolders.isEmpty();
    }
}

```

#### 运行效果
{% asset_img screenshot.png %}










