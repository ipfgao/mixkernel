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
