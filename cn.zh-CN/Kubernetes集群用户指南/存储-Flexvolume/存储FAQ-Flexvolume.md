---
keyword: [存储卷, 常见问题, FAQ]
---

# 存储FAQ-Flexvolume

本文介绍您在使用云盘、NAS及OSS存储卷时常见问题的处理方法。

|类型|问题|
|--|--|
|存储常见问题|-   [如何解决存储卷挂载不上的问题？](#section_dpb_h3t_h2b)
-   [如何查看存储相关日志？](#section_wrg_q3t_h2b)
-   [如何解决Kubelet出现不受ACK管理的Pod日志的问题？](#section_u3t_r77_ctv) |
|云盘存储卷常见问题|-   [云盘挂载失败，出现timeout错误](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md)
-   [云盘挂载失败时出现Zone错误](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md)
-   [升级系统后，云盘报错input/output error](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md)
-   [卸载云盘时提示The specified disk is not a portable disk](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md)
-   [挂载云盘的Pod无法启动且提示had volume node affinity conflict](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md)
-   [启动挂载了云盘的Pod时，提示can't find disk](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md)
-   [动态创建PV失败，提示disk size is not supported](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷FAQ.md) |
|NAS存储卷常见问题|-   [NAS存储卷挂载时间延长](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md)
-   [NAS存储卷挂载失败时出现timeout错误](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md)
-   [使用NAS存储卷时，提示chown: option not permitted](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md)
-   [挂载NAS存储卷失败](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md)
-   [使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md) |
|OSS存储卷常见问题|-   [OSS存储卷挂载失败](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/OSS存储卷FAQ.md)
-   [集群升级后容器内OSS挂载目录不可用](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/OSS存储卷FAQ.md)
-   [OSS存储卷挂载时间延长](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/OSS存储卷FAQ.md) |

## 如何解决存储卷挂载不上的问题？

您需要检查Flexvolume和动态存储插件是否安装，如果没有，请安装Flexvolume和动态存储插件。

**方式一：检查Flexvolume是否安装**

执行以下命令获取Pod信息。

```
kubectl get pod -n kube-system | grep flexvolume
```

预期输出：

```
flexvolume-4wh8s            1/1       Running   0          8d
flexvolume-65z49            1/1       Running   0          8d
flexvolume-bpc6s            1/1       Running   0          8d
flexvolume-l8pml            1/1       Running   0          8d
flexvolume-mzkpv            1/1       Running   0          8d
flexvolume-wbfhv            1/1       Running   0          8d
flexvolume-xf5cs            1/1       Running   0          8d   
```

查看Flexvolume Pod状态是否为Running，且运行的数量与节点数量相同。

如果运行状态不对，请参见插件运行日志分析。

**方式二：检查动态存储插件是否安装**

如果使用云盘的动态存储功能，需要确认是否安装动态存储插件，执行以下命令查看Pod信息。

```
kubectl get pod -n kube-system | grep alicloud-disk
```

预期输出：

```
alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d           
```

如果运行状态不对，请参考插件运行日志分析。

## 如何查看存储相关日志？

您可以查看Flexvolume日志、Provisioner插件日志和Kubelet日志。

**方式一：查看Flexvolume日志（master1上执行）**

执行get命令查看出错的Pod。

```
kubectl get pod -n kube-system | grep flexvolume
```

执行log命令，查看出错Pod的日志。

```
kubectl logs flexvolume-4wh8s -n kube-system
kubectl describe pod flexvolume-4wh8s -n kube-system
```

**说明：** 在Pod描述最后若干行是Pod运行状态的描述，可以根据描述分析错误。

查看云盘、NAS及OSS驱动日志。

执行以下命令查看Host节点上持久化的日志。如果某个Pod挂载失败，查看Pod所在的节点地址。

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

预期输出：

```
Node: cn-hangzhou.i-bp19myla3uvnt6zi****/192.168.XX.XX
Node-Selectors:  <none>
```

登录节点，查看云盘、NAS、OSS挂载的日志。

```
ssh 192.168.XX.XX
ls /var/log/alicloud/flexvolume*
```

预期输出：

```
flexvolume_disk.log  flexvolume_nas.log  flexvolume_o#ss.log
```

**方式二：查看Provisioner插件日志（master1上执行）**

执行get命令查看出错的Pod。

```
kubectl get pod -n kube-system | grep alicloud-disk
```

执行log命令，查看出错Pod的日志。

```
kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
```

**说明：** 在Pod描述最后若干行是Pod运行状态的描述，可以根据描述分析错误。

**方式三：查看Kubelet日志**

如果某个Pod挂载失败，查看Pod所在的节点地址。

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

预期输出：

```
Node: cn-hangzhou.i-bp19myla3uvnt6zi****/192.168.XX.XX
Node-Selectors:  <none>
```

登录节点，查看kubelet日志。

```
ssh 192.168.XX.XX
journalctl -u kubelet -r -n 1000 &> kubelet.log
```

**说明：** -n的值表示期望看到的日志行数。

上述为获取Flexvolume、Provisioner、Kubelet错误日志的方法，如果无法根据日志修复状态，可以附带日志信息联系阿里云技术支持。

## 如何解决Kubelet出现不受ACK管理的Pod日志的问题？

Pod异常退出，导致数据卷挂载点在卸载过程中没有清理干净，最终导致Pod无法删除。Kubelet的GC流程对数据卷垃圾回收实现并不完善，目前需要手动或脚本自动化实现垃圾挂载点的清理工作。

您需要在问题节点运行以下脚本，对垃圾挂载点进行清理。

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

