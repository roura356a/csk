# 使用OSS静态卷 {#task_1780362 .task}

本文为您介绍使用OSS静态存储卷的几种方式。

## 直接使用 volume 方式 {#section_p60_knt_xpx .section}

1.  创建并复制以下内容到oss-deploy.yaml中。 

    ``` {#codeblock_b5s_hqt_tqy}
    apiVersion: extensions/v1beta1
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
                  akId: ***
                  akSecret: ***
                  otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

2.  执行如下命令，创建Pod。 

    ``` {#codeblock_bhh_7yh_3jm}
    kubectl apply -f oss-deploy.yaml
    ```


## 使用 PV/PVC {#section_otk_qv5_7uc .section}

1.  创建 PV。 您可以使用 yaml 文件或者通过容器服务控制台界面创建 PV。
    -   使用 yaml 文件创建 PV

        使用oss-pv.yaml文件创建 PV。

        ``` {#codeblock_f4p_ci0_p08}
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
              akId: ***
              akSecret: ***
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
        ```

    -   通过控制台界面创建 OSS 存储卷
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群** \> **存储卷**，进入存储与存储声明页面。
        3.  在**存储卷**页签，选择所需的集群，单击**创建**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16689/156638078810740_zh-CN.png)

        4.  在创建数据卷对话框中，配置数据卷的相关参数。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16689/156638078810741_zh-CN.png)

            -   **存储卷类型**：本示例中为 OSS。
            -   **数据卷名**：创建的数据卷的名称。数据卷名在集群内必须唯一。本例为 pv-oss。
            -   **总量**：所创建存储卷的容量。
            -   **访问模式**：默认为 ReadWriteMany。
            -   **AccessKey ID**、**AccessKey Secret**：访问 OSS 所需的 AccessKey。
            -   **可选参数**：您可以为OSS存储卷输入定制化参数，格式为：`-o *** -o ***`。
            -   **Bucket ID**：您要使用的 OSS bucket 的名称。单击**选择Bucket**，在弹出的对话框中选择所需的 bucket 并单击**选择**。
            -   **访问域名**：如果 Bucket 和 ECS 实例位于不同地域（Region），请选择**外网域名**；如果位于相同地域，需要根据集群网络类型进行选择，若是 VPC 网络，请选择**VPC域名**，若是经典网络，请选择**内网域名**。
            -   **标签**：为该存储卷添加标签。
        5.  完成配置后，单击**创建**。
2.  创建 PVC。 使用oss-pvc.yaml文件创建 PVC。

    ``` {#codeblock_8t3_lac_vli}
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

3.  创建 Pod。 使用oss-deploy.yaml创建 Pod。

    ``` {#codeblock_i8u_9l4_2lb}
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


## 通过Secret方式使用AccessKey {#section_ct1_fy1_033 .section}

1.  执行如下命令，创建Secret。 

    ``` {#codeblock_ixp_mtn_g5g}
    # kubectl create secret generic osssecret --from-literal=akId='111111' --from-literal=akSecret='2222222' --type=alicloud/oss -n default
    
    akId: 为用户的Access Key ID；
    akSecret：为用户的Access Key Secret；
    secret类型：配置为alicloud/oss
    名词空间：需要与期望的pod相同在相同namespace；
    osssecret：为secret的名字，可以自行配置；
    ```

2.  在PV中使用Secret。 在PV中通过secretRef字段定义Secret。

    ``` {#codeblock_qww_yx3_j0p}
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

3.  在Volume中使用Secret。 Volume中通过secretRef字段定义Secret。

    ``` {#codeblock_64x_6fq_8dn}
    apiVersion: extensions/v1beta1
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

    **说明：** 使用Secret方式配置AccessKey时，应用（Pod）所在的Namespace要和Secret所在的Namespace一致。


