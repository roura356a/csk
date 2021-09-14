---
keyword: [云盘存储卷, FAQ, 常见问题]
---

# 云盘存储卷FAQ

本文为您介绍云盘存储卷常见问题的处理方法。

-   [使用云盘时提示The specified disk is not a portable disk](#section_ddz_m1r_muj)
-   [启动挂载了云盘的Pod时提示had volume node affinity conflict](#section_e36_d7y_yvn)
-   [启动挂载了云盘的Pod时提示can't find disk](#section_44d_enm_5qg)
-   [动态创建PV失败且提示The specified AZone inventory is insufficient](#section_hxz_s8n_psd)
-   [动态创建PV失败且提示disk size is not supported](#section_wfu_6qp_5yd)
-   [磁盘阻塞导致Pod长时间无法启动](#section_u8n_7uh_0zb)
-   [启动Pod时出现FailedMount警告](#section_mwn_qnc_5o8)
-   [Pod卸载失败且Kubelet出现不受ACK管理的Pod日志](#section_8pk_40e_0n1)
-   [Pod删除后无法重新启动，提示挂载失败且无法自动恢复。](#section_vse_dem_l0i)

## 使用云盘时提示The specified disk is not a portable disk

问题现象：

卸载云盘时，提示The specified disk is not a portable disk。

问题原因：

您申请了包年包月的云盘，或者在升级ECS时，将ECS关联的云盘一起升级为包年包月。

解决方法：

将云盘的付费方式改为按量付费。

## 启动挂载了云盘的Pod时提示had volume node affinity conflict

问题现象：

在启动挂载云盘的Pod的时候，出现Pod无法启动的情况并报错had volume node affinity conflict。

问题原因：

您在PV中编写了`nodeaffinity`属性，这个属性与Pod中声明的属性不一致，导致Pod无法被调度到正确的节点上。

解决方法：

修改PV或者Pod的属性，使二者属性保持一致。

## 启动挂载了云盘的Pod时提示can't find disk

问题现象：

启动挂载了云盘的Pod时提示can't find disk。

问题原因：

-   您在编写PV的时候输入了错误的DiskID。
-   您的账号无权限操作DiskID，可能不是当前账号的DiskID。

解决方法：

更换DiskID。

## 动态创建PV失败且提示The specified AZone inventory is insufficient

问题现象：

创建PV失败，PVC Event提示The specified AZone inventory is insufficient。

问题原因：

ECS库存不足，导致创建云盘失败。

解决方法：

更换云盘类型或者更换Azone。

## 动态创建PV失败且提示disk size is not supported

问题现象：

动态创建PV失败，提示disk size is not supported。

问题原因：

在PVC中指定的云盘大小不符合规范，要求最小20 GiB。

解决方法：

调整PVC中云盘的大小，使其符合规范。

## 磁盘阻塞导致Pod长时间无法启动

问题现象：

磁盘出现阻塞，kubelet日志出现以下大量报错。

```
Operation for "{volumeName:kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvv**** podName: nodeName:}" failed. 
No retries permitted until 2020-11-05 14:38:12.653566679 +0800 CST m=+9150650.781033052 (durationBeforeRetry 2m2s). 
Error: "MountVolume.MountDevice failed for volume \"d-2zejaz33icbp2vvv****\" (UniqueName: \"kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvv****\") pod \"pod-e5ee2d454cdb4d1d916d933495e56cbe-358****\" (UID: \"f8d71e90-d934-4d5a-b54f-62555da5****\") : rpc error: code = Aborted desc = NodeStageVolume: Previous attach action is still in process
```

问题原因：

您使用的是旧版本的CSI，旧版本的CSI使用了BlkID通过UID获取盘符。由于使用快照恢复的云盘有着相同的UID，导致BlkID命令执行阻塞。

解决方法：

重启当前节点，或升级CSI到最新版本。

## 启动Pod时出现FailedMount警告

问题现象：

启动Pod的时候，出现以下警告。

```
Warning  FailedMount       104s                 kubelet, cn-zhangjiakou.172.20.XX.XX  Unable to attach or mount volumes: unmounted volumes=[sysdata-nas], unattached volumes=[kun-log kun-script kun-app sysdata-nas kun-patch default-token-rbx8p kun-etc kun-bin]: timed out waiting for the condition
Warning  FailedMount       98s (x9 over 3m45s)  kubelet, cn-zhangjiakou.172.20.XX.XX  MountVolume.MountDevice failed for volume "nas-9d9ead08-8a1d-4463-a7e0-7bd0e3d3****" : kubernetes.io/csi: attacher.MountDevice failed to create newCsiDriverClient: driver name nasplugin.csi.alibabacloud.com not found in the list of registered CSI drivers
```

问题原因：

一般出现在新增的节点。由于CSI的Pod是和业务Pod一起启动，且CSI注册需要一定时间，所以业务Pod开始挂载的时候，CSI还没有注册完成，导致出现警告。

解决方法：

无需处理，不影响Pod启动。

## Pod卸载失败且Kubelet出现不受ACK管理的Pod日志

问题现象：

Pod卸载失败，并且Kubelet出现不受ACK管理的Pod日志。

问题原因：

Pod异常退出，导致数据卷挂载点在卸载过程中没有清理干净，最终导致Pod无法删除。Kubelet的GC流程对数据卷垃圾回收实现并不完善，目前需要手动或脚本自动化实现垃圾挂载点的清理工作。

解决方法：

在问题节点运行以下脚本，对垃圾挂载点进行清理。

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

## Pod删除后无法重新启动，提示挂载失败且无法自动恢复。

问题现象：

Pod在删除之后无法拉起，报出以下异常提示，且无法自动恢复。

```
Warning FailedMount 9m53s (x23 over 40m) kubelet MountVolume.SetUp failed for volume “xxxxx” : rpc error: code = Internal desc = stat /var/lib/kubelet/plugins/kubernetes.io/csi/pv/xxxxx/globalmount: no such file or directory
```

影响范围：

-   ACK集群版本为1.20.4-aliyun-1。
-   应用的存储介质为云盘。
-   使用StatefulSet并且设置了`podManagementPolicy: "Parallel"`属性。

问题原因：

更多信息，请参见[Pod fails to start after restarting rapidly](https://github.com/kubernetes/kubernetes/issues/102779)。

解决方法：

-   为集群新增节点后移除旧节点，将旧节点全部替换为新节点，有故障的Pod将自动恢复。具体操作，请参见[扩容节点池](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/管理节点池.md)及[移除节点](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/移除节点.md)。
-   调整StatefulSet为`orderedready`或去除`podManagementPolicy: "Parallel"` 属性。
-   如果当前集群节点数较少，可以采用以下方案：
    1.  将当前Pod所在的节点添加`cordon`标签，设置为不可调度状态。
    2.  删除Pod，等待Pod状态变为Pending。
    3.  去除节点上的`cordon`标签，等待Pod重启。
-   如果当前集群节点数较多，则将Pod调度到其他节点后可正常启动。

若有其他疑问，请[联系我们](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/安装与升级CSI组件.md)。

