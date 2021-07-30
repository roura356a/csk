---
keyword: [云盘存储卷, FAQ, 常见问题, Flexvolume]
---

# 云盘存储卷FAQ

本文为您介绍云盘存储卷常见问题的处理方法。

-   [云盘挂载失败，出现timeout错误](#section_td0_7vk_92o)
-   [升级系统后，云盘报错input/output error](#section_vxc_7oy_i24)
-   [卸载云盘时提示The specified disk is not a portable disk](#section_f1w_yob_goz)
-   [挂载云盘的Pod无法启动且提示had volume node affinity conflict](#section_fay_4ao_0ik)
-   [启动挂载了云盘的Pod时，提示can't find disk](#section_kwt_2d5_r0u)
-   [动态创建PV失败，提示disk size is not supported](#section_vi0_dfh_v7w)

## 云盘挂载失败，出现timeout错误

问题现象：

云盘挂载失败，出现timeout错误。

问题原因：

如果节点为手动添加，可能是由于STS权限的问题导致。

解决方法：

需要手动配置RAM权限。具体操作，请参见[授予实例RAM角色](/intl.zh-CN/安全/管理身份和权限/通过RAM角色控制资源访问/授予实例RAM角色.md)。

## 云盘挂载失败时出现Zone错误

问题现象：

云盘挂载时，出现Zone错误。

问题原因：

ECS挂载的云盘时不在同一个Region下面的相同Zone内。

解决方法：

ECS挂载云盘时，必须在同一个Region下面的相同Zone内，否则不能挂载成功。

## 升级系统后，云盘报错input/output error

问题现象：

升级系统后，云盘报错input/output error。

问题原因：

Flexvolume组件版本过低。

解决方法：

1.  升级Flexvolume到v1.9.7-42e8198或以后版本。

    ```
    kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
    ```

2.  对于已经出问题的Pod，需要重建。

关于Flexvolume组件版本信息，请参见[Flexvolume](/intl.zh-CN/产品发布记录/组件介绍与变更记录/存储/Flexvolume.md)。

## 卸载云盘时提示The specified disk is not a portable disk

问题现象：

卸载云盘时，提示The specified disk is not a portable disk。

问题原因：

您申请了包年包月的云盘，或者在升级ECS时，将ECS关联的云盘一起升级为包年包月。

解决方法：

将云盘的付费方式改为按量付费。

## 挂载云盘的Pod无法启动且提示had volume node affinity conflict

问题现象：

在启动挂载云盘的Pod的时候，出现Pod无法启动的情况并报错had volume node affinity conflict。

问题原因：

您在PV中编写了`nodeaffinity`属性，这个属性与Pod中声明的属性不一致，导致Pod无法被调度到正确的节点上。

解决方法：

修改PV或者Pod的属性，使二者属性保持一致。

## 启动挂载了云盘的Pod时，提示can't find disk

问题现象：

卸载云盘时，提示The specified disk is not a portable disk。

问题原因：

-   您在编写PV的时候输入了错误的DiskID。
-   您的账号无权限操作DiskID，可能不是当前账号的DiskID。

解决方法：

更换DiskID。

## 动态创建PV失败，提示disk size is not supported

问题现象：

动态创建PV失败，提示disk size is not supported。

问题原因：

在PVC中指定的云盘大小不符合规范，要求最小20 GiB。

解决方法：

调整PVC中云盘的大小，使其符合规范。

