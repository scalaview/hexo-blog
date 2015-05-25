title: "Android Asynctask and Handler"
date: 2014-05-06 00:24:41
tags: [Java, Android, Thread]
---

# AsyncTask #
你可以直接查看[AsyncTask 官方文档](http://developer.android.com/reference/android/os/AsyncTask.html)
AsyncTask 是android 提供的轻量级异步类，使用线程池的机制，容量是128，最多同时运行5个core线程

下面一个示例代码

```java
  public class TestActivity extends Activity{

    @Override
    protected void onCreate(Bundle saveInstanceState){
      super.onCreate(saveInstanceState);
      setContentView(R.layout.main);
      (new LoadAsyncTask()).execute(1000);
    }

    private classLoadAsyncTask extends AsyncTask{
      @Override
      protected void onPreExecute(){
        Log.v("test", "start");
      }

      @Override
      protected String doInBackground(Integer...params){
        // load the http data
      }

      @Override
      protected void onPostExecute(String result){
        Log.v("test", "finish");
      }

    }

  }
```

task 只能执行一次,重复执行会抛出异常

# Handler #
[Handler 文档](http://developer.android.com/reference/android/os/Handler.html)

在多个后台任务时，简单，清晰，但是对于单任务处理，就显得过于臃肿

```java
public class TestActivity extends Activity{
  private threadHandler handler;

  @Override
  public void onCreate(Bundle saveInstanceState){
    super.onCreate(saveInstanceState);
    setContentView(R.layout.main);
    handler = new threadHandler(this);
    new Thread(new Runnable(){
      @Override
      public void run(){
        // load http data
        updateUI();
      }
    }).start();
  }

  static class threadHandler extends Handler {
    private WeakReference testReference;
    public threadHandler(TestActivity activity){
      testReference = new WeakReference<TestActivity>(activity);
    }

    @Override
    public void handleMessage(Message msg){
      super.handleMessage(msg);
      TestActivity activity = testReference.get();
      // update UI
    }

    public void updateUI(){
      handler.sendEmptyMessage(0);
    }
  }

}
```

#总结#
使用这两种方式就能异步的请求资源，然后更新前台UI