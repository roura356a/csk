---
keyword: [ACK Windows节点, SMB, 存储资源, Kubernetes]
---

# Windows容器挂载SMB

您可以在ACK Windows容器上使用SMB存储资源。本文介绍如何在Windows容器上使用SMB。

-   [创建Windows节点池](/intl.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)。
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。
-   在NAS控制台创建一个与集群在相同的VPC的SMB存储盘，并创建挂载点。具体操作，请参见[Windows系统挂载SMB文件系统]()。

## 步骤一：部署插件

有关Windows容器挂载SMB中插件部署的具体步骤，请参见Window容器挂载云盘中的[部署插件](/intl.zh-CN/Kubernetes集群用户指南/Windows容器/Windows容器挂载云盘.md)。

## 步骤二：创建PV和PVC

1.  使用以下YAML模板创建PV和PVC。



    上述PV模板中的参数说明如下。

    |参数|描述|
    |--|--|
    |driver|配置挂载驱动，必须为alicloud/smb.exe。|
    |server|SMB存储盘的挂载点域名，所在网络需要和集群所在VPC相同。|
    |path|SMB存储盘的挂载路径，必须以\\myshare开头，后面可以为空或者子目录。|
    |user|集群节点的登录用户名，推荐使用workshop\\administrator。|
    |password|集群节点的登录密码。|

2.  执行以下命令查看创建的PVC。

    ```
    kubectl get pvc |grep pvc-smb
    ```

    预期输出：

    ```
    pvc-smb                    Bound    pv-smb                   5Gi        RWX                                         24h
    ```


## 步骤三：创建应用

1.  使用以下YAML模板创建应用。

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: nginx-smb
      namespace: default
    spec:
      selector:
        matchLabels:
          app: nginx-smb
      template:
        metadata:
          labels:
            app: nginx-smb
        spec:
          replicas: 2
          tolerations:
          - effect: NoSchedule
            key: os
            operator: Equal
            value: windows
          containers:
          - args:
            - -Command
            - start-sleep 10000
            command:
            - pwsh.exe
            image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.16.9.7be0fa0-windows1809
            imagePullPolicy: IfNotPresent
            name: nginx
            volumeMounts:
            - mountPath: /data
              name: pvc-nas
          restartPolicy: Always
          volumes:
          - name: pvc-nas
            persistentVolumeClaim:
              claimName: pvc-smb
    ```

2.  执行以下命令查看应用状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                        READY   STATUS    RESTARTS   AGE
    nginx-smb-965fb4597-jz6fv   1/1     Running   0          95s
    nginx-smb-965fb4597-zvbhk   1/1     Running   0          42s
    ```

    当状态为**Running**时，说明成功创建应用。


