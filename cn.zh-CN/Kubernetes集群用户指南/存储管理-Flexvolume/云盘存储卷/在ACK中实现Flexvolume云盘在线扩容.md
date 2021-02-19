---
keyword: [Kubernetes 1.16, 云盘的动态扩容]
---

# 在ACK中实现Flexvolume云盘在线扩容

在Kubernetes 1.16版中，云盘数据卷在线扩容已经进入Beta阶段，ACK支持在Kubernetes 1.16及以上版本中实现Flexvolume云盘在线扩容。本文介绍在Flexvolume环境中如何进行云盘的动态扩容，从而增加云盘空间。

云盘数据卷扩容包括**云盘扩容**和**文件系统扩容**两部分，云盘和文件系统都支持应用在线扩容（设备mount到目录状态下扩容），但是从文件系统扩容最佳实践角度，最稳定的扩容方案是先停止应用层服务、解除挂载目录，再进行数据卷扩容。

基于稳定性需求，提供以下两种扩容的方式：

-   Pod不重启实现数据卷扩容：在负载高IO情况下，有小概率造成文件系统问题。
-   Pod重启时实现数据卷扩容：应用停止后，扩容更安全。

从Kubernetes 1.16开始默认实现了Pod不重启即可以实现数据卷扩容。

## 使用说明

-   **使用限制**

    仅支持在线扩容2000 GiB以下容量的云盘。

-   **数据备份**

    扩容数据卷前，先备份云盘快照，以防扩容过程异常导致数据出现问题。

-   **适用范围**
    -   只有动态存储卷才可以进行数据卷动态扩容，即配置了StorageClassName的PV。
    -   不支持扩容InlineVolume类型（非PV、PVC方式）的云盘数据卷。
    -   不支持动态扩容普通云盘类型。
    -   StorageClass需要配置为**AllowVolumeExpansion: True**。ACK集群默认提供的storageclass为True，自建的storageclass需要自行配置。
-   **插件版本**

    升级您使用Flexvolume或者CSI插件到最新版本。


## 为RAM用户添加ResizeDisk权限

实现云盘在线扩容前，您需要为集群的**RAM角色**添加ResizeDisk权限。针对不同集群和插件类型，添加ResizeDisk权限的步骤如下：

**专有集群（CSI插件）**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选中目标集群，并在目标集群右侧**操作**列下，单击**详情**。

4.  在集群管理页左侧导航栏中，单击**集群信息**。

5.  单击**集群资源**页签，单击**Master RAM角色**链接。

6.  在RAM控制台中，添加ResizeDisk权限。添加权限具体步骤，请参见[修改自定义策略内容](/cn.zh-CN/权限策略管理/自定义策略/修改自定义策略内容.md)。

    ![resizedisk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4785659951/p101021.jpg)


**专有集群（Flexvolume插件）、托管集群**

参照上述步骤，并将上述步骤[5](#d6e117)中的单击**Master RAM 角色**链接改成单击**Worker RAM 角色**链接。

## 不重启容器实现在线扩容

1.  [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

    本文假设应用的当前状态如下。

    执行以下命令获取Pod信息。

    ```
    kubectl get pod
    ```

    返回结果如下：

    ```
    web-0         1/1     Running   0          42s
    ```

    执行以下命令查看Pod的挂载详情。

    ```
    kubectl exec web-0 df /data
    ```

    返回结果如下：

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    执行以下命令获取PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            alicloud-disk-available   57s
    ```

    执行以下命令获取PV信息。

    ```
    kubectl get pv
    ```

    返回结果如下：

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            Delete           Bound      default/disk-ssd-web-0   alicloud-disk-available            65s
    ```

2.  在符合[使用说明](/cn.zh-CN/Kubernetes集群用户指南/存储管理-CSI/云盘存储卷/在ACK中实现CSI云盘在线扩容.md)的各个条件下，执行以下命令进行数据卷扩容。

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

    等待一定时间（一分钟以内）后扩容完成，检查状态如下。

    执行以下命令获取PV信息。

    ```
    kubectl get pv d-wz9hpoifm43yn9zie6gl
    ```

    返回结果如下：

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            5m23s
    ```

    执行以下命令获取PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            alicloud-disk-available   5m10s
    ```

    执行以下命令查看Pod的挂载详情。

    ```
    kubectl exec web-0 df /data
    ```

    返回结果如下：

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    您只需要执行一条命令即可完成所有的扩容操作。


## 重启容器实现在线扩容

1.  使用命令行工具连接Kubernetes集群，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

    本文假设应用的当前状态如下。

    执行以下命令获取Pod信息。

    ```
    kubectl get pod
    ```

    返回结果如下：

    ```
    web-0         1/1     Running   0          42s
    ```

    执行以下命令查看Pod的挂载详情。

    ```
    kubectl exec web-0 df /data
    ```

    返回结果如下：

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    执行以下命令获取PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            alicloud-disk-available   57s
    ```

    执行以下命令获取PV信息。

    ```
    kubectl get pv
    ```

    返回结果如下：

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            Delete           Bound      default/disk-ssd-web-0   alicloud-disk-available            65s
    ```

2.  执行以下命令查看PV的调度信息。

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4  -oyaml | grep failure-domain.beta.kubernetes.io/zone
        failure-domain.beta.kubernetes.io/zone: cn-shenzhen-e
    ```

3.  修改调度信息label，在zone后面添加字段，促使这个PV关联的Pod不可调度。

    例如，把以下示例中原来的cn-shenzhen-e改为cn-shenzhen-e-nozone。

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone=cn-shenzhen-e-nozone --overwrite
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

4.  重启Pod。

    由于修改了调度信息，Pod会暂时Pending。

    执行以下命令删除Pod。

    ```
    kubectl delete pod web-0
    ```

    执行以下命令获取Pod信息。

    ```
    kubectl get pod
    ```

    返回结果如下：

    ```
    web-0   0/1     Pending   0          27s
    ```

5.  执行以下命令扩容数据卷。

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

6.  将PV的调度信息label改回去，促使Pod启动完成（把cn-shenzhen-e-nozone改为起初的cn-shenzhen-e）。

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone=cn-shenzhen-e --overwrite
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

    等待一定时间（一分钟内）后检查扩容是否符合预期。

    执行以下命令获取Pod信息。

    ```
    kubectl get pod
    ```

    返回结果如下：

    ```
    web-0   1/1     Running   0          3m23s
    ```

    执行以下命令获取PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    disk-ssd-web-0   Bound    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            alicloud-disk-available   17m
    ```

    执行以下命令获取PV信息。

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4
    ```

    返回结果如下：

    ```
    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            17m
    ```

    执行以下命令查看Pod的挂载详情。

    ```
    kubectl exec web-0 df /data
    ```

    返回结果如下：

    ```
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    从返回结果中可以看出云盘由20 GiB成功被扩容至30 GiB。


