---
keyword: [Windows节点, SMB, 存储资源, 云盘]
---

# 在Windows容器中使用云盘及基于SMB的文件存储

您可以在ACK Windows容器中使用云盘动态存储卷及SMB静态存储资源。本文介绍如何在Windows容器中为Windows容器配置阿里云云盘及基于SMB的文件存储。

-   已开通Flexvolume存储插件的使用权限。

    若未开通Flexvolume存储插件的使用权限，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请配置白名单。

-   已创建Kubernetes集群，且创建集群时使用的存储插件为Flexvolume。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建Windows节点池。具体操作，请参见[创建Windows节点池](/cn.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)。
-   已通过kubectl工具连接Kubernetes集群。具体操作，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 为Windows容器配置阿里云云盘

阿里云云盘是为阿里云服务器（ECS）提供的一种块存储服务，它具有低时延，高性能，耐用性好及高可靠等特点。

您可以通过以下步骤为Windows容器配置阿里云云盘。

**步骤一：创建存储类StorageClass**

云盘控制器（Cloud Disk Controller）根据存储类（StorageClass）的参数fstype的值判断创建的云盘适用于Windows文件系统或Linux文件系统。

-   当参数fstype的值为ext3、ext4或xfs时，表示该StorageClass适用于Linux文件系统。
-   当参数fstype的值为ntfs时，表示该StorageClass适用于Windows文件系统。

默认情况下，若存储插件选择为Flexvolume，则在ACK集群创建时会定义Linux文件系统适用的存储类（StorageClass），而Windows文件系统适用的存储类需要自定义。

1.  使用以下YAML示例自定义Windows文件系统适用的存储类（StorageClass），并创建storageclass.yaml文件。



    以下为一些StorageClass的配置示例说明：

    |YAML示例|说明|
    |------|--|
    |    ```
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: alicloud-disk-ssd-windows
  provisioner: alicloud/disk
  parameters:
    type: cloud_ssd
    fstype: ntfs
+ allowVolumeExpansion: true # useless
    ```

|目前Windows不支持盘区扩展。例如，配置的`allowVolumeExpansion: true`并不能起到预期扩展云盘的作用。|
    |    ```
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: alicloud-disk-ssd-windows
  provisioner: alicloud/disk
  parameters:
    type: cloud_ssd
    fstype: ntfs
+ reclaimPolicy: Retain # only unmount
    ```

|根据需要使用保留回收策略`reclaimPolicy: Retain`可以减少由于错误操作导致数据删除的问题。|
    |    ```
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: alicloud-disk-ssd-windows
  provisioner: alicloud/disk
  parameters:
    type: cloud_ssd
    fstype: ntfs
+   regionId: cn-hangzhou
+   zoneId: cn-hangzhou-b
    ```

|若缺少地域及可用区的定义，云盘可能会挂载失败。使用参数`regionId`及`zoneId`指定地域及可用区可以避免挂载失败。|

2.  执行以下命令创建Windows环境下的StorageClass。

    ```
    kubectl create -f storageclass.yaml
    ```


**步骤二：为Windows节点安装Flexvolume插件**

1.  使用以下YAML示例创建flexvolume-windows.yaml文件。

2.  执行以下命令为所有的Windows工作节点安装Flexvolume插件。

    ```
    kubectl create -f flexvolume-windows.yaml
    ```

3.  DaemonSet部署成功后，执行以下命令可以在每个WIndows节点上查看FlexVolume插件alicloud-disk-controller。

    ```
    ls C:\usr\libexec\kubernetes\kubelet-plugins\volume\exec\alicloud~disk.exe\
    ```

    预期输出：

    ```
    Directory: C:\usr\libexec\kubernetes\kubelet-plugins\volume\exec\alicloud~disk.exe
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----        8/12/2021   2:45 PM        5636096 disk.exe
    ```


**步骤三：创建云盘并挂载到Windows容器中**

以有状态工作负载（StatefulSet）为例，您可以在volumeClaimTemplates中定义需要的存储类（StroageClass），Windows工作节点上的FlexVolume插件会自动完成所有操作：验证您的账户信息，创建阿里云云盘，并挂载到阿里云服务器上，最后挂载到Windows容器中。

StatefulSet部署完成后，执行以下命令可查看到持久化存储卷（PersitentVolume）资源对象。

```
kubectl get pv
```

预期输出：

```
NAME                     STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS                AGE
ssd-web-windows-disk-0   Bound    d-wz9dihqdkusysc56****   50Gi       RWO            alicloud-disk-ssd-windows   5s
```

**步骤四：验证云盘的持久化存储**

在持久化存储卷（PersitentVolume）的支持下，部署的工作负载在被删除后，其访问日志仍然可以被保留。

