title: "Jquery自定义插件"
date: 2014-07-09 22:51:27
tags: [Jquery]
---

## Jquery 自定义插件 ##

平时看到的Jquery插件都是 $("xxx").my_plugin()这样子就能使用。对于jquery来说自定义插件非常方便，只需要实现 $.fn.my_plugin这种类型的方法就可以。

在这里我们实现一个瀑布流的产品选择器


``` javascript
(function($) {
  // 需要实现的各种方法在这里实现
  
  return $.fn.productsModalSelector = function(options, callBack) {
  
      return $(this).each(function(i, e) {
        var defaults = {
          width: '1250px',
          height: '320px',
          remove_result: true,
          remove_chosen: true,
          debug: false,
          pagination: ".pagination a",
          multiple: true,
          type: 'product',
          extend_params: {},
          after_save: function(data) {},   // 一个回调函数， 为了可以让用户选择完成后能够自定义一些处理函数
        }  // 默认传入的参数， 可以被重载
        var $this = $(e)
        _data_extend($this, defaults)
  
        options = $.extend(true, {}, defaults, options);
        initialization($this, options)
  
        $this.click(function() {
          $save_btn.unbind("click");
          $root.modal({
            width: options.width,
            height: options.height
          });
          $save_btn.click(function() {
            var products = selectedItmes()
            // 函数内自己的处理
            options.after_save.call($this, products)  // 调用用户重载的回调函数传入选择好的products
            // 函数内自己的处理
          });
        });
      })
    }
  
  })(jQuery);
```

上面简单的实现了自定义插件的基本框架

### 瀑布流 ###

瀑布流是指我们很流行的拉伸刷新，只要你查看到底部，就会请求下一页的数据

需要使用到的方法就是 [jquery scroll](http://api.jquery.com/scroll/)，利用这个方法实现：
``` javascript
  var nDivHight = $results_list.height();  //用户能看到的高度

  var _scroll = function(event){
    var options = event.data
    nScrollHight = $(this)[0].scrollHeight;  //用来获取这个元素的高度
    nScrollTop = $(this)[0].scrollTop;      //用来获取这个元素
    if(nScrollTop + nDivHight >= nScrollHight && !loading){  //判断是不是拉到底部
      loading = true
      _loader.call($(this), options)
    }
  }
```

负责请求数据的方法，瀑布流的原理很简单，每次拉伸除了请求本次数据以外还需要拿到下次请求数据的链接，当被拉伸到底部，请求方法根据链接请求下一页

```javascript
var _loader = function(options){
    var _url = $(options.pagination).attr("href")

    if (typeof _url != "undefined" && _url.trim() != ""){
      $results_list.append('<div id="state_mes" class="span1 offset4"><i class="icon-spinner icon-spin orange bigger-250"></i></div>')

      $.ajax({
        url : _url,
        dataType : 'html',
        success : function(data){
          if (options.debug) {
                console.log(data)
          }
          $(options.pagination).remove() // 下一页的链接
          $results_list.append(data)
          loading = false
        },
        error : function(xhr, status){
          $("#state_mes").remove()
          $results_list.append('<div id="state_mes" class="span2 offset2"><p>loading fail</div>')
          setTimeout(_remove_mes, 1000) // 显示错误信息，等待1秒后删除
        }
      })
    }else{
      $results_list.append('<div id="state_mes" class="span3 offset1"><p>you hit the end of the results</div>')
      setTimeout('$("#state_mes").remove()', 1000)  //已经没有下一页
    }
  }

```


最后就是在init 的时候为每个方法绑定
```javascript
  $results_list.bind("scroll", options, _scroll)
```

后台需要返回的数据：

```ruby
  next_url = "#{search_product_search_index_path}?query=#{params[:query]}&page=#{@products.next_page}&type=#{params[:type]}" unless @products.last_page?
  respond_to do |format|
      format.html {
       render :partial => 'result_list', :locals=>{:@products => @products, :@next => next_url}
      }
      format.json{
        render :json => { :products => @products.as_json(only: [ :id, :name, :model, :image ], methods: [:image_thumb_url]),
                          :next_url => next_url
                        }
      }
   end
```


## 效果图 ##

{% asset_img case-example.png %}

{% asset_img case-select.png %}

{% asset_img loading.png %}