---
keyword: [有状态服务, 使用NAS]
---

# 有状态服务-NAS使用最佳实践

本文为您介绍有状态的服务-NAS的常见使用场景（需要多个Pod共享数据的应用场景）及方法。

-   [创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。
-   您已在文件存储控制台创建一个文件系统，请参见[Linux系统挂载NFS文件系统]()。创建的文件系统需要与您的Kubernetes集群在同一可用区。
-   您已在创建好的文件系统中添加容器服务Kubernetes集群的挂载点，请参见[Windows系统挂载SMB文件系统]()。文件系统挂载时的VPC网络要与您Kubernetes集群所在的VPC网络保持一致。

NAS支持同时被多个Pod挂载，此时多个Pod可能同时修改相同数据，需要应用自行实现数据的同步。

有状态服务-NAS的使用场景：

-   对磁盘I/O要求较高的应用。
-   读写性能相对于对象存储OSS高。
-   可实现跨主机文件共享，例如可作为文件服务器。

有状态服务-NAS的使用方式：

1.  手动创建文件系统，并添加挂载点。
2.  手动创建PV及PVC。

本文为您介绍利用阿里云提供的flexvolume插件，通过PV或PVC的方式使用阿里云NAS。

## 创建PV

1.  创建pv-nas.yaml文件。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-nas
      labels:
        alicloud-pvname: pv-nas
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      flexVolume:
        driver: "alicloud/nas"
        options:
          server: "***-**.cn-hangzhou.nas.aliyuncs.com"   ////请替换为您实际的挂载点
          path: "/k8s1"
          vers: "4.0"
    ```

    |参数|描述|
    |--|--|
    |`alicloud-pvname`|PV的名称。|
    |`server`|NAS的挂载点。可在文件存储控制台，单击左侧导航栏的**文件系统列表**，选择目标文件系统，单击**操作**列**管理**，在挂载点区域，**挂载地址**即为NAS数据盘的挂载点。|
    |`path`|NAS的挂载目录，支持挂载NAS的子目录，当子目录不存在时，会在NAS上自动创建子目录并挂载。|
    |`vers`|（可选）NFS挂载协议的版本号，支持3和 4.0，默认情况是3。|
    |`mode`|（可选）挂载目录的访问权限，默认情况是不配置。**说明：**

    -   挂载 NAS的根目录不能配置访问权限。
    -   当 NAS中数据量很大时，配置`mode`会导致挂载非常慢，甚至挂载失败，不建议配置。 |

2.  执行以下命令，创建PV。

    ```
    kubectl create -f pv-nas.yaml
    ```


**预期结果：**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**。
5.  在**存储卷**页面，可以看到刚刚创建的PV。

    ![创建PV](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6685659951/p99218.png)


## 创建PVC

创建NAS存储声明PVC，使用`selector`筛选PV，精确配置PVC和PV的绑定关系。

1.  创建pvc-nas.yaml文件。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-nas
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-nas
    ```

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-nas.yaml
    ```


**预期结果：**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
4.  在集群管理页左侧导航栏中，选择**存储** \> **存储声明**。
5.  在**存储声明**页面，可以看到刚刚创建的PVC。

    ![创建PVC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6685659951/p99219.png)


## 创建应用

1.  创建nas.yaml文件。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 2
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
              - name: pvc-nas
                mountPath: "/data"
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```

2.  执行以下命令，创建Deployment。

    ```
    kubectl create -f nas.yaml
    ```


**预期结果：**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**
4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。
5.  在**无状态**页面，可以看到刚刚创建的Deployment。

    ![创建应用](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6685659951/p161278.png)


## NAS的共享存储

1.  执行以下命令，查看部署的deployment所在Pod的名称。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-r****       1/1     Running   0          9m
    nas-static-f96b6b5d7-w****       1/1     Running   0          9m
    ```

2.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data                    
    ```

    预期输出：

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data                   
    ```

    **说明：** /data路径下为空，没有文件。

3.  执行以下命令，在任意一个Pod的/data路径下创建文件nas。

    ```
    kubectl exec nas-static-f96b6b5d7-r**** touch /data/nas
    ```

4.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data
    ```

    预期输出：

    ```
    nas
    ```

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data
    ```

    预期输出：

    ```
    nas
    ```

    **说明：** 在任意一个Pod的/data下创建的文件，两个Pod下的/data路径下均存在此文件，说明两个Pod共享一个NAS。


## NAS的持久化存储

1.  执行以下命令，删除该应用的所有Pod。

    ```
    kubectl delete pod nas-static-f96b6b5d7-r**** nas-static-f96b6b5d7-wthmb
    ```

    预期输出：

    ```
    pod "nas-static-f96b6b5d7-r****" deleted
    pod "nas-static-f96b6b5d7-w****" deleted
    ```

2.  执行以下命令，查看Pod删除及Kubernetes重建Pod的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

    预期输出：

    ```
    NAME                             READY   STATUS              RESTARTS       AGE
    nas-static-f96b6b5d7-r****       1/1     Running             0              27m
    nas-static-f96b6b5d7-w****       1/1     Running             0              27m
    nas-static-f96b6b5d7-r****       1/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-w****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-w****       0/1     ContainerCreating   0              0s
    nas-static-f96b6b5d7-w****       1/1     Terminating         0              28m
    nas-static-f96b6b5d7-n****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-n****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-n****       0/1     ContainerCreating   0              0s
    nas-static-f96b6b5d7-r****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-r****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-r****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       1/1     Running             0              10s
    nas-static-f96b6b5d7-w****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-n****       1/1     Running             0              17s
    ```

3.  执行以下命令，查看Kubernetes重建的Pod名称。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-n****       1/1     Running   0          21s
    nas-static-f96b6b5d7-w****       1/1     Running   0          21s
    ```

4.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec nas-static-f96b6b5d7-n**** ls /data
    ```

    预期输出：

    ```
    nas
    ```

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data
    ```

    预期输出：

    ```
    nas
    ```

    **说明：** 刚刚创建的文件nas并没有被删除，说明NAS的数据可持久保存。


