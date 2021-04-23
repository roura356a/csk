---
keyword: [云盘数据卷, 手动扩容]
---

# 在ACK中手动扩容云盘数据卷

在容器服务Kubernetes 1.16之前的旧版本中，目前无法实现云盘自动扩容，需要手动扩容。本文介绍如何手动扩容云盘，从而增加云盘空间。

**相关概念**

-   自动扩容

    指只需要修改PVC的Size，而不需手动进行具体扩容操作，即可完成云盘及文件系统的扩容。

-   手动扩容

    指需要手动扩容云盘卷，且手动执行`resize2fs`实现文件系统扩容。

-   在线扩容

    指在业务Pod不暂停的情况下，实现云盘及其文件系统的扩容。

-   离线扩容

    指在业务Pod暂停后，完成云盘卷、文件系统扩容，再启动业务Pod。


云盘数据卷扩容包括以下三部分：

-   扩容云盘存储空间：需要在云盘控制台操作。
-   扩容文件系统：需要所挂载的云盘在ECS节点手动操作。
-   更新PV及PVC的Size（暂不支持）。

    **说明：**

    由于以下原因，建议在集群升级到高版本后再通过自动扩容方式修改PV及PVC的大小，低版本不修改。

    -   不同版本K8s集群更新PV、PVC对象Size值的操作不同。
    -   PV、PVC的Size值不会影响底层存储空间的使用，即：虽然PV、PVC存储空间大小为20 GiB，对应云盘与文件系统存储空间为30 GiB，但是Pod依然可以使用30 GiB的存储空间。

基于稳定性需求，提供以下两种扩容的方式：

-   不暂停应用手动在线扩容：文件系统在线扩容时，如果IO流量高，文件系统存在出现IO报错的风险。但应用可以不重启。
-   暂停应用手动扩容：应用停止后，磁盘IO会暂停，文件系统扩容数据更安全。但应用会暂停一段时间。

## 使用说明

-   **使用限制**

    仅支持扩容2000 GiB以下容量的云盘。

-   **数据备份**

    扩容数据卷前，先执行云盘快照备份数据，以防扩容过程异常导致数据丢失。

-   **适用范围**

    不支持自动扩容的场景：

    -   K8s集群版本小于1.16。
    -   PV类型为静态云盘数据卷。

## 示例应用

本文以Web有状态应用为例，介绍两种扩容方式。执行以下操作，查看云盘使用信息。

-   执行以下命令查看Web应用的Pod。

    ```
    kubectl get pod | grep web
    ```

    预期输出：

    ```
    NAME                               READY   STATUS    RESTARTS   AGE
    web-0                              1/1     Running   0          11h
    web-1                              1/1     Running   0          11h
    ```

-   执行以下命令查看Web应用的PVC。

    ```
    kubectl get pvc | grep web
    ```

    预期输出：

    ```
    NAME             STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS         AGE
    disk-ssd-web-0   Bound    d-0jlhaq***   20Gi       RWO            alicloud-disk-essd   11h
    disk-ssd-web-1   Bound    d-0jl0j5***   20Gi       RWO            alicloud-disk-essd   11h
    ```

-   执行以下命令查看Web应用的PV。

    ```
    kubectl get pv | grep web
    ```

    预期输出：

    ```
    NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS        REASON    AGE
    d-0jl0j5***   20Gi       RWO            Delete           Bound    default/disk-ssd-web-1   alicloud-disk-essd            11h
    d-0jlhaq***   20Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-essd            11h
    ```


从以上预期输出可得：示例应用Web使用了20 GiB的`d-0jl0j5***`和`d-0jlhaq***`云盘，分别挂载在2个Pod上。

关于如何部署Web有状态应用，请参见[创建有状态工作负载StatefulSet](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建有状态工作负载StatefulSet.md)。

## 方式一：不暂停应用手动在线扩容

根据PV信息找到对应的云盘，手动对云盘扩容，然后在云盘对应挂载节点手动执行文件系统扩容。以下示例把2个云盘扩容到30 GiB。

**步骤一：云盘数据卷扩容**

1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

2.  在左侧导航栏，选择**存储与快照** \> **云盘**。

3.  找到Web应用对应的云盘d-0jl0j5\*\*\*及d-0jlhaq\*\*\*，查看云盘状态，并分别在**操作**列单击**更多** \> **云盘扩容**。

4.  在磁盘扩容页面选中**在线扩容**，并输入扩容后容量，例如30 GiB。

    **说明：** 设置的扩容后容量不允许小于当前容量。

5.  确认费用，阅读并选中《云服务器ECS服务条款》后，单击**确认扩容**。

    具体操作，请参见[在线扩容云盘（Linux系统）](/cn.zh-CN/块存储/扩容云盘/在线扩容云盘（Linux系统）.md)。


**步骤二：文件系统扩容**

扩容云盘后需要完成对文件系统的扩容，否则容器内存储空间依然是扩容前的大小。

**说明：** 该步骤是针对于未分区云盘，是K8s中云盘的标准使用方法，不建议在K8s系统中使用分区云盘。

