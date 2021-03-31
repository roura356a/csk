---
keyword: [ACK Windows节点, 云盘, 存储资源, Kubernetes]
---

# Windows容器挂载云盘

您可以在ACK Window容器上使用云盘存储资源。本文介绍如何在Windows容器上使用云盘。

-   [创建Windows节点池](/intl.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

## 操作步骤

1.  部署插件。

    1.  创建YAML文件。

        使用以下模板创建YAML文件在集群中部署Flexvolume组件，创建Windows环境下的StorageClass，以及更新集群中默认安装的alicloud-disk-controller，使其支持Windows云盘PV的创建。

        Flexvolume是Kubernetes支持的一种存储插件扩展方式。更多信息，请参见[存储插件说明](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/存储插件说明.md)。



    2.  执行`kubectl apply -f <YAML文件名称>.yaml`部署插件。

2.  创建应用。

    1.  使用以下YAML模板创建PVC和StatefulSet验证存储组件是否可用。

        ```
        apiVersion: v1
        kind: Service
        metadata:
          name: nginx
          labels:
            app: nginx
        spec:
          ports:
          - port: 80
            name: web
          clusterIP: None
          selector:
            app: nginx
        ---
        apiVersion: apps/v1beta2
        kind: StatefulSet
        metadata:
          name: disk-windows
        spec:
          selector:
            matchLabels:
              app: nginx
          serviceName: "nginx"
          replicas: 1
          template:
            metadata:
              labels:
                app: nginx
            spec:
              tolerations:
              - effect: NoSchedule
                key: os
                operator: Equal
                value: windows
              containers:
              - name: nginx
                image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.16.9.7be0fa0-windows1809
                command: ["pwsh.exe"]
                args: ["-Command", "start-sleep 10000"]
                volumeMounts:
                  - name: disk-essd
                    mountPath: "C:\\data"
          volumeClaimTemplates:
          - metadata:
              name: disk-essd
            spec:
              accessModes: [ "ReadWriteOnce" ]
              storageClassName: alicloud-disk-essd-windows
              resources:
                requests:
                  storage: 20Gi
        ```

    如果存储组件成功部署，执行以下命令可看到自动创建的PV。

    ```
    kubectl get pv
    ```

    预期输出：

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM              STORAGECLASS        REASON   AGE
    d-2zeh2yew2t48lu75joy1   20Gi       RWO            Delete           Bound    default/pvc-disk   alicloud-disk-ssd            2m46s
    ```


