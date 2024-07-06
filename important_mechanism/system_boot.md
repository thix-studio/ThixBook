# Android系统启动过程



<img src="https://cdn.nlark.com/yuque/0/2021/png/314790/1634627689386-bc6fbc2f-6045-4905-a5fe-6105873bfc1b.png"  align="center" width="40%" />



1、启动电源以及系统启动

当电源按下时引导芯片代码从预定义的地方（固化在ROM）开始执行，加载引导程序BootLoader到RAM，然后执行；

2、引导程序BootLoader拉起系统OS

引导程序BootLoader是在Android操作系统开始运行前的一个小程序，它的主要作用是把系统OS拉起来并运行；

3、Linux内核启动

当内核启动后，设置缓存、被保护存储器、计划列表、加载驱动。当内核完成系统设置时，它首先在系统文件中寻找init.rc文件，并启动init进程；

4、init进程启动

初始化和启动属性服务，并且启动zygote进程；

（1）创建和挂载启动所需的文件目录；

（2）初始化和启动属性服务；

（3）解析init.rc配置文件并启动Zygote进程；

5、zygote进程启动

创建Java虚拟机并注册JNI方法；创建服务端Socket，等待来自AMS的请求，去创建新的应用进程；启动SystemServer进程；

（1）创建AppRuntime并调用其start方法，启动Zygote进程；

（2）创建Java虚拟机并为Java虚拟机注册JNI方法；

（3）通过JNI调用ZygoteInit的main方法进入Zygote的Java框架层；

（4）通过registerZygoteSocket方法创建服务器端Socket，并通过runSelectLoop方法等待AMS的请求来创建新的应用程序进程；

（5）启动SystemServer进程；

6、SystemServer进程启动

启动binder线程池；启动SystemServiceManager，并且启动各种系统服务；

（1）启动Binder线程池，用于与其他进程进行通信；

（2）创建SystemServiceManager，用于对系统的其他服务进行创建、启动和生命周期管理；

（3）启动各种系统服务：引导服务、核心服务和其他服务；

其中，引导服务包括：ActivityManagerService、PowerManagerService、PackageManagerService等；

7、Launcher启动

被SystemServer进程启动的AMS会启动Launcher，Launcher启动后会将已安装应用的图标显示到桌面上。

<img src="https://cdn.nlark.com/yuque/0/2021/png/314790/1637579442550-57fd7ede-035a-428b-b194-6d33f36543fe.png"  align="center" width="80%" />

注意：

- init进程是通过解析init.rc文件启动的，是第一个进程；
- Zygote进程是所有Java进程的父进程，所有App进程都是通过Zygote进程fork生成的，避免App进程每次运行都需要重新初始化和启动虚拟机，起到预加载的作用，缩短App进程启动时间；
- SystemServer进程是Zygote进程孵化的第一个进程，负责启动和管理整个Java Framework；
- Launcher进程是Zygote进程孵化的第一个App进程；
- ServiceManager进程比Zygote进程先启动，因为Zygote进程启动后紧接着就会启动SystemServer进程，SystemServer进程会启动各种系统服务，但所有服务都需要在ServiceManager中注册，因此先启动ServiceManager，再启动Zygote进程；



