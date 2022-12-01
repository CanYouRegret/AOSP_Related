# 编译烧写Android kernel(Pixel4)

## 前言

&emsp; Android是基于Linux的，想要对Android系统有更深的了解，对内核的研究必不可少。该文章基于Pixel4用来记录编译烧写kernel的过程，笔者的开发环境为Ubuntu20.04、Android源码android-13.0.0_r1分支、Google Pixel4 flame。

## 1. 下载所需的Android Kernel源码

* 首先新建一个目录用来存放kernel源码，最好跟Android系统的源码位于同级目录。

* 选择我们需要下载的kernel代码分支，这点可以参考Android官方介绍(https://source.android.com/docs/setup/build/building-kernels)，笔者的Android真机为Pixel4，因此选择的kernel代码分支为android-msm-coral-4.14-android13。

* 确定需要下载的分支之后，在目录下运行repo相关的命令进行下载。Android官方的命令如下：

  ```shell
  repo init -u https://android.googlesource.com/kernel/manifest -b android-msm-coral-4.14-android13
  repo sync
  ```

​       如果不能科学上网的话，可以用国内清华的镜像来替代下载。

  ```shell
  repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/kernel/manifest -b android-msm-coral-4.14-android13
  repo sync
  ```

* 然后就是静静的等待sync完成，相应的目录下可以看到kernel的源码已经下载成功。

## 2. 编译Kernel

&emsp; 正常来说，直接在目录下运行./build/build.sh，等待编译结束即可，编译生成的镜像文件位置为out/android-msm-pixel-4.14/dist/Image.lz4。

&emsp; 但是笔者在烧写该kernel镜像文件时，发现烧写不进去，卡在Google界面。笔者是采用以下办法解决的：

* 在目录下使用ls -al build.config查看build.config文件的映射关系，结果如下：

  ```
  build.config -> private/msm-google/build.config.no-cfi
  ```

  所以猜测是用的配置不对，因此先将该build.config删除掉(rm build.config)，在使用ln -s private/msm-google/build.config.floral build.config命令建立新的配置映射，再运行build/build.sh。

* 按照上述步骤操作之后，发现生成的镜像文件还是不能成功烧录，不过out/android-msm-pixel-4.14/private/msm-google/arch/arm64/boot/Image.lz4-dtb是可以成功烧写的。(具体什么原因也不太清楚。。。)

## 3. 烧写Kernel至手机

&emsp; 首先我们要明确一个问题，如何判断我们编译的kernel镜像烧写成功了？

这里Pixel4已经烧写了AOSP android-13.0.0.r1，此时我们通过在shell下运行uname -a命令可以得到如下结果:

```
Linux localhost 4.14.276-g8ae7b4ca8564-ab8715030 #1 SMP PREEMPT Mon Jun 13 10:05:49 UTC 2022 aarch64 Toybox
```

然后我们连接手机，运行adb reboot bootloader进入bootloader，fastboot boot Image.lz4(或者Image.lz4-dtb)。待到开机之后，我们再运行uname -a，如果成功的化结果类似如下：

```
Linux localhost 4.14.276-dirty_audio #1 repo:android-msm-coral-4.14-android13 SMP PREEMPT Thu Dec 1 1 aarch64 Toybox
```

&emsp; 可以看到有了dirty以及android-msm-coral-4.14-android13分支的标识。

## 后记

&emsp; 至此，已经可以成功的编译kernel，并烧写至手机使能。可以在kernel代码中添加log或者功能来进行探索学习。后续将会介绍如何在kernel增加一个新的驱动，跟hal层通信或者对系统调用表进行hook。

