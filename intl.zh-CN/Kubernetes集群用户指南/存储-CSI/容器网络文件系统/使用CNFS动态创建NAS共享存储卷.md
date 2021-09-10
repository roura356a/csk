---
keyword: [CNFS, 共享存储卷]
---

# 使用CNFS动态创建NAS共享存储卷

当您的多个Kubernetes应用或Pod需要挂载相同的NAS存储卷共享数据时，可通过使用CNFS动态创建sharepath类型的共享存储卷。本文介绍如何使用CNFS动态创建NAS共享存储卷。

-   已创建Kubernetes集群，且存储插件选择为CSI。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
    -   若需要新建集群，选择CSI存储插件时请同时选中**创建默认NAS文件系统和CNFS容器网络文件系统动态存储类型**。
    -   若已创建集群，但未选中**创建默认NAS文件系统和CNFS容器网络文件系统动态存储类型**，请使用CNFS托管NAS文件系统。具体操作，请参见[使用CNFS托管NAS文件系统](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/容器网络文件系统/使用CNFS托管NAS文件系统.md)。
-   csi-plugin和csi-provisioner组件版本不低于v1.20.5-ff6490f-aliyun。关于升级CSI-Plugin和CSI-Provisioner组件的操作，请参见[升级CSI-Plugin和CSI-Provisioner](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/安装与升级CSI组件.md)。
-   已通过kubectl工具连接Kubernetes集群。具体操作，请参见[通过kubectl工具连接集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 操作步骤

您需要手动创建sharepath类型的storageclass并在containerNetworkFileSystem引用集群中CNFS的名称。

1.  执行以下命令查看CNFS的名称。

    ```
    kubectl get cnfs
    ```

    预期输出：

    ```
    NAME                                      AGE
    default-cnfs-nas-837d6ea-20210819155623   17d
    ```

2.  创建StorageClass。

    1.  使用以下YAML示例创建storageclass.yaml。

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alibabacloud-cnfs-nas-sharepath
        mountOptions:
          - nolock,tcp,noresvport
          - vers=3
        parameters:
          volumeAs: "sharepath"
          containerNetworkFileSystem: "default-cnfs-nas-837d6ea-20210819155623"
          path: "/sharepath"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        allowVolumeExpansion: true
        ```

        **说明：** NAS动态共享存储卷中volumeAs必须为sharepath，同时reclaimPolicy为Retain。如果reclaimPolicy为Delete，会导致动态创建的PVC为Pending状态。

    2.  执行以下命令创建StorageClass。

        ```
        kubectl create -f storageclass.yaml
        ```

3.  创建PVC1及引用该PVC1的Deployment1。

    1.  使用以下YAML模板创建nas-sharepath1.yaml。

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: pvc-nas-sharepath1
        spec:
          accessModes:
            - ReadWriteMany
          storageClassName: alibabacloud-cnfs-nas-sharepath
          resources:
            requests:
              storage: 10Gi
              
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-nas-sharepath1
          labels:
            app: nginx
        spec:
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
                  image: nginx:1.14.2
                  ports:
                    - containerPort: 80
                  volumeMounts:
                    - name: pvc-nas-sharepath1
                      mountPath: "/data"
              volumes:
                - name: pvc-nas-sharepath1
                  persistentVolumeClaim:
                    claimName: pvc-nas-sharepath1
        ```

    2.  执行以下命令创建PVC1及引用该PVC1的Deployment1。

        ```
        kubectl create -f nas-sharepath1.yaml
        ```

4.  在Deployment1的挂载目录下创建测试文件。

    1.  执行以下命令查看Deployment1。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                                         READY   STATUS    RESTARTS   AGE
        deployment-nas-sharepath1-586686b789-v4bwt   1/1     Running   0          6m
        ```

    2.  执行以下命令在Deployment1的/data目录下创建测试文件test.txt且写入`hello world`。

        ```
         kubectl exec deployment-nas-sharepath1-586686b789-v4bwt -ti sh
         cd /data
         echo "hello world" > test.txt
        ```

5.  再次创建一个PVC2及引用该PVC2的Deployment2。

    1.  使用以下YAML模板创建nas-sharepath2.yaml。

        **说明：**

        -   建议PVC2的storageClassName参数与PVC1的storageClassName参数配置相同。本示例PVC1和PVC2的storageClassName参数都配置为`alibabacloud-cnfs-nas-sharepath`。
        -   Deployment1与Deployment2的mountPath参数必须配置相同，即使用相同的NAS子目录实现共享。本示例为/data。
        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: pvc-nas-sharepath2
        spec:
          accessModes:
            - ReadWriteMany
          storageClassName: alibabacloud-cnfs-nas-sharepath
          resources:
            requests:
              storage: 10Gi
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-nas-sharepath2
          labels:
            app: nginx
        spec:
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
                  image: nginx:1.14.2
                  ports:
                    - containerPort: 80
                  volumeMounts:
                    - name: pvc-nas-sharepath2
                      mountPath: "/data"
              volumes:
                - name: pvc-nas-sharepath2
                  persistentVolumeClaim:
                    claimName: pvc-nas-sharepath2
        ```

    2.  执行以下命令创建PVC2及引用该PVC2的Deployment2。

        ```
        kubectl create -f nas-sharepath2.yaml
        ```

6.  在Deployment2的挂载目录下查看Deployment1的文件是否存在。

    1.  执行以下命令查看Deployment2。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                                         READY   STATUS    RESTARTS   AGE
        deployment-nas-sharepath2-74b85f4c86-tj9xz   1/1     Running   0          6m
        ```

    2.  执行以下命令进入Deployment2的/data目录下。

        ```
        kubectl exec deployment-nas-sharepath2-74b85f4c86-tj9xz -ti sh
        cd /data
        ```

    3.  执行以下命令查看/data下的文件。

        ```
        ls -arlth
        ```

        预期输出：

        ```
        total 8.5K
        drwxr-xr-x 2 root root 4.0K Sep  6 03:56 .
        -rw-r--r-- 1 root root   14 Sep  6 03:56 test.txt
        drwxr-xr-x 1 root root 4.0K Sep  6 03:57 ..
        ```

        从预期输出可得，Deployment2的/data目录下存在test.txt文件。

    4.  执行以下命令查看test.txt文件中的内容。

        ```
        cat test.txt
        ```

        预期输出：

        ```
        hello world
        ```

        从预期输出可得，Deployment2的/data目录下的test.txt文件内容与之前在Deployment1的/data目录下test.txt文件的写入内容一致。

7.  删除Deployment2、PVC2及其PV。

    ```
    kubectl delete deployment/deployment-nas-sharepath2 pvc/pvc-nas-sharepath2 pv/nas-daf9f0f0-16cf-412c-9a81-ad8b53490293
    ```

    预期输出：

    ```
    deployment.apps "deployment-nas-sharepath2" deleted
    persistentvolumeclaim "pvc-nas-sharepath2" deleted
    persistentvolume "nas-daf9f0f0-16cf-412c-9a81-ad8b53490293" deleted
    ```

8.  在Deployment1的/data目录下，查看test.txt文件。

    1.  执行以下命令进入Deployment1的/data目录下。

        ```
        kubectl exec deployment-nas-sharepath1-586686b789-v4bwt -ti sh
        cd /data
        ```

    2.  执行以下命令查看/data下的文件。

        ```
        ls
        ```

        预期输出：

        ```
        test.txt
        ```

        从预期输出可得，Deployment1的/data目录下存在test.txt文件。

    3.  执行以下命令查看test.txt文件中的内容。

        ```
        cat test.txt
        ```

        预期输出：

        ```
        hello world
        ```

        从预期输出及上述操作可得，当Deployment2及其PV/PVC删除时，并不影响共享子目录。


