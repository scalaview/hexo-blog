title: "Android service在后台不被kill"
date: 2015-05-10 11:44:20
tags: [Android, Java, Service]
---

1. onStartCommand方法,返回START_STICKY
[Service 文档](http://developer.android.com/intl/zh-cn/reference/android/app/Service.html)
####StartCommond几个常量参数简介:####
- START_STICKY
    >在运行onStartCommand后service进程被kill后,那将保留在开始状态,但是不保留那些传入的intent不久后service就会再次尝试重新创建,因为保留在开始状态,在创建service后将保证调用onstartCommand。如果没有传递任何开始命令给service,那将获取到null的intent。
- START_NOT_STICKY
    >在运行onStartCommand后service进程被kill后,并且没有新的intent传递给它。Service将移出开始状态,并且直到新的明显的方法(startService)调用才重新创建。因为如果没有传递任何未决定的intent那么service是不会启动,也就是期间onstartCommand不会接收到任何null的intent。
- START_REDELIVER_INTENT
    >在运行onStartCommand后service进程被kill后,系统将会再次启动service,并传入最后一个intent给onstartCommand。直到调用stopSelf(int)才停止传递intent。如果在被kill后还有未处理好的intent,那被kill后服务还是会自动启动。因此onstartCommand不会接收到任何null的intent。

    手动返回START_STICKY,亲测当service因内存不足被kill ,当内存又有的时候,service又被重新创建,比较不销,但是不能保证任何情况下都被重建,比如进程被干掉了.
 
2. 提升service优先级

在AndroidManifest.xml文件中对于intent-filter可以通过android:priority="1000"这个属性设置最高优先级,1000是最高値,如果数字越小则优先级越低,同时适用于广播。 

```xml
<?xml version="1.0" encoding="utf-8"?>
<service 
    android:name="com.dbjtech.acbxt.waiqin.UploadService"
    android:enabled="true" >
    <intent-filter android:priority="10000"
    <action android:name="com.dbjtech.myservice" /> 
    </intent-filter>
</service>
```

目前看来, priority这个属性貌似只适用于broadcast,对于Service来说可能无效
 
 
3.提升service进程优先级

 Android中的进程是托管的,当系统进程空间紧张的时候,会依照优先级自动进行进程的回收。Android将进程分为6个等级,它们按优先级顺序由高到低依次是:
+ 前台进程(FOREGROUND_APP)
+ 可视进程(VISIBLE_APP)
+ 次要服务进程(SECONDARY_SERVER)
+ 后台进程(HIDDEN_APP)
+ 内容供应节点(CONTENT_PROVIDER)
+ 空进程(EMPTY_APP)
 
当service运行在低内存的环境时,将会kill掉一些存在的进程。因此进程的优先级将会很重要,可以使用startForeground将service放到前台状态。这样在低内存时被kill 的几率会低一些。

如果在极度极度低内存的压力下,该service还是会被kill掉,并且不一定会restart
 
4.onDestroy方法里重启service
 
service+broadcast方式,就是当service走ondestory的时候,发送一个自定文的广播,当收到广播的时候,重新启动service

```xml
<receiver android:name="com.dbjtech.acbxt.waiqin.BootReceiver" >
<intent-filter>
<action android:name="android.intent.action.B00T_COMPLETED" />
<action android:name="android.intent.action.USER_PRESENT" />
<action android:name="com.dbjtech.waiqin.destroy" />//这个就是自定文的action </intent-filter>
</receiver>
```
 
在onDestroy时:

```Java
@Override
public void onDestroy(){
  stopForeground(true);
  lntent intent= newlntent("com.dbjtech.waiqin.destroy"); sendBroadcast(intent);
  super.onDestroy0;
}
```

在BootReceiver里


```Java
public class BootReceiver extends BroadcastReceiver{

  @Override
  public void onReceive(Context context, lntent intent) {
    if(intent.getAction0.equals("com.dbjtech.waiqin.destroy")) {
    //TOD0
    //在这里写重新启动service的相关操作
    startUploadService(context);
    }
  }
}
```

也可以直接在onDestroy( )里startService

```java
@Override
public void onDestroy0{
  Intent sevice = new Intent(this, MainService.class); 
  this.startService(sevice);
  super.onDestroy0; 
}
```

当使用类似口口管家等第三方应用或是在setting里一应用一强制停止时, APP 进程可能就直接被干掉了, onDestroy方法都进不来,所以还是无法保证~.~


5.【终极方案】启动一个native进程,每1分钟检查一下Service是否启动
  
注:无需root权限
依束: am命令, proc文件系统
 
 
- 使用popen每1分钟启动一次Service,如果service启动过了,是不会执行onCreate 方法的。
以下是 native程序实现test.cpp:

```c
int main(int argc, char* argv[]) {
  while(1) {
  //需要判断app是否已卸載,如果已卸載则退出程序,代码省略
  char* command= "am startservice -n com.android.xxx/ com.android.xxx.XXXService-u 0"；

  FILE *fp= popen(command, "r");
  if(fp) {
    pclose(fp);
  }
    sleep(60);
  }
}
```

Android.mk文件,与JNl的so不同, Android.mk文件需要重写:

```c
include$(CLEAR_VARS)
LOCAL_SRC_FILES:= test.cpp
LOCAL_MODULE:= test1
LOCAL_FORCE_STATIC_EXECUTABLE:= true
LOCAL_STATIC_LIBRARIES:= libc android
LOCAL_MODULE_PATH:= S(TARGET_0UT_0PTIONAL_EXECUTABLES) LOCAL_MODULE_TAGS:= debug
#注意这里的命令,必须编译成这种文件名,当然是为了逃过android安装
LOCAL_MODULE_FILENAME:= "libtest.so"
LOCAL_LDLIBS:= -lm-llog-landroid
include S(BUILD_EXECUTABLE)
```

- Android App中启动一个native进程

```java
/***************************************************
* RunExecutable启动一个可自行的lib*.so文件<br> *文件要全名filename<br>
* alias是别名<br>
* args是参数
*********************************************** **/
public String RunExecutable(String pacaageName, String filename,String alias, String args) {
  String path= "/data/data/" + pacaageName;
  String cmd1 = path+ "/lib/" + filename;
  String cmd2 = path+ "/" + alias;
  String cmd2_a1 = path+ "/" + alias+ " " + args;
  String cmd3 = "chmod777 " + cmd2;
  String cmd4 = "dd if=" + cmd1 + " of=" + cmd2;
  StringBuffer sb_result= new StringBuffer0;

 
  if(!new File("/data/data/" + alias).exists0) {
    //拷贝lib/libtest.so到上一层目录,同时命名为test.
    RunLocalUserCommand(pacaageName, cmd4, sb_result);
    sb_result.append(";");
  }
  RunLocalUserCommand(pacaageName, cmd3, sb_result);//改变test的属性,让其变为可执行
  sb_result.append(";");
  RunLocalUserCommand(pacaageName, cmd2_a1, sb_result);//执行test程序.
  sb_result.append(";");
  return sb_result.toString0;
}

 
/****************************************************
* RunLocalUserCommand<br>
*执行本地用户命令
* **************************************************/
public boolean RunLocalUserCommand(String pacaageName, String
command,StringBuffer sb_out_Result) {
  Process process= null;
  try{
    process= Runtime.getRuntime0.exec("sh");//获得shell进程
    DatalnputStream inputStream= new DatalnputStream(process.getlnputStream());
    DataOutputStream outputStream= new DataOutPutStream(process.getOutStream());
    
    //保证在command在自己的数据目录里执行,才有权限写文件到当前目录
    outputStream.writeBytes("cd/data/data/" + pacaageName+ "\n");
    outputStream.writeBytes(command+ " &\n");//让程序在后台运行,前台马上返回outputStream.writeBytes("exit\n");
    outputStream.flush();
    process.waitFor();
    byte[] buffer= new byte[inputStream.available0]; inputStream.read(buffer);
    String s= new String(buffer);
    if(sb_out_Result!= null) {
      sb_out_Result.append("CMD Result:\n" + s);
    } 
  } catch(Exception e) {
    if(sb_out_Result!= null){
      sb_out_Result.append("Exception:" + e.getMessage0); 
    }
      return false;
  }
  return true;
}
```