---
keyword: [云盘监控, csi-plugin, Prometheus, 事件告警]
---

# 使用csi-plugin组件监控节点侧存储资源

容器服务Kubernetes版ACK（Container Service for Kubernetes）的存储监控可以帮助您在使用云盘和NAS时，通过阿里云Prometheus服务监控当前节点侧存储的使用状况。本文介绍如何使用ACK的节点侧存储监控功能，并举例说明如何配置事件告警。

目前只有v1.18.8.46-afb19e46-aliyun版本的csi-plugin同时支持云盘监控功能及NAS监控功能，请确保当前的csi-plugin组件的YAML版本和镜像版本为最新版本。您可以通过以下方式升级csi-plugin至最新版。

-   执行GitHub社区中[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/tree/master/deploy/ack/upgrade)的升级脚本。
-   通过控制台升级。具体操作，请参见[通过控制台升级csi-plugin组件](#section_y7k_ts0_p79)。

在阿里云ACK集群中通常使用云盘数据卷或NAS数据卷存储数据，通过部署csi-plugin的最新版本（ACK集群默认安装csi-plugin组件）可以将磁盘监控与PVC进行绑定。通过阿里云Prometheus监控可以查看当前PVC的状态、配置容量阈值和IOPS阈值，进行事件告警以感知当前PVC使用情况。

## 通过控制台升级csi-plugin组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **组件管理**。

5.  单击存储页签，在**csi-plugin**组件区域单击**升级**。


## 云盘监控使用说明

目前节点侧监控功能仅支持云盘及NAS两种存储产品。您可以通过阿里云Prometheus服务监控当前云盘或NAS的使用状况，具体操作如下。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **Prometheus监控**。

5.  单击**CSI Nodes**监控大盘名称，进入csi-plugin组件节点侧的监控大盘，查看监控信息。

    监控信息如下图。

    ![metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3121098161/p183549.png)


**CSI Nodes**监控大盘过滤项及监控指标说明如下：

-   过滤项：

    |过滤项|说明|
    |---|--|
    |StorageType|存储类型|
    |Namespace|PVC所在的命名空间|
    |Pvc|PVC的名称|

-   监控指标：

    |指标名称|说明|显示单位|
    |----|--|----|
    |IOPS|PVC的读写IOPS|ops/sec（每秒读写磁盘次数）|
    |Latency\(avg\)|PVC的读写请求的平均时延|ms（毫秒）|
    |ThroughPut|PVC的吞吐量|GB|
    |IO Count In Queue|IO请求在磁盘队列的个数|个数|
    |Used Capacity|PVC的磁盘使用空间大小|GB|
    |Total Capacity|PVC的磁盘总空间大小|GB|
    |Free Capacity|PVC的磁盘剩余空间大小|GB|


## 云盘告警使用示例

ACK集群默认不开启云盘监控告警功能。如果您需要开启云盘监控告警功能，可通过kubectl命令行客户端找到csi-plugin.yaml文件，然后在文件中配置相关参数。有关如何使用kubectl，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

本文假设磁盘读写延迟为10 ms，磁盘存储容器的阈值为85%，您只需在csi-plugin.yaml文件中的env下设置DISK\_LATENCY\_THRESHOLD和DISK\_CAPACITY\_THRESHOLD\_PERCENTAGE的值为10 ms和85%。

```
- name: DISK_LATENCY_THRESHOLD
  value: 10ms
- name: DISK_CAPACITY_THRESHOLD_PERCENTAGE
  value: 85%
```

csi-plugin.yaml文件的部分配置内容如下：

```
- name: csi-plugin
  securityContext:
    privileged: true
    capabilities:
      add: ["SYS_ADMIN"]
  allowPrivilegeEscalation: true
  image: registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.45-1c5d2cd1-aliyun
  imagePullPolicy: "Always"
  args:
    - "--endpoint=$(CSI_ENDPOINT)"
    - "--v=2"
    - "--driver=oss,nas,disk"
  env:
    - name: KUBE_NODE_NAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: spec.nodeName
    - name: CSI_ENDPOINT
      value: unix://var/lib/kubelet/csi-plugins/driverplugin.csi.alibabacloud.com-replace/csi.sock
    - name: MAX_VOLUMES_PERNODE
      value: "15"
    - name: SERVICE_TYPE
      value: "plugin"
    - name: DISK_LATENCY_THRESHOLD
      value: 10ms
    - name: DISK_CAPACITY_THRESHOLD_PERCENTAGE
      value: 85%
  livenessProbe:
    httpGet:
      path: /healthz
      port: healthz
      scheme: HTTP
    initialDelaySeconds: 10
    periodSeconds: 30
    timeoutSeconds: 5
    failureThreshold: 5
  ports:
    - name: healthz
      containerPort: 11260
      protocol: TCP
  volumeMounts:
    - name: kubelet-dir
      mountPath: /var/lib/kubelet/
      mountPropagation: "Bidirectional"
    - name: etc
      mountPath: /host/etc
    - name: host-log
      mountPath: /var/log/
    - name: ossconnectordir
      mountPath: /host/usr/
    - name: container-dir
      mountPath: /var/lib/container
      mountPropagation: "Bidirectional"
    - name: host-dev
      mountPath: /dev
      mountPropagation: "HostToContainer"
```

当使用[fio](https://fio.readthedocs.io/en/latest/fio_doc.html#command-line-options)命令压测云盘性能达到设置的阈值时，您可以通过两种方式查看告警信息：

-   使用`kubectl get events`命令查看事件告警。

    ![metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660614161/p183534.png)

-   通过容器服务控制台查看告警信息。
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **事件列表**，然后在**事件列表**页签中查看告警信息。

        ![查看告警信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8371224161/p243258.png)


