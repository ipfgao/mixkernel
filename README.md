# mixkernel
============
## 这个一个能使用同一个内核（包括对应的内核模块），可以同时或分别启动安卓x86 和 标准Linux桌面系统（Ubuntu）的项目。
## 该项目包括（或者将要包括）：
    1、从主线内核到安卓x86内核的补丁
    2、config文件
    3、其他还没有想到的东西
## linux内核源代码的获取
   1、主线内核：到 https://www.kernel.org/ 下载
         或者：git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git  
   2、安卓内核：git clone https://android.googlesource.com/kernel/x86_64
   3、安卓X86内核：git clone git://git.code.sf.net/p/android-x86/kernel android-x86-kernel
   4、用git checkout切换到你需要的分支上，以安卓X86内核kernel-4.9分支为例：
   cd android-x86-kernel
   git checkout kernel-4.9
## 内核配置
为了保证能启动标准Linux桌面系统，以Ubuntu为例，采用Ubuntu系统内核的config配置文件为基础，添加配置安卓x86所必须驱动。
**安卓必须驱动：
```
    CONFIG_ANDROID=y
    CONFIG_ANDROID_BINDER_IPC=y
    CONFIG_ANDROID_BINDER_DEVICES="binder"
    # CONFIG_ANDROID_BINDER_IPC_32BIT is not set
    CONFIG_ASHMEM=y
    CONFIG_ANDROID_LOW_MEMORY_KILLER=y
    CONFIG_ANDROID_LOW_MEMORY_KILLER_AUTODETECT_OOM_ADJ_VALUES=y
```

** ubuntu模块化了的，但安卓initrd无法引导需要编译在内核内的AHCI驱动：
```
    CONFIG_SATA_AHCI=y
    CONFIG_SATA_AHCI_PLATFORM=y
```

** 解决导致安卓出现黑屏的问题
```
    CONFIG_NO_GPU_AUTHENTICATION=y
```

** 解决导致安卓出问题的驱动（鼠标是只能上下活动，而且是左右变成了上下）：
```
    # CONFIG_USB_KBD is not set
    # CONFIG_USB_MOUSE is not set
```

** 解决导致Ubuntu普通用户访问网络出问题的选项配置（主要安卓特有的网络权限设置导致的）：
```
    # CONFIG_ANDROID_PARANOID_NETWORK is not set
```

同时在commoncap.c中注释掉（此4.9.31的内核不需要，这是自己打补丁的4.10内核碰到的问题）：
    
```
    /*    if (cap == CAP_NET_RAW && in_egroup_p(AID_NET_RAW))
            return 0;
        if (cap == CAP_NET_ADMIN && in_egroup_p(AID_NET_ADMIN))
            return 0;
    */
```

** 解决编译内核时体积过大的问题（这是Ubuntu自己的问题）
```
    # CONFIG_DEBUG_INFO is not set
```

将以上选项添加到Ubuntu内核conifg文件中，使用修改后的文件作为配置文件来编译安卓x86的内核，可以实现一个内核通用与android x86 和ubuntu。

另外需要的功能，看看是否存在：
    启用devtmpfs：
```
            CONFIG_DEVTMPFS=y
```
## 编译内核
进入 内核源码目录
```
    cd android-x86-kernel
```

把配置好的config文件重命名为.config
```
    make oldconfig
```

然后编译
```
    make
```

打包
```
    make targz-pkg
```
需要deb包的可以
```
    make deb-pkg
```
如果有缺少wl文件的错误可以用make -i编译忽略该错误。
当然你也可以用安卓x86的工具链交叉编译
例如：
```
    export ARCH=x86
    export SUBARCH=x86_64
    export PATH=~/prebuilts/x86_64-linux-android-4.9/bin:$PATH
    export CROSS_COMPILE=x86_64-linux-android-
    make
    make targz-pkg
```

## 安装
安装到linux里可以直接
```
    make install
```

对于安卓x86看你的安装方式了。
