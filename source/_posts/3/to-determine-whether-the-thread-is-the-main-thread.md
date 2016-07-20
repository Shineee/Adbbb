---
title: Android 判断线程是否是主线程
tags: [多线程]
---
```java
private static void checkThread() {  
        if (Looper.myLooper() != Looper.getMainLooper()) {  
        }  
}  
```
获取当前线程的looper与主线程的looper比对，如果一样，说明是主线程；如果不一样，说明不是主线程

