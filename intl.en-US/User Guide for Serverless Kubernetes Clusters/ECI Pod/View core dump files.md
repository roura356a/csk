View core dump files 
=========================================

This topic describes how to configure the directory where a core dump file is saved. You can view and analyze a core dump file when a container is terminated abnormally. This helps you to identify the cause of the issue. 

Background information 
-------------------------------------------

In Linux, if a program suddenly terminates or unexpectedly exits, the operating system records the state of the working memory of the program at that time and saves it in a file. This process is called a core dump. You can view and analyze the core dump file to identify the cause of the issue. 

The following figure shows some signals that cause a core dump in Linux. These signals have a value of Core in the Action column. 

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8044183161/p107903.png)For more information, visit [Core dump file](http://man7.org/linux/man-pages/man5/core.5.html).

Description 
--------------------------------

For Elastic Container Instance, the names and directories of core dump files cannot be specified because the elastic container instances are managed. By default, the name of a core dump file is core.pid, and the file is saved in the current directory. 



    / # cd /pod/
    /pod # ls
    data
    /pod # sleep 10
    ^\Quit (Core dumped) (Press Ctrl+\ to trigger a core dump)
    /pod # ls
    core.45  data



Even if a core dump file is generated when a container process abnormally exits, the file is lost due to the container exit. Therefore, you cannot view the file offline for subsequent analysis. 

Elastic Container Instance allows you to specify the directories in which to save core dump files to solve the preceding problems. You can save core dump files in external volumes. Then, you can still obtain a core dump file for offline viewing and analysis even if the corresponding container exits. 

You can use one of the following configuration methods:

* Kubernetes

      annotations:
          k8s.aliyun.com/eci-core-pattern: "/xx/xx/core"

  

* API operation

      CorePattern = "/xx/xx/core"

  




Configuration examples 
-------------------------------------------

Typically, core dump files are analyzed offline. Therefore, external volumes are preferred over local directories of the containers to save core dump files. You can use disks or network file system (NFS) file systems as external volumes. An NFS file system is used in the following example. 

1. Mount an NFS file system and configure the directory where core dump files are saved. 

   In the following example, the NFS file system is mounted to the /pod/data/dump/ directory of the container and the /pod/data/dump/core directory is used to save core dump files. In this case, all core dump files of the container are automatically saved to the directory and synchronized to the NFS file system. You can obtain the core dump files from the NFS file system for analysis even if the container is released. 

       'Volume.1.Name': 'default-volume1',
       'Volume.1.Type': 'NFSVolume',
       'Volume.1.NFSVolume.Path': '/dump/',
       'Volume.1.NFSVolume.Server': '143b24****-gfn3.cn-beijing.nas.aliyuncs.com',
       
       'Container.1.VolumeMount.1.Name': 'default-volume1',
       'Container.1.VolumeMount.1.MountPath': '/pod/data/dump/',
       
       'CorePattern':'/pod/data/dump/core-eci',

   

2. Trigger a core dump in a directory of the container. 

   The following example shows that the name and directory of the core dump file have taken effect. 

       / # ls
       bin   dev   etc   home  pod   proc  root  sys   tmp   usr   var
       / # sleep 10
       ^\Quit (core dumped) (Press Ctrl+\ to trigger a core dump)
       / # ls
       bin   dev   etc   home  pod   proc  root  sys   tmp   usr   var
       / # cd /pod/data/dump/
       /pod/data/dump # ls
       core-eci.12

   

3. Release the elastic container instance.

   

4. Mount the NFS file system to another elastic container instance and log on to the elastic container instance to view a core dump file. 

   The following example shows that the core dump file is not lost, and you can view and analyze the file. 

       / # cd /pod/data/dump/
       /pod/data/dump # ls
       core-eci.12

   




