---
keyword: [OSS静态卷, K8s]
---

# 使用OSS静态卷

本文为您介绍使用OSS静态存储卷的几种方式。

## 直接使用Volumes挂载的方式

1.  创建并复制以下内容到oss-deploy.yaml中。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-oss-deploy
    spec:
      replicas: 1
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx-flexvolume-oss
            image: nginx
            volumeMounts:
              - name: "oss1"
                mountPath: "/data"
            livenessProbe:
              exec:
                command:
                - sh
                - -c
                - cd /data
              initialDelaySeconds: 30
              periodSeconds: 30
          volumes:
            - name: "oss1"
              flexVolume:
                driver: "alicloud/oss"
                options:
                  bucket: "docker"
                  url: "oss-cn-hangzhou.aliyuncs.com"
                  akId: "LTAI4G6E3whAAKnzdRPx****"
                  akSecret: "uRJeIi0cbahgWOhxncpp54wR5b****"
                  otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

2.  执行以下命令，创建Pod。

    ```
    kubectl apply -f oss-deploy.yaml
    ```


## 使用创建PV和PVC的方式

1.  创建PV。

    您可以使用YAML文件或者通过容器服务控制台界面创建PV。

    -   使用YAML文件创建PV

        使用oss-pv.yaml文件创建PV。

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: pv-oss
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          storageClassName: oss
          flexVolume:
            driver: "alicloud/oss"
            options:
              bucket: "docker"
              url: "oss-cn-hangzhou.aliyuncs.com"
              akId: "LTAI4G6E3whAAKnzdRPx****"
              akSecret: "uRJeIi0cbahgWOhxncpp54wR5b****"
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
        ```

    -   通过控制台界面创建OSS存储卷
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**。
        5.  在**存储卷**页面单击**创建**。
        6.  在**创建存储卷**对话框中，配置存储卷的相关参数。

            |参数|描述|
            |--|--|
            |**存储卷类型**|本示例中为OSS。|
            |**名称**|创建的数据卷的名称。数据卷名在集群内必须唯一。本例为`pv-oss`。|
            |**存储驱动**|本示例选择Flexvolume。有关存储插件详细信息，请参见[CSI和Flexvolume存储插件的区别](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)。|
            |**总量**|所创建存储卷的容量。|
            |**访问模式**|默认为ReadWriteMany。|
            |**访问证书**|选择访问OSS所需的保密字典。            -   **选择已有保密字典**：选择命名空间及保密字典。
            -   **新建保密字典**：配置命名空间、名称、AccessKey ID及AccessKey Secret。 |
            |**可选参数**|您可以为OSS存储卷输入定制化参数，格式为：`-o *** -o ***`。|
            |**Bucket ID**|您要使用的OSS Bucket的名称。单击**选择Bucket**，在弹出的对话框中选择所需的Bucket并单击**选择**。|
            |**访问域名**|如果Bucket和ECS实例位于不同地域（Region），请选择**公网域名**；若是经典网络，请选择**私网域名**。|
            |**标签**|为该存储卷添加标签。|

        7.  完成配置后，单击**创建**。
2.  创建PVC。

    使用oss-pvc.yaml文件创建PVC。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-oss
    spec:
      storageClassName: oss
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
    ```

3.  创建Pod。

    使用oss-deploy.yaml创建Pod。

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


## 通过Secret方式使用AccessKey

1.  执行以下命令，创建Secret。

    ```
    kubectl create secret generic osssecret --from-literal=akId='LTAI4G6E3whAAKnzdRPx****' --from-literal=akSecret='uRJeIi0cbahgWOhxncpp54wR5b****' --type=alicloud/oss -n default
    ```

    -   `osssecret`：Secret的名字，可以自行配置。
    -   `akId`：AccessKey ID。
    -   `akSecret`：AccessKey Secret。
    -   Secret类型：配置为`alicloud/oss`名词空间，需要与期望的Pod在相同的命名空间。
2.  在PV中使用Secret。

    在PV中通过`secretRef`字段定义Secret。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-oss
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      storageClassName: oss
      flexVolume:
        driver: "alicloud/oss"
        secretRef:
          name: "osssecret"
        options:
          bucket: "docker"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

3.  在Volume中使用Secret。

    Volume中通过`secretRef`字段定义Secret。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-oss-deploy1
    spec:
      replicas: 3
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx-flexvolume-oss
            image: nginx
            volumeMounts:
              - name: "oss1"
                mountPath: "/data"
                subPath: "hello"
          volumes:
            - name: "oss1"
              flexVolume:
                driver: "alicloud/oss"
                secretRef:
                  name: "osssecret"
                options:
                  bucket: "aliyun-docker"
                  url: "oss-cn-hangzhou.aliyuncs.com"
                  otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

    **说明：** 使用Secret方式配置AccessKey时，应用（Pod）所在的命名空间要和Secret所在的命名空间一致。


