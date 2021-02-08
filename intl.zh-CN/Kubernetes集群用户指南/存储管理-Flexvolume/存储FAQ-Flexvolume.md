---
keyword: [云盘存储, NAS存储, FAQ]
---

# 存储FAQ-Flexvolume

本文为您介绍存储常见问题的处理方法。

**云盘存储常见问题**

-   [使用云盘时，提示The specified disk is not a portable disk](#section_rqz_dv4_up0)
-   [启动挂载了云盘的Pod时，提示had volume node affinity conflict](#section_24n_fqd_1lz)
-   [启动挂载了云盘的Pod时，提示can't find disk](#section_3et_ci6_kef)
-   [动态创建PV失败，提示The specified AZone inventory is insufficient](#section_0as_22b_txn)
-   [动态创建PV失败，提示disk size is not supported](#section_ony_za8_acw)
-   [Pod卸载失败，并且Kubelet出现不受ACK管理的Pod日志](#section_u64_r44_iej)

**NAS文件存储常见问题**

-   [使用NAS时，提示chown: option not permitted](#section_cxn_2gp_hxw)
-   [挂载NAS PV失败](#section_1h3_9u3_zox)

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

## 挂载NAS PV失败

问题现象：

挂载NAS PV失败，并报以下错误：

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e0084d3)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

问题原因：

没有安装Flexvolume插件。

解决方法：

安装Flexvolume插件。具体操作，请参见[安装插件](/intl.zh-CN/Kubernetes集群用户指南/存储管理-Flexvolume/安装插件.md)。