-   若PV使用未分区云盘，则不能手动执行分区操作，否则将会对当前文件系统产生破坏，造成数据丢失。
-   若PV使用分区云盘，则需要扩容分区设备后扩容文件系统。具体操作，请参见[步骤三：查看云盘分区情况](/cn.zh-CN/块存储/扩容云盘/在线扩容云盘（Linux系统）.md)及[步骤四：扩容分区](/cn.zh-CN/块存储/扩容云盘/在线扩容云盘（Linux系统）.mdsection_kmu_sxr_8jk)。

1.  查看当前云盘挂载节点。

    1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

    2.  在左侧导航栏，选择**存储与快照** \> **云盘**。

    3.  找到Web应用对应的云盘d-0jl0j5\*\*\*及d-0jlhaq\*\*\*，分别单击云盘名称。

    4.  在云盘详情页面的**云盘挂载信息**列表下单击**所挂载的实例**。

    5.  在实例详情页签查看实例**网络信息**。

    **说明：** 也可通过在K8s中查看该云盘对应Pod所在的节点。具体操作，请参见[查看容器组（Pod）](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/管理容器组（Pod）.md)。

2.  连接到挂载云盘的ECS实例节点上，获取云盘设备符。

    关于如何连接到ECS实例节点，请参见[连接方式概述](/cn.zh-CN/实例/连接实例/连接方式概述.md)。

    可通过以下方法获取云盘设备符。

    -   [获取云盘设备符](#step_3q5_c6f_yyn)。
    -   执行以下命令获取云盘d-0jlhaq\*\*\*的设备符。

        ```
        # 查询 {pv-name}
        mount |grep d-0jlhaq*** 
        ```

        预期输出：

        ```
        /dev/vdc on /var/lib/kubelet/plugins/kubernetes.io/csi/pv/d-0jlhaq***/globalmount type ext4 (rw,relatime)
        /dev/vdc on /var/lib/kubelet/pods/a26d174f-***/volumes/kubernetes.io~csi/d-0jlhaq***/mount type ext4 (rw,relatime)
        ```

        从以上预期输出可得：所挂载的云盘`d-0jlhaq***`在当前节点的设备符为/dev/vdc。

3.  执行以下命令扩容文件系统。

    ```
    resize2fs /dev/vdc
    ```

    **说明：** /dev/vdc为[步骤2](#step_3hm_hlz_fsv)获取的云盘设备符。

    预期输出：

    ```
    resize2fs 1.43.5 (04-Aug-2017)
    Filesystem at /dev/vdc is mounted on /var/lib/kubelet/plugins/kubernetes.io/csi/pv/d-0jlhaq***/globalmount; on-line resizing required
    old_desc_blocks = 3, new_desc_blocks = 4
    The filesystem on /dev/vdc is now 7864320 (4k) blocks long.
    ```

4.  执行以下命令验证文件系统扩容结果。

    ```
    lsblk /dev/vdc
    ```

    预期输出：

    ```
    NAME MAJ:MIN  RM SIZE RO TYPE MOUNTPOINT
    vdc  254:32   0  30G  0  disk /var/lib/kubelet/pods/a26d174f-***/volumes/kubernetes.io~csi/d-0jlhaq***/mount
    ```

    从以上预期输出可得，vdc文件系统存储空间为30 GiB。


## 方式二：暂停应用手动扩容

通过删除StatefulSet或者将Replica配置为0的方式暂停应用，手动对每个依赖数据盘扩容，然后再启动应用。通过以下示例把2个云盘扩容到30 GiB。

**步骤一：删除应用Pod**

1.  执行以下命令将Pod缩容为0个副本。

    ```
    kubectl scale sts web --replicas=0
    ```

    预期输出：

    ```
    statefulset.apps/web scaled
    ```

2.  执行以下命令查看Pod是否删除。

    ```
    kubectl get pod | grep web
    ```

    无输出，代表已删除Web应用。


**步骤二：云盘数据卷扩容**

1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

2.  在左侧导航栏，选择**存储与快照** \> **云盘**。

3.  找到Web应用对应的云盘d-0jl0j5\*\*\*及d-0jlhaq\*\*\*，查看云盘状态，并分别在**操作**列选择**更多** \> **云盘扩容**。

4.  在磁盘扩容页面配置扩容方式及扩容后容量。

    -   若云盘处于**待挂载**状态，在磁盘扩容页面无需选中**在线扩容**，输入扩容后容量，例如30 GiB。
    -   若云盘处于**使用中**状态，在磁盘扩容页面选中**在线扩容**，并输入扩容后容量。
    **说明：** 设置的扩容后容量不允许小于当前容量。

5.  确认费用，阅读并选中《云服务器ECS服务条款》后，单击**确认扩容**。

    具体操作，请参见[在线扩容云盘（Linux系统）](/cn.zh-CN/块存储/扩容云盘/在线扩容云盘（Linux系统）.md)。


**步骤三：文件系统扩容**

扩容云盘后需要完成对文件系统的扩容，否则容器内存储空间依然是扩容前的大小。

**说明：** 该步骤是针对于未分区云盘，是K8s中云盘的标准使用方法，不建议在K8s系统中使用分区云盘。

-   若PV使用未分区云盘，则不能手动执行分区操作，否则将会对当前文件系统产生破坏，造成数据丢失。
-   若PV使用分区云盘，则需要扩容分区设备后扩容文件系统。具体操作，请参见[步骤三：查看云盘分区情况](/cn.zh-CN/块存储/扩容云盘/在线扩容云盘（Linux系统）.md)及[步骤四：扩容分区](/cn.zh-CN/块存储/扩容云盘/在线扩容云盘（Linux系统）.mdsection_kmu_sxr_8jk)。

1.  将待挂载云盘挂载到某个ECS节点。

    **说明：** 需要将云盘挂载到ECS节点上才可实现文件系统扩容。

    1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

    2.  在左侧导航栏，选择**存储与快照** \> **云盘**。

    3.  对于**待挂载**状态的云盘，在**操作**列单击**更多** \> **挂载**。

    4.  在挂载云盘对话框，选择目标实例并设置云盘相关释放行为。

        |参数|说明|
        |--|--|
        |目标实例|选择需要挂载的ECS实例。|
        |云盘随实例释放|选中此选项，释放实例时会自动释放此云盘。 如果未选中，当实例被释放时该云盘会被保留下来。**说明：** 如果您挂载的是从其他实例卸载的系统盘，**云盘随实例释放**中的实例指系统盘被卸载前的源ECS实例，并非当前操作的实例。 |
        |自动快照随云盘释放|选中此选项，当云盘释放时该云盘创建的自动快照都会一起释放。建议您不要选择该选项，以便保留备份数据。|

    5.  单击**执行挂载**。

    如果该云盘的状态变为**使用中**，表示挂载成功。

2.  连接到挂载云盘的ECS实例节点上，获取云盘设备符。

    关于如何连接到ECS实例节点，请参见[连接方式概述](/cn.zh-CN/实例/连接实例/连接方式概述.md)。

    -   执行以下命令获取云盘设备符。

        ```
        for device in `ls /sys/block | grep vd`; do 
          cat /sys/block/$device/serial | grep 0jlhaq*** && echo $device; 
        done
        ```

        **说明：** `0jlhaq***`为扩容云盘ID`d-0jlhaq***`中`d-`后的字符串。

    -   若上述操作无法获取到云盘设备符，请按以下操作获取。
        1.  将目标云盘卸载后执行`ls /dev/vd*`命令查看云盘列表。
        2.  将目标云盘挂载后执行`ls /dev/vd*`命令查看云盘列表。
        3.  比较上述获取的云盘列表，多出来的云盘即为目标云盘。
3.  执行以下命令扩容文件系统。

    ```
    resize2fs /dev/vdb
    ```

    **说明：** /dev/vdb为[步骤2](#step_3q5_c6f_yyn)获取的云盘设备符。

    预期输出：

    ```
    resize2fs 1.43.5 (04-Aug-2017)
    Resizing the filesystem on /dev/vdb to 7864320 (4k) blocks.
    The filesystem on /dev/vdb is now 7864320 (4k) blocks long.
    ```

4.  验证文件系统扩容结果。

    1.  执行以下命令创建临时文件夹/mnt/disk/，并将云盘挂载到临时文件夹上。

        ```
        mkdir /mnt/disk
        mount /dev/vdb /mnt/disk/
        ```

    2.  执行以下命令查看文件系统大小。

        ```
        df /mnt/disk/
        ```

        预期输出：

        ```
        Filesystem     1K-blocks  Used Available Use% Mounted on
        /dev/vdb        30832548 45036  30771128   1% /mnt/disk
        ```

        从以上预期输出可得：临时文件夹/dev/vdb的内存为30 GiB，说明已经扩容成功。

    3.  执行以下命令将云盘从临时文件夹卸载。

        ```
        umount /mnt/disk
        ```


**步骤四：重启应用**

1.  执行以下命令将Pod扩容为2个副本。

    ```
    kubectl scale sts web --replicas=2
    ```

    预期输出：

    ```
    statefulset.apps/web scaled
    ```

2.  执行以下命令查看Pod是否删除。

    ```
    kubectl get pod | grep web
    ```

    预期输出：

    ```
    NAME         READY   STATUS    RESTARTS   AGE
    web-0        1/1     Running   0          74s
    web-1        1/1     Running   0          42s
    ```

3.  执行以下命令查看文件系统大小。

    ```
    kubectl exec web-0 df /data
    ```

    预期输出：

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    从以上预期输出可得，/dev/vdb文件系统存储空间为30 GiB。


## 常见问题

问题现象：

在执行`resize2fs`命令之后出现如下错误提示。

```
resize of device /dev/xxx failed: exit status 1 resize2fs output: resize2fs xxx(version)
Please run `e2fsck -f /dev/xxx` first
```

问题原因：

因为不满足文件系统的一致性导致IO崩溃。

解决方案：

请按照提示执行`e2fsck -f /dev/xxx`后再尝试扩容。

**相关文档**  


[在ACK中自动扩容CSI云盘数据卷](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/在ACK中自动扩容CSI云盘数据卷.md)

