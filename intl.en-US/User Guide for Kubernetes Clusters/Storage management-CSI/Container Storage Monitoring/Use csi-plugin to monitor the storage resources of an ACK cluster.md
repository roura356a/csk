---
keyword: [monitor disk, csi-plugin, Prometheus, alert]
---

# Use csi-plugin to monitor the storage resources of an ACK cluster

Container Service for Kubernetes \(ACK\) allows you to monitor disks and Apsara File Storage NAS \(NAS\) file systems that are used in ACK clusters by using Prometheus Service. This topic describes how to monitor disks and NAS file systems used in ACK clusters and provides an example on how to configure alert rules.

Only csi-plugin of v1.18.8.46-afb19e46-aliyun can be used to monitor both disks and NAS file systems. Make sure that the YAML file and the image of csi-plugin are upgraded to the latest version. You can upgrade csi-plugin to the latest version by using the following methods:

-   Download and run the upgrade script from the GitHub community to upgrade csi-plugin to the latest version. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/tree/master/deploy/ack/upgrade).
-   Upgrade csi-plugin by using the ACK console. For more information, see [Upgrade csi-plugin by using the ACK console](#section_y7k_ts0_p79).

In most cases, ACK clusters store data in disks or NAS file systems. You can install the latest version of csi-plugin in ACK clusters to map the drive letters of disks to the persistent volume claims \(PVCs\) that are used to mount the disks. You can use Prometheus Service to monitor the status of PVCs. You can also configure threshold-based alert rules to monitor the storage usage and I/O operations per second \(IOPS\). If the specified thresholds are reached, alerts are triggered.

## Upgrade csi-plugin by using the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the Storage tab, find **csi-plugin** and click **Upgrade**.


## How to monitor disks

Only disks and NAS file systems can be monitored at the node side. To view the monitoring data that is provided by Prometheus Service, perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster details page, choose **Operations** \> **Prometheus Monitoring**.

5.  Click the dashboard named **CSI Nodes**. In the dashboard that appears, you can view the monitoring data that is collected by csi-plugin from the nodes.

    The following figure shows an example of the dashboard.

    ![metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1420719161/p183549.png)


The following tables describe the filters and metrics on the **CSI Nodes** dashboard.

-   Filters

    |Filter|Description|
    |------|-----------|
    |StorageType|The volume type of the PVC.|
    |Namespace|The namespace to which the PVC belongs.|
    |Pvc|The name of the PVC.|

-   Metrics

    |Metric|Description|Unit|
    |------|-----------|----|
    |IOPS|The read/write IOPS of the PVC.|ops/s \(number of reads/writes per second\)|
    |Latency\(avg\)|The average latency at which the PVC processes read/write requests.|ms \(milliseconds\)|
    |ThroughPut|The throughput of the PVC.|GB|
    |IO Count In Queue|The number of I/O requests in the queue.|Count|
    |Used Capacity|The used storage of the PVC.|GB|
    |Total Capacity|The storage capacity of the PVC.|GB|
    |Free Capacity|The available storage of the PVC.|GB|


## Examples on how to configure alert rules for disks

By default, alerting is disabled for disks that are mounted to ACK clusters. To enable alerting for disks, find the csi-plugin.yaml file by using the kubectl client and set the required parameters. For more information about how to use kubectl, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

In this example, the read/write latency of the disk is 10 milliseconds and the desired storage usage threshold is 85%. Set DISK\_LATENCY\_THRESHOLD to 10 ms and DISK\_CAPACITY\_THRESHOLD\_PERCENTAGE to 85% in the env section of the csi-plugin.yaml file.

```
- name: DISK_LATENCY_THRESHOLD
  value: 10ms
- name: DISK_CAPACITY_THRESHOLD_PERCENTAGE
  value: 85%
```

The following code block shows some of the configurations in the csi-plugin.yaml file:

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

After you run the [fio](https://fio.readthedocs.io/en/latest/fio_doc.html#command-line-options) command to perform a stress test on the disk and the specified thresholds are reached, you can check the alerts by using the following methods:

-   Run the `kubectl get events` command to check the triggered alerts.

    ![metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1420719161/p183534.png)

-   Check the alerts in the ACK console.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the details page, choose **Operations** \> **Events**. On the **Events** tab, you can view details about the alerts.

        ![View alert information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1420719161/p243258.png)


