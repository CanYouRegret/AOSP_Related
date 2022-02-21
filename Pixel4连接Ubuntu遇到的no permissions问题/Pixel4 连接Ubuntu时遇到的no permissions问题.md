# Pixel4 连接Ubuntu时遇到的no permissions问题

![](.\adb_devices_no_permissions.png)

参考[](https://blog.csdn.net/cgx090902/article/details/73863481)解决

## 1. lsusb 查看设备相关id, 这里Pixel4是 18d1:4ee7

![](.\adb_devices_lsusb.png)

## 2. 运行 sudo vim /etc/udev/rules.d/51-android.rules

添加如下内容 

SUBSYSTEM==”usb”,ATTRS{idVendor}==”**18d1**”,ATTRS{idProducd}==”**4ee7**”,MODE=”0666”

## 3. 运行sudo vim /lib/udev/rules.d/50-udev-default.rules

查找"usb_device"关键字, 找到如下内容

SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", MODE="0664"

将0664改为0666 保存。

## 4. 运行sudo chmod a+rx /lib/udev/rules.d/50-udev-default.rules

## 5. 运行sudo service udev restart 重启udev

## 6. 拔掉usb线再重新连接，重启adb
