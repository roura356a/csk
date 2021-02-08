---
keyword: [云盘存储, NAS存储, FAQ]
---

# 存储FAQ-CSI

本文为您介绍存储常见问题的处理方法。

**云盘存储常见问题**

-   [使用云盘时，提示The specified disk is not a portable disk](#section_rqz_dv4_up0)
-   [启动挂载了云盘的Pod时，提示had volume node affinity conflict](#section_24n_fqd_1lz)
-   [启动挂载了云盘的Pod时，提示can't find disk](#section_3et_ci6_kef)
-   [动态创建PV失败，提示The specified AZone inventory is insufficient](#section_0as_22b_txn)
-   [动态创建PV失败，提示disk size is not supported](#section_ony_za8_acw)
-   [磁盘出现阻塞，导致Pod长时间无法启动](#section_rv9_dh1_7r1)
-   [启动Pod时，出现FailedMount警告](#section_vv3_6ll_hvn)
-   [Pod卸载失败，并且Kubelet出现不受ACK管理的Pod日志](#section_u64_r44_iej)

**NAS文件存储常见问题**

[使用NAS时，提示chown: option not permitted](#section_cxn_2gp_hxw)

## 使用云盘时，提示The specified disk is not a portable disk

问题现象：

卸载云盘时，提示The specified disk is not a portable disk。

问题原因：

您申请了包年包月的云盘，或者在升级ECS时，把ECS关联的云盘一起升级为包年包月。

解决方法：

将云盘的付费方式改为按量付费。

## 启动挂载了云盘的Pod时，提示had volume node affinity conflict

问题现象：

在启动挂载云盘的Pod的时候，出现Pod无法启动的情况并报错had volume node affinity conflict。

问题原因：

您在PV中编写了nodeaffinity属性，这个属性与Pod中声明的属性不一致，导致Pod无法被调度到正确的节点上。

解决方法：

修改PV或者Pod的属性，使二者属性保持一致。

## 启动挂载了云盘的Pod时，提示can't find disk

问题现象：

在启动挂载云盘的Pod时，无法找到对应diskid。

问题原因：

-   您在编写PV的时候输入了错误的diskid。
-   您的账号无权限操作diskid，可能不是当前账号的diskid。

解决方法：

更换diskid。

## 动态创建PV失败，提示The specified AZone inventory is insufficient

问题现象：

创建PV失败，PVC Event提示The specified AZone inventory is insufficient。

问题原因：

ECS库存不足，导致创建云盘失败。

解决方法：

更换云盘类型或者更换Azone。

## 动态创建PV失败，提示disk size is not supported

问题现象：

动态创建PV失败，提示disk size is not supported。

问题原因：

在PVC中指定的云盘大小不符合规范，要求最小20 Gi。

解决方法：

调整PVC中云盘的大小，使其符合规范。

## 磁盘出现阻塞，导致Pod长时间无法启动

问题现象：

磁盘出现阻塞，kubelet日志出现下以下大量报错。

```
Operation for "{volumeName:kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvvc9le podName: nodeName:}" failed. No retries permitted until 2020-11-05 14:38:12.653566679 +0800 CST m=+9150650.781033052 (durationBeforeRetry 2m2s). Error: "MountVolume.MountDevice failed for volume \"d-2zejaz33icbp2vvvc9le\" (UniqueName: \"kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvvc9le\") pod \"pod-e5ee2d454cdb4d1d916d933495e56cbe-3584893\" (UID: \"f8d71e90-d934-4d5a-b54f-62555da5df22\") : rpc error: code = Aborted desc = NodeStageVolume: Previous attach action is still in process
```

问题原因：

您使用的是旧版本的CSI，旧版本的CSI使用了blkid通过uuid获取盘符。由于使用快照恢复的云盘有着相同的uuid，导致blkid命令卡死。

解决方法：

重启当前节点，或升级CSI到最新版本。

## 启动Pod时，出现FailedMount警告

问题现象：

启动Pod的时候，出现以下警告：

```
 Warning  FailedMount       104s                 kubelet, cn-zhangjiakou.172.20.11.162  Unable to attach or mount volumes: unmounted volumes=[sysdata-nas], unattached volumes=[kun-log kun-script kun-app sysdata-nas kun-patch default-token-rbx8p kun-etc kun-bin]: timed out waiting for the condition
 Warning  FailedMount       98s (x9 over 3m45s)  kubelet, cn-zhangjiakou.172.20.11.162  MountVolume.MountDevice failed for volume "nas-9d9ead08-8a1d-4463-a7e0-7bd0e3d3****" : kubernetes.io/csi: attacher.MountDevice failed to create newCsiDriverClient: driver name nasplugin.csi.alibabacloud.com not found in the list of registered CSI drivers
```

问题原因：

一般出现在新增的节点。由于CSI的Pod是和业务Pod一起启动，且CSI注册需要一定时间，所以业务Pod开始挂载的时候，CSI还没有注册完成，导致出现警告。

解决方法：

无需处理，不影响Pod启动。

## Pod卸载失败，并且Kubelet出现不受ACK管理的Pod日志

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

## 使用NAS时，提示chown: option not permitted

问题现象：

使用NAS时，提示chown: option not permitted。

问题原因：

您的容器没有权限使用该NAS。

解决方法：

使用root权限启动容器。

