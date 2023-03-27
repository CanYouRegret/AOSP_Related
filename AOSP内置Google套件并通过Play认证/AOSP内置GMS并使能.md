# 在自编译的AOSP中内置GMS套件，并通过Google Play认证

## 前言

&emsp; 因为工作上的原因，需要研究GMS套件的逻辑，但是代码混淆的太过厉害了，静态分析也不够直观，因此想到在系统的层面上，对GMS调用系统API时的参数及返回值进行打印以辅助分析。

&emsp; 在比较早的Android版本，GMS套件一般是通过下载open gapps并通过recovery刷入的，但是笔者发现在比较高的Android版本，已经没有找到支持的方法了。 因此在想，既然我们都能够修改系统源码了，那么内置几个App应该也是没什么问题的。记录下内置GMS并成功使能的过程

## 1. 将所需的APP内置

&emsp; 正常来说，我们需要内置的就是Google的三件套GMS，GSF以及Vending。从网络上下载这三个应用，并为其编写相应的mk文件，单独建立目录放置在 packages/apps/目录下。笔者使用的资源可在该文章所在目录下找到。

&emsp; 然后在build/make/target/product/handheld_system.mk的PRODUCT_PACKAGES中将新建的几个目录加上 以参加编译。

## 2. 为APP添加相应的权限

&emsp; 如果只是内置之后编译-刷入，会发现开不了机，这几个应用进程一直在crash。是因为没有默认赋予相关的权限。 只需要在frameworks/base/data/etc/privapp-permission-platform.xml仿照其他应用为Google套件应用追加缺失的权限即可。 这点笔者也将相应的文件放在目录下

&emsp; 将权限补齐后再编译-刷入，应该是可以正常开机启动的，logcat可能还会看到一些crash，但是影响不大，如果需要也可以改动源码进行解决。

## 3. 注册设备

```
1. adb shell

2. sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = 'android_id';"
```

通过上面的方式拿到一个id，然后在https://www.google.com/android/uncertified/进行认证。 然后保持科学上网，静静等待即可。 一般最慢第二天就能正常的登录Google账号