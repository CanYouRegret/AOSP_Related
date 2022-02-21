# 解锁bootloader

## 1. 确保开发者选项中的"OEM 解锁"是打开状态

## 2. 连接usb，运行adb reboot bootloader

## 3. 运行fastboot devices可以看到自己的设备

## 4. 运行fastboot flashing unlock 可以看到手机上出现选项

## 5. 操作音量键选择 解锁bootloader

## 6. 点击开机键进行Start重启完成，这时候发现开发者选项中的"OEM 解锁"是灰显状态，并显示"引导加载程序已解锁"，表明bootloader已经解锁成功。




