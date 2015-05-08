title: "sd card 读写"
date: 2014-06-09 00:30:45
tags: [Java, Andorid, Data Storage]
---
# SD Card 读写 #
对sdcrad 进行读写与普通java IO一样，只需要在使用前使用 Environment.getExternalStorageState() 判断sdcard 是否可用

[Android Data Storage](http://developer.android.com/guide/topics/data/data-storage.html)
#### 常用的几种存储方式 ####
* SharedPreferences
* External Storage
* SQLite

#### SD Card 属于 External Storage ####
通过添加下面的权限才可以使用

```xml
<manifest ...>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    ...
</manifest>
```



```java
public String getFileFromSdcard(String fileName) {
        FileInputStream inputStream = null;
        // 缓存的流，和磁盘无关，不需要关闭
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        File file = new File(Environment.getExternalStorageDirectory(),
                fileName);
        if (Environment.MEDIA_MOUNTED.equals(Environment
                .getExternalStorageState())) {
            try {
                inputStream = new FileInputStream(file);
                int len = 0;
                byte[] data = new byte[1024];
                while ((len = inputStream.read(data)) != -1) {
                    outputStream.write(data, 0, len);
                }

            } catch (FileNotFoundException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } catch (IOException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } finally {
                if (inputStream != null) {
                    try {
                        inputStream.close();
                    } catch (IOException e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                    }
                }
            }

        }

        return new String(outputStream.toByteArray());
    }

    /**
     * @param fileName
     *            文件的名称
     * @param content
     *            文件的内容
     * @return
     */
    public boolean saveContentToSdcard(String fileName, String content) {
        boolean flag = false;
        FileOutputStream fileOutputStream = null;
        // 获得sdcard卡所在的路径
        File file = new File(Environment.getExternalStorageDirectory(),
                fileName);
        // 判断sdcard卡是否可用
        if (Environment.MEDIA_MOUNTED.equals(Environment
                .getExternalStorageState())) {
            try {
                fileOutputStream = new FileOutputStream(file);
                fileOutputStream.write(content.getBytes());
                flag = true;
            } catch (FileNotFoundException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } catch (IOException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } finally {
                if (fileOutputStream != null) {
                    try {
                        fileOutputStream.close();
                    } catch (IOException e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                    }
                }
            }
        }
        return flag;
    }
```