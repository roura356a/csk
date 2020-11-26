ECI支持Core Dump 
===================================

当程序运行的过程中异常终止或崩溃，操作系统会将程序当时的内存状态记录下来，保存在一个文件中，这种行为就叫做Core Dump（核心转储）。

背景 
-----------------------

在Linux中，当进程在运行过程中因异常终止或崩溃时会发生core dump，即操作系统会自动将进程现场的内存状态保存在本地的一个文件中。"The default action of certain signals is to cause a process to terminate and produce a core dump file, a disk file containing an image of the process's memory at the time of termination"。

Linux中支持core dump的信号如下（Action为Core）：

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4149837951/p107903.png)

ECI支持 
--------------------------

对于ECI的用户，因为VM是托管的，所以不能指定core dump的文件名，以及文件目录，这样就导致即使容器进程异常退出产生了core dump文件也没法离线查看，比如后续的分析等。

比如，在ECI OpenAPI支持自定义之前，core dump本身也是支持：



    / # cd /pod/
    /pod # ls
    data
    /pod # sleep 10
    ^\Quit (core dumped)（按Ctrl+\触发）
    /pod # ls
    core.45  data



可以看到，默认情况下，linux的core dump的文件名是core.pid，而且保存的当前目录。但是容器如果退出了，core文件会随之一起丢失。

而ECI将core dump的文件名以及保存目录的设置能力透给用户，对于用户现在只需要设置一个参数即可：API用户：CorePattern = "/xx/xx/core"。




    CorePattern = "/xx/xx/core"



k8s用户



    annotations:
        k8s.aliyun.com/eci-core-pattern: "/xx/xx/core"



Demo演示 
---------------------------

用户一般产生core dump文件是为了离线分析，所以设置core dump的目录一般会选择外部存储，而不是保存在容器本地目录。目前可选的有云盘、nfs，我们很快也会支持OSS，我们以nfs为例：




    'Volume.1.Name': 'default-volume1',
    'Volume.1.Type': 'NFSVolume',
    'Volume.1.NFSVolume.Path': '/dump/',
    'Volume.1.NFSVolume.Server': '143b24822a-gfn3.cn-beijing.nas.aliyuncs.com',
    
    'Container.1.VolumeMount.1.Name': 'default-volume1',
    'Container.1.VolumeMount.1.MountPath': '/pod/data/dump/',
    
    'CorePattern':'/pod/data/dump/core-eci',



将nfs的目录挂载到容器的/pod/data/dump/目录，然后设置core dump目录为/pod/data/dump/core。这样容器所有的cor dump文件都会自动保存在/pod/data/dump/并且同步到nfs，不随容器释放便于用户做离线分析。

1、我们在容器任意的目录触发core dump：



    / # ls
    bin   dev   etc   home  pod   proc  root  sys   tmp   usr   var
    / # sleep 10
    ^\Quit (core dumped)（按Ctrl+\触发）
    / # ls
    bin   dev   etc   home  pod   proc  root  sys   tmp   usr   var
    / # cd /pod/data/dump/
    /pod/data/dump # ls
    core-eci.12



可以看到core name以及保存目录都生效了。

2、释放该台ECI，登录另外一台挂载该nfs的ECI查看：




    / # cd /pod/data/dump/
    /pod/data/dump # ls
    core-eci.12



core dump文件没有随ECI容器销毁，即可以进行离线分析了。

关于core dump详情请参见[core](https://man7.org/linux/man-pages/man5/core.5.html)。
