---
keyword: [云盘存储, NAS存储, FAQ]
---

# 存储FAQ-CSI

本文为您介绍存储常见问题的分析流程，云盘存储卷与NAS存储卷常见问题的处理方法。

|类型|问题|
|--|--|
|常见问题分析流程|[常见问题分析流程](#section_1ym_k67_i1r)|
|云盘存储卷常见问题|-   [使用云盘时提示The specified disk is not a portable disk](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [启动挂载了云盘的Pod时提示had volume node affinity conflict](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [启动挂载了云盘的Pod时提示can't find disk](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [动态创建PV失败且提示The specified AZone inventory is insufficient](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [动态创建PV失败且提示disk size is not supported](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [磁盘阻塞导致Pod长时间无法启动](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [启动Pod时出现FailedMount警告](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [Pod卸载失败且Kubelet出现不受ACK管理的Pod日志](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md)
-   [Pod删除后无法重新启动，提示挂载失败且无法自动恢复。](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷FAQ.md) |
|NAS存储卷常见问题|-   [使用NAS时，提示chown: Operation not permitted](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/NAS存储卷FAQ.md)
-   [使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md)
-   [NAS存储卷挂载时间延长](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/NAS存储卷FAQ.md) |
|OSS存储卷常见问题|[OSS存储卷挂载时间延长](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/OSS存储卷FAQ.md)|

## 常见问题分析流程

通过以下操作查看对应存储插件日志文件，明确问题现象。

1.  执行以下命令查看PVC或Pod上是否有明显的Event。

    ```
    kubectl get events
    ```

    预期输出：

    ```
    LAST SEEN   TYPE      REASON                 OBJECT                                                  MESSAGE
    2m56s       Normal    FailedBinding          persistentvolumeclaim/data-my-release-mariadb-0         no persistent volumes available for this claim and no storage class is set
    41s         Normal    ExternalProvisioning   persistentvolumeclaim/pvc-nas-dynamic-create-subpath8   waiting for a volume to be created, either by external provisioner "nasplugin.csi.alibabacloud.com" or manually created by system administrator
    3m31s       Normal    Provisioning           persistentvolumeclaim/pvc-nas-dynamic-create-subpath8   External provisioner is provisioning volume for claim "default/pvc-nas-dynamic-create-subpath8"
    ```

2.  执行以下命令查看集群是否部署了Flexvolume或CSI存储插件。

    -   执行以下命令查看集群是否部署了Flexvolume存储插件。

        ```
        kubectl get pod -nkube-system |grep flexvolume
        ```

        预期输出：

        ```
        NAME                      READY   STATUS             RESTARTS   AGE
        flexvolume-***            4/4     Running            0          23d
        ```

    -   执行以下命令查看集群是否部署了CSI存储插件。

        ```
        kubectl get pod -nkube-system |grep csi
        ```

        预期输出：

        ```
        NAME                       READY   STATUS             RESTARTS   AGE
        csi-plugin-***             4/4     Running            0          23d
        csi-provisioner-***        7/7     Running            0          14d
        ```

3.  查看使用的数据卷模板是否和集群存储插件（Flexvolume或CSI）的模板匹配。

    若在此集群中初次使用数据卷，请检查您的PV对象及StorageClass对象中定义的Driver名称属于CSI还是Flexvolume。Driver名称必须与当前集群部署的插件类型一致。

4.  查询存储插件是否为最新版本。

    -   执行以下命令查询Flexvolume插件的镜像版本。

        ```
        kubectl get ds flexvolume -nkube-system -oyaml | grep image
        ```

        预期输出：

        ```
        image: registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.109-649dc5a-aliyun
        ```

        关于Flexvolume插件信息，请参见[Flexvolume](/cn.zh-CN/产品发布记录/组件介绍与变更记录/存储/Flexvolume.md)。

    -   执行以下命令查询CSI插件的镜像版本。

        ```
        kubectl get ds csi-plugin -nkube-system -oyaml |grep image
        ```

        预期输出：

        ```
        image: registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.45-1c5d2cd1-aliyun
        ```

        关于CSI插件信息，请参见[csi-plugin](/cn.zh-CN/产品发布记录/组件介绍与变更记录/存储/csi-plugin.md)及[csi-provisioner](/cn.zh-CN/产品发布记录/组件介绍与变更记录/存储/csi-provisioner.md)。

5.  查看日志。

    -   若云盘PVC处于Pending状态，无法成功创建PV，则需要查看Provisioner日志。
        -   若集群部署了Flexvolume插件，则执行以下命令查询alicloud-disk-controller日志。

            ```
            podid=`kubectl get pod -nkube-system | grep alicloud-disk-controller | awk '{print $1}'`
            kubectl logs <PodID> -nkube-system
            ```

        -   若集群部署了CSI插件，则执行以下命令查询csi-provisioner日志。

            ```
            podid=`kubectl get pod -nkube-system | grep csi-provisioner | awk '{print $1}'`
            kubectl logs <PodID> -nkube-system -c csi-provisioner
            ```

            **说明：** csi-provisioner的容器组数量为2，上述`kubectl get pod -nkube-system | grep csi-provisioner | awk '{print $1}'`命令操作会输出2个`podid`，请分别执行`kubectl logs <PodID> -nkube-system -c csi-provisioner`命令。

    -   若Pod启动时挂载报错，则需要查看Flexvolume或csi-plugin日志。
        -   若集群部署了Flexvolume插件，则执行以下命令查询Flexvolume日志。

            ```
            kubectl get pod <pod-name> -owide
            ```

            登录Pod所在节点，在`/var/log/alicloud/flexvolume_**.log`查看Flexvolume日志。

        -   若集群部署了CSI插件，则执行以下命令查询csi-plugin日志。

            ```
            nodeID=`kubectl get pod <pod-name> -owide | awk 'NR>1 {print $7}'`
            podID=`kubectl get pods -nkube-system -owide -lapp=csi-plugin | grep $nodeID|awk '{print $1}'`
            kubectl logs <PodID> -nkube-system
            ```

    -   查看Kubelet日志。

        执行以下命令获取Pod所在节点。

        ```
        kubectl get pod <pod-name> -owide | awk 'NR>1 {print $7}'
        ```

        登录到该节点上查看/var/log/message日志文件。


**快速恢复**

众多Pod挂载不上数据卷场景的问题，可以通过将Pod调度到其他节点快速恢复。具体操作，请参见[调度应用Pod至指定节点](/cn.zh-CN/Kubernetes集群用户指南/应用/应用调度部署/调度应用Pod至指定节点.md)。

