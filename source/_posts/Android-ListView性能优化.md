title: "Android ListView性能优化"
date: 2015-05-11 23:04:41
tags: [Android, listView, 优化]
---

1. 使用Adapter提供的convertView
    convertView是Adapter提供的视图缓存机制,当第一次显示数据的时候, adapter会创建n个(n等于页面可见的item的数目) convertView,当下次需要显示新的item的时候, adapter会循环利用这些已经创建的convertView, 減少再次创建convertView所带来的开销,从而达到性能的提升。
2. 使用自定义的视图缓存类
    就是自定义一个视图缓存类,在这个类中保存我们在item中使用到的视图的引用,通过convertView的setTag方法和getTag方法来存储这个视图缓存类引用和重新获取这个视图缓存类引用 , 其目的也是为了減少重复创建视图时的开销。
3. 減少不必要的视图更新
    ListView在滚动时会请求重新获取item,来显示不同内容的item,而如果在获取item时比较耗时就会造成在滚动时出现卡顿的现象。 那我们可以通过监听ListView的滚动事件来使ListView处于不同的滚动状态时做不同的事情 , 比如在ListView处于滚动过程中加裁少量的显示数据,当ListView处于空闲的状态时再加裁所有的数据,这样就可以減少ListView在滚动过程中的开销,从而提供ListView的滚动速度。

####对1、 2两点进行实现####
实现ViewHandler，利用setTag 、getTag存储这个视图缓存类

``` java
/**
ViewHandler
**/
public class ViewHandler{
  private List<View> views;
  private View convertView;
  public ViewHandler(View convertView){
    this.convertView = convertView;
    this.views = new ArrayList<View>();
    this.convertView.setTag(this);
  }

  public <T extends View> getView(int id)

  public static ViewHandler get(View convertView){
    if(convertView.getTag() == null){
      return new ViewHandler(convertView);
    }
    return (ViewHandler) convertView.getTag();
  }

}

```


``` java
/**
Adapter
**/
public View getView(int position, View convertView, ViewGroup parent) {
    if (convertView == null) {
         convertView = LayoutInflater.from(context)
                 .inflate(R.layout.good_list_item, null, false);
    }

    ViewHandler mViewHandler = ViewHandler.get(convertView);
    TextView price = mViewHolder.getView(R.id.price);
    //...其他getView

    return convertView;
 }
```