1.  执行以下命令访问部署的Windows应用**web-windows-disk**。

    ```
    for i in {1..2}; do; curl $(kubectl get svc web-windows-disk --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

2.  执行以下命令查看**web-windows-disk-0**应用的日志。

    ```
    kubectl logs web-windows-disk-0
    ```

    预期输出：

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

3.  执行以下命令删除**web-windows-disk-0**应用。

    ```
    kubectl delete pod web-windows-disk-0
    ```

4.  执行以下命令访问重新部署的Windows应用**web-windows-disk**。

    ```
    for i in {1..3}; do; curl $(kubectl get svc web-windows-disk --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

5.  执行以下命令查看**web-windows-disk-0**应用的日志。

    ```
    kubectl logs web-windows-disk-0
    ```

    预期输出：

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:41 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

    从前后两次**web-windows-disk-0**应用日志的对比可得，部署的工作负载**web-windows-disk**在被删除后，其访问日志仍然被保留。


## 为Windows容器配置基于SMB的文件存储

阿里云为ECS提供阿里云文件存储NAS的分布式文件系统。阿里云文件存储NAS基于POSIX文件接口，天然适配原生操作系统，提供共享访问，同时保证数据一致性和锁互斥。

SMB（Server Message Block）是阿里云文件存储NAS实现的一种协议，在Windows平台上工作良好。和阿里云云盘FlexVolume插件不同的是，使用SMB Flexvolume插件并不依赖Linux工作节点或Kubernetes控制器。

没有Kubernetes控制器意味着SMB FlexVolume插件仅能处理静态的持久化存储卷（PersitentVolume）资源对象，而无法利用存储类（StorageClass）动态分配卷。

**步骤一：为Windows节点安装Flexvolume插件**

为所有的Windows工作节点安装Flexvolume插件。具体操作，请参见[为Windows容器配置阿里云云盘](#section_k1m_mb2_90c)的步骤二：为Windows节点安装Flexvolume插件。

**步骤二：使用SMB创建持久卷并挂载到Windows容器中**

1.  创建持久化存储卷。

    使用以下YAML示例创建PV及PVC，向阿里云文件系统NAS申请10 GiB的文件存储。

    配置项的部分参数说明如下所示：

    |参数|说明|
    |--|--|
    |driver|挂载存储卷的驱动类型，必须为`alicloud/smb.exe`。|
    |server|NAS文件系统中挂载点的挂载地址，必须与Windows节点的VPC在同一地域。|
    |path|NAS在容器中挂载的位置，必须以/myshare/为父目录并创建子目录。例如，/myshare/web-windows-logs。|
    |user|登录ECS的用户名，建议使用**workgroup/administrator**。|
    |password|登录ECS的用户密码。|

2.  使用以下YAML示例创建Service及Deployment，并挂载已创建的持久化存储卷。

3.  Deployment部署完成后，执行以下命令查看应用及持久化存储卷（PersitentVolume）资源对象。

    -   执行以下命令查看部署的工作负载Deployment。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                               READY   STATUS              RESTARTS   AGE
        web-windows-smb-584676df65-t4mmh   1/1     Running             0          26d
        ```

    -   执行以下命令查看持久化存储卷（PersitentVolume）资源对象。

        ```
        kubectl get pv
        ```

        预期输出：

        ```
        NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                       STORAGECLASS      REASON   AGE
        pv-smb-10g   10Gi       ReadWriteMany  Retain           Bound       default/web-windows-smb                                10d
        ```


**步骤四：验证NAS的持久化存储**

在持久化存储卷（PersitentVolume）的支持下，部署的工作负载在被删除后，其访问日志仍然可以被保留。

1.  执行以下命令访问部署的Windows应用**web-windows-smb**。

    ```
    for i in {1..2}; do; curl $(kubectl get svc web-windows-smb --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

2.  执行以下命令查看**web-windows-smb-584676df65-t4mmh**应用的日志。

    ```
    kubectl logs web-windows-smb-584676df65-t4mmh
    ```

    预期输出：

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

3.  执行以下命令删除**web-windows-smb-584676df65-t4mmh**应用。

    ```
    kubectl delete pod web-windows-smb-584676df65-t4mmh
    ```

4.  执行以下命令访问重新部署的Windows应用**web-windows-smb**。

    ```
    for i in {1..3}; do; curl $(kubectl get svc web-windows-smb --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

5.  执行以下命令查看应用。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                               READY   STATUS              RESTARTS   AGE
    web-windows-smb-584676df65-87bpz   1/1     Running             0          26d
    ```

6.  执行以下命令查看**web-windows-smb-584676df65-87bpz**应用的日志。

    ```
    kubectl logs web-windows-smb-584676df65-87bpz
    ```

    预期输出：

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:41 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

    从前后两次**web-windows-smb-\*\*\*\***应用日志的对比可得，部署的工作负载**web-windows-smb-584676df65-t4mmh**在被删除后，其访问日志仍然被保留。


