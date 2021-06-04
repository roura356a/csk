查看Core dump文件 
==================================

本文介绍如何设置Core dump文件的保存目录，以便在容器异常终止时查看分析Core dump文件，找出问题原因。

背景信息 
-------------------------

在Linux中，如果程序突然异常终止或者崩溃时，操作系统会将程序当时的内存状态记录下来，保存在一个文件中，这种行为就叫做Core dump。此时，您可以查看分析Core dump文件，找出问题原因。

Linux中支持Core dump（Action为Core）的Signal如下图所示。

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3870083161/p107903.png)更多信息，请参见[Core dump file](http://man7.org/linux/man-pages/man5/core.5.html)。

功能概述 
-------------------------

对于ECI来说，因为VM是托管的，所以无法指定Core dump文件的文件名和文件目录。默认情况下，Core dump文件的名称为core.pid，保存在当前目录下。



    / # cd /pod/
    /pod # ls
    data
    /pod # sleep 10
    ^\Quit (Core dumped)（按Ctrl+\触发）
    /pod # ls
    core.45  data



即使容器进程异常退出产生了Core dump文件，但Core dump文件会随容器退出而丢失，因此也无法离线查看Core dump文件，进行后续的分析。

针对上述情况，ECI支持自定义设置Core dump文件的保存目录。您可以将Core dump文件保存到外挂存储中，即使容器退出了，也可以获取到Core dump文件进行离线查看和分析。

配置方式如下：
**注意**

配置的路径不能以`|`开头，即不能通过Core dump来配置可执行程序。

* Kubernetes

      annotations:
          k8s.aliyun.com/eci-core-pattern: "/xx/xx/core"

  

* OpenAPI

      CorePattern = "/xx/xx/core"

  




配置示例 
-------------------------

对于Core dump文件，一般是为了离线分析，因此在设置Core dump文件的保存目录时，一般会选择外挂存储，而不是保存在容器本地目录。目前支持的外挂存储包括云盘、NFS等。下文以NFS为例进行介绍。

1. 挂载NFS，并设置Core dump文件的保存目录。

   如下示例，将NFS挂载到容器的/pod/data/dump/目录，然后设置Core dump文件的保存目录为：/pod/data/dump/core。此时，容器的所有Core dump文件将自动保存到该目录下，并同步到NFS。即使该容器释放了，您仍可以从NFS中获取Core dump文件进行分析。

       apiVersion: v1
       kind: Pod
       metadata:
         name: test
         annotations:
           k8s.aliyun.com/eci-core-pattern: "pod/data/dump/core" 
       spec:
         containers:
         - image: nginx:latest
           name: test-container
           volumeMounts:
           - mountPath: /pod/data/dump/
             name: default-volume
         volumes:
         - name: default-volume
           nfs:
             server: 143b24****-gfn3.cn-beijing.nas.aliyuncs.com
             path: /dump/
             readOnly: false

   

2. 在容器任意的目录下触发Core dump。

   如下示例，可以看到设置的Core dump文件的名称和保存目录已经生效。

       / # ls
       bin   dev   etc   home  pod   proc  root  sys   tmp   usr   var
       / # sleep 10
       ^\Quit (core dumped)（按Ctrl+\触发）
       / # ls
       bin   dev   etc   home  pod   proc  root  sys   tmp   usr   var
       / # cd /pod/data/dump/
       /pod/data/dump # ls
       core.12

   

3. 释放该台ECI实例。

   

4. 将NFS挂载到新的一台ECI实例上，然后登录实例查看Core dump文件。

   如下示例，可以看到Core dump文件并没有丢失，您可以进行查看分析。

       / # cd /pod/data/dump/
       /pod/data/dump # ls
       core.12

   




