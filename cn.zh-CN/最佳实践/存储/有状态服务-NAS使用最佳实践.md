# 有状态服务-NAS使用最佳实践 {#concept_ohj_lsm_2gb .concept}

本文为您介绍有状态服务-NAS的常见使用场景及方法。

## 背景信息 {#section_d1n_dxm_2gb .section}

NAS支持同时被多个Pod挂载，此时多个Pod可能同时修改相同数据，需要应用自行实现数据的同步。

**NAS的使用场景：**

-   对磁盘I/O要求较高的应用。
-   读写性能相对于对象存储OSS高。
-   可实现跨主机文件共享，例如：可作为文件服务器。

**NAS的使用方式：**

1.  手动创建文件系统，并添加挂载点。
2.  手动创建PV及PVC。

本文为您介绍利用阿里云提供的flexvolume插件，通过PV/PVC的方式使用阿里云NAS。

## 前提条件 {#section_y5c_mym_2gb .section}

-   您已成功创建一个Kubernetes集群，参见[创建Kubernetes集群](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您可以通过kubectl连接到Kubernetes集群，参见[通过 kubectl 连接 Kubernetes 集群](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   您已在文件存储控制台创建一个文件系统，参见[创建文件系统](../../../../../intl.zh-CN/快速配置指南/创建文件系统.md#)。创建的文件系统需要与您的Kubernetes集群在同一可用区。
-   您已在创建好的文件系统中添加容器服务Kubernetes集群的挂载点，参见[添加挂载点](../../../../../intl.zh-CN/快速配置指南/添加挂载点.md#)。文件系统挂载时的VPC网络要与您Kubernetes集群所在的VPC网络保持一致。

## 创建PV {#section_mgc_ggn_2gb .section}

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

    **参数解释**

    -   `alicloud-pvname`：PV的名称。
    -   `server`：NAS的挂载点。可在文件存储控制台，单击左侧导航栏的**文件系统列表**，选择目标文件系统，单击**操作**列**管理**，在挂载点区域，**挂载地址**即为NAS数据盘的挂载点。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81297/154694013034781_zh-CN.png)

    -   `path`：NAS的挂载目录，支持挂载NAS的子目录，当子目录不存在时，会在NAS上自动创建子目录并挂载。
    -   `vers`：（可选）NFS挂载协议的版本号，支持3和 4.0，默认情况是4.0。
    -   `mode`：（可选）挂载目录的访问权限，默认情况是不配置。

        **说明：** 

        -   挂载 NAS的根目录不能配置访问权限。
        -   当 NAS中数据量很大时，配置`mode`会导致挂载非常慢，甚至挂载失败，不建议配置。
2.  执行以下命令，创建PV。

    ```
    $ kubectl create -f pv-nas.yaml
    ```


**预期结果**

在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **存储卷**，进入存储卷列表页面，选择目标集群，可以看到刚刚创建的PV。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81297/154694013034784_zh-CN.png)

## 创建PVC {#section_yly_mkn_2gb .section}

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
    $ kubectl create -f pvc-nas.yaml
    ```


**预期结果**

在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **存储声明**，进入**存储声明列表**页面，选择目标集群和命名空间，可以看到刚刚创建的PVC。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81297/154694013034785_zh-CN.png)

## 创建应用 {#section_n4l_jln_2gb .section}

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
    $ kubectl create -f nas.yaml
    ```


**预期结果**

在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **部署**，进入部署列表页面，选择目标集群和命名空间，可以看到刚刚创建的deployment。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81297/154694013034786_zh-CN.png)

## NAS的共享存储 {#section_nmc_qmn_2gb .section}

1.  执行以下命令，查看部署的deployment所在Pod的名称。

    ```
    $ kubectl get pod
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-rcb2f       1/1     Running   0          9m
    nas-static-f96b6b5d7-wthmb       1/1     Running   0          9m
    ```

2.  执行以下命令，查看/data路径下的文件。

    ```
    $ kubectl exec nas-static-f96b6b5d7-rcb2f ls /data
    ```

    ```
    $ kubectl exec nas-static-f96b6b5d7-wthmb ls /data
    ```

    **说明：** /data路径下为空，没有文件。

3.  执行以下命令，在任意一个Pod的/data路径下创建文件nas。

    ```
    $ kubectl exec nas-static-f96b6b5d7-rcb2f touch /data/nas
    ```

4.  执行以下命令，查看/data路径下的文件。

    ```
    $ kubectl exec nas-static-f96b6b5d7-rcb2f ls /data
    nas
    ```

    ```
    $ kubectl exec nas-static-f96b6b5d7-wthmb ls /data
    nas
    ```

    **说明：** 在任意一个Pod的/data下创建的文件，两个Pod下的/data路径下均存在此文件，说明两个Pod共享一个NAS。


## NAS的持久化存储 {#section_nwt_q4n_2gb .section}

1.  执行以下命令，删除该应用的所有Pod。

    ```
    $ kubectl delete pod nas-static-f96b6b5d7-rcb2f nas-static-f96b6b5d7-wthmb
    pod "nas-static-f96b6b5d7-rcb2f" deleted
    pod "nas-static-f96b6b5d7-wthmb" deleted
    ```

2.  同时在另一个窗口中，执行以下命令，查看Pod删除及Kubernetes重建Pod的过程。

    ```
    $ kubectl get pod -w -l app=nginx
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-rcb2f       1/1     Running   0          27m
    nas-static-f96b6b5d7-wthmb       1/1     Running   0          27m
    nas-static-f96b6b5d7-rcb2f   1/1   Terminating   0     28m
    nas-static-f96b6b5d7-wnqdj   0/1   Pending   0     0s
    nas-static-f96b6b5d7-wnqdj   0/1   Pending   0     0s
    nas-static-f96b6b5d7-wnqdj   0/1   ContainerCreating   0     0s
    nas-static-f96b6b5d7-wthmb   1/1   Terminating   0     28m
    nas-static-f96b6b5d7-nwkds   0/1   Pending   0     0s
    nas-static-f96b6b5d7-nwkds   0/1   Pending   0     0s
    nas-static-f96b6b5d7-nwkds   0/1   ContainerCreating   0     0s
    nas-static-f96b6b5d7-rcb2f   0/1   Terminating   0     28m
    nas-static-f96b6b5d7-wthmb   0/1   Terminating   0     28m
    nas-static-f96b6b5d7-rcb2f   0/1   Terminating   0     28m
    nas-static-f96b6b5d7-rcb2f   0/1   Terminating   0     28m
    nas-static-f96b6b5d7-wnqdj   1/1   Running   0     10s
    nas-static-f96b6b5d7-wthmb   0/1   Terminating   0     28m
    nas-static-f96b6b5d7-wthmb   0/1   Terminating   0     28m
    nas-static-f96b6b5d7-nwkds   1/1   Running   0     17s
    ```

3.  执行以下命令，查看Kubernetes重建的Pod名称。

    ```
    $ kubectl get pod
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-nwkds       1/1     Running   0          21s
    nas-static-f96b6b5d7-wnqdj       1/1     Running   0          21s
    ```

4.  执行以下命令，查看/data路径下的文件。

    ```
    $ kubectl exec nas-static-f96b6b5d7-nwkds ls /data 
    nas
    ```

    ```
    $ kubectl exec nas-static-f96b6b5d7-wnqdj ls /data
    nas
    ```

    **说明：** 刚刚创建的文件nas并没有被删除，说明NAS的数据可持久保存。


