---
keyword: [阿里云对象存储服务, OSS, 持久化存储]
---

# 使用OSS实现持久化存储-Flexvolume

当容器发生宕机故障时，有状态服务容器存储的业务数据存在着丢失和不可靠等风险。使用持久化存储可以解决该问题。本文介绍如何使用OSS实现持久化存储。

## 背景信息

阿里云对象存储服务（OSS）提供海量、安全、低成本、高可靠的云存储服务。OSS支持同时被多个Pod挂载。

OSS使用场景：

-   对磁盘I/O要求低。
-   配置文件、图片、小视频等共享业务。

OSS使用方式：

1.  手动创建Bucket。
2.  获取**AccessKey ID**和**AccessKey Secret**。
3.  手动创建PV及PVC。

## 前提条件

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。
-   您已在OSS管理控制台创建Bucket，请参见[创建存储空间](/intl.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

## 注意事项

-   容器服务Kubernetes集群升级会重启Kubelet，OSSFS驱动跟随一起重启，导致OSS目录不可用。这时使用OSS的Pod需要重建，可在YAML文件中增加健康检查的配置，在容器内OSS目录不可用时自动重启Pod，重新挂载OSS。
-   通过最新版本挂载的OSS已经解决了此问题。

## 创建PV

1.  创建pv-oss.yaml文件。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-oss
      labels:
        alicloud-pvname: pv-oss
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      storageClassName: oss
      flexVolume:
        driver: "alicloud/oss"
        options:
          bucket: "docker"                        //请替换为您的Bucket名称。
          url: "oss-cn-hangzhou.aliyuncs.com"     //请替换为您的URL。
          akId: "***"                             //请替换为您的AccessKey ID。
          akSecret: "***"                         //请替换为您的AccessKey Secret。
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"   //请替换为您的otherOpts。
    ```

    参数解释：

    -   `alicloud-pvname`：PV的名称，与PVC中的`selector`配合使用。
    -   `bucket`：Bucket名称，目前只支持挂载Bucket，不支持挂载Bucket下面的子目录或文件。
    -   `url`：OSS Bucket的访问域名（Endpoint），请参见[访问域名和数据中心](/intl.zh-CN/开发指南/访问域名（Endpoint）/访问域名和数据中心.md)。也可以在OSS管理控制台查询：登录OSS管理控制台，在左侧导航栏选择目标Bucket，在**概览**页面的访问域名区域，查看**Endpoint（地域节点）**。
    -   `akId`：AccessKey ID。在容器服务管理控制台，单击右上角![user](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7685659951/p161100.png)，选择**AccessKey 管理**，设置**AccessKey ID**和**AccessKey Secret**。
    -   `akSecret`：AccessKey Secret。获取方法同`akId`。
    -   `otherOpts`：挂载OSS时支持定制化参数，格式为：`-o *** -o ***`。
2.  执行以下命令，创建PV。

    ```
    kubectl create -f pv-oss.yaml
    ```


**预期结果：**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**，可以看到刚刚创建的PV。

    ![存储卷](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8685659951/p161619.png)


## 创建PVC

创建OSS存储声明PVC，使用`selector`筛选PV，精确配置PVC和PV的绑定关系。使用`storageClassName`，表示PVC只与OSS类型的PV绑定。

1.  创建pvc-oss.yaml文件。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-oss
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: oss
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-oss
    ```

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-oss.yaml
    ```


**预期结果：**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**，可以看到刚刚创建的PVC。

    ![PVC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8685659951/p161622.png)


## 创建应用

1.  创建oss-static.yaml文件。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: oss-static
      labels:
        app: nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
            volumeMounts:
              - name: pvc-oss
                mountPath: "/data"
              - name: pvc-oss
                mountPath: "/data1"
            livenessProbe:
              exec:
                command:
                - sh
                - -c
                - cd /data
              initialDelaySeconds: 30
              periodSeconds: 30
          volumes:
            - name: pvc-oss
              persistentVolumeClaim:
                claimName: pvc-oss
    ```

    **说明：** 健康检查`livenessProbe`的详细内容及解释，请参见[OSS存储卷使用说明](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS储存卷/OSS存储卷使用说明.md)。

2.  执行以下命令，创建Deployment。

    ```
    kubectl create -f oss-static.yaml d
    ```


**预期结果：**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。
4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**，可以看到刚刚创建的Deployment。

    ![Deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8685659951/p161689.png)


## OSS的持久化存储

1.  执行以下命令，查看部署的deployment所在Pod的名称。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          1h
    ```

2.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    **说明：** /data路径下为空，没有文件。

3.  执行以下命令，在/data路径下创建文件tmpfile。

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 touch /data/tmpfile
    ```

4.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    预期输出：

    ```
    tmpfile
    ```

5.  执行以下命令，删除名称为oss-static-66fbb85b67-dqbl2的Pod。

    ```
    kubectl delete pod oss-static-66fbb85b67-dqbl2
    ```

    预期输出：

    ```
    pod "oss-static-66fbb85b67-dqbl2" deleted
    ```

6.  同时在另一个窗口中，执行以下命令，查看Pod删除及Kubernetes重建Pod的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          78m
    oss-static-66fbb85b67-dqbl2   1/1   Terminating   0     78m
    oss-static-66fbb85b67-zlvmw   0/1   Pending   0     <invalid>
    oss-static-66fbb85b67-zlvmw   0/1   Pending   0     <invalid>
    oss-static-66fbb85b67-zlvmw   0/1   ContainerCreating   0     <invalid>
    oss-static-66fbb85b67-dqbl2   0/1   Terminating   0     78m
    oss-static-66fbb85b67-dqbl2   0/1   Terminating   0     78m
    oss-static-66fbb85b67-dqbl2   0/1   Terminating   0     78m
    oss-static-66fbb85b67-zlvmw   1/1   Running   0     <invalid>
    ```

7.  执行以下命令，查看Kubernetes重建的Pod名称。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-zlvmw      1/1     Running   0          40s
    ```

8.  执行以下命令，查看/data路径下的文件，刚刚创建的文件tmpfile并没有被删除，说明OSS上的数据可持久保存。

    ```
    kubectl exec oss-static-66fbb85b67-zlvmw ls /data | grep tmpfile
    ```

    预期输出：

    ```
    tmpfile
    ```


