# 为容器服务的Docker增加数据盘 {#concept_opp_flh_4gb .concept}

Docker的数据通过联合文件系统的方式存储到磁盘上。当需要在机器上运行的容器或者镜像数量不断增加时，磁盘的大小可能不再满足需求，您需要通过增加数据盘的方式对Docker的数据目录进行扩容。

## Docker 数据目录 {#section_s2h_wlh_4gb .section}

Docker默认的容器和镜像数据存储在/var/lib/docker目录下。您可以通过du命令查看这个目录目前占用的磁盘大小。

```
# du -h --max-depth=0 /var/lib/docker
7.9G    /var/lib/docker
```

## 更换Docker的数据盘 {#section_fwg_mvh_4gb .section}

很多Docker镜像较大，因此可能几个镜像就会占用大量磁盘空间。较大的镜像或较多的容器，都会导致磁盘空间的不足。为了满足您增加镜像或容器的需求，您需要为Docker的数据目录增加数据盘。

## 增加数据盘 {#section_ivb_xxh_4gb .section}

完成以下步骤，为Docker的数据目录增加数据盘：

1.  购买ECS数据盘，并挂载到需要扩容的机器上。
    1.  通过[云服务器 ECS 控制台](https://ecs.console.aliyun.com/)购买需要配置的云盘。
    2.  单击左侧导航栏中的**实例**。
    3.  单击ECS实例ID，进入实例详情页。
    4.  单击左侧导航栏中的**本实例磁盘**。
    5.  在右侧的**磁盘列表**中，选择刚才购买的磁盘，并记录挂载点`/dev/xvd*`或者`/dev/vd*`。
2.  登录ECS实例，对刚才挂载的磁盘进行格式化。
    1.  执行ls -l /dev/xvd\*或者ls -l /dev/vd\*命令，得到和刚才记录一致的磁盘ID。
    2.  通过fdisk命令对磁盘进行分区，然后使用mkfs.ext4命令格式化磁盘。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119956/154873959238212_zh-CN.png)

3.  移动Docker数据到新的磁盘。

    如果不希望中断节点上正在运行的应用，您需要对应用进行迁移。迁移swarm集群，请参考[指定多节点调度](../../../../../intl.zh-CN/用户指南/应用管理/指定多节点调度.md#)。迁移Kubernetes集群，请参考[Safely Drain a Node while Respecting Application SLOs](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)。

    1.  首先停止Docker Daemon和kubelet（Kubernetes集群），保证迁移时的数据完整。可以使用service kubelet stop\(Kubernetes集群\)和service docker stop 命令进行停止。
    2.  先移动Docker的目录到一个备份的目录。例如：mv /var/lib/docker /var/lib/docker\_data。
    3.  然后把新的格式化好的磁盘挂载到/var/lib/docker和/var/lib/kubelet目录。例如：

        ```
        echo "/dev/xvdb1    /var/lib/container/     ext4    defaults        0 0" >>/etc/fstab
        echo "/var/lib/container/kubelet /var/lib/kubelet none defaults,bind 0 0" >>/etc/fstab
        echo "/var/lib/container/docker /var/lib/docker none defaults,bind 0 0" >>/etc/fstab
        
        
        mkdir /var/lib/docker
        
        mount -a
        ```

    4.  把之前备份的Docker数据移动到新的磁盘上。例如：mv /var/lib/docker\_data/\* /var/lib/docker/。
4.  启动Docker Daemon和kubelet\(Kubernetes集群\)，并检查数据位置。
    1.  启动Docker Daemon和kubelet\(Kubernetes集群\)，命令分别是service docker start和service kubelet start\(Kubernetes集群\)。
    2.  执行df命令，可看到/var/lib/docker挂载到了新的磁盘上。如果需要启动Kubernetes集群，请跳过此步骤。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119956/154873959238133_zh-CN.png)

    3.  执行 docker ps命令，查看容器是否丢失。根据需要，重启相关容器。如没有设置`restart:always`标签的容器。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119956/154873959238134_zh-CN.png)

5.  您可以通过调度的方式使被迁移走的容器回归到这个节点上来。

更多容器服务的相关内容，请参考[容器服务](https://www.alibabacloud.com/zh/product/container-service)。

