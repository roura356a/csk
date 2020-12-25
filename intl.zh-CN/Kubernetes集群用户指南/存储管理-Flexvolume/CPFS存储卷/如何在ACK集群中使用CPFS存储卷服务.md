---
keyword: [CPFS, 并行文件系统, 共享存储服务类型]
---

# 如何在ACK集群中使用CPFS存储卷服务

您可以在阿里云容器服务Kubernetes版（ACK）中使用文件存储CPFS（Cloud Paralleled File System）。本文介绍如何在ACK中安装Flexvolume插件，并通过CPFS数据卷的方式为应用（Pod）提供CPFS服务。

-   您已创建一个专有版Kubernetes集群，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   开放公网SSH访问，请参见[SSH访问Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过SSH访问Kubernetes集群.md)。

**说明：** ACK CPFS存储卷服务目前只支持CPFS 1.0版，不支持2.0版本。

CPFS是一种并行文件系统。CPFS的数据存储在集群中的多个数据节点，并可由多个客户端同时访问，从而能够为大型高性能计算机集群提供高IOPS、高吞吐、低时延的数据存储服务。CPFS是共享存储服务类型，适合于容器服务场景对资源共享、高性能的要求，在大数据、AI、基因计算等高性能场景中使用容器服务和CPFS是一个推荐的解决方案。有关CPFS详情，请参见[什么是文件存储CPFS]()。

## 步骤一、部署插件

ACK中使用CPFS依赖以下两个驱动类组件：

-   CPFS容器驱动：即为Flexvolume-cpfs插件，对Centos各个版本都兼容，部署Flexvolume-cpfs即可完成安装。
-   CPFS客户端驱动：为CPFS挂载时的客户端（类似于nfs-client），驱动与操作系统内核是强依赖。容器场景中安装CPFS客户端驱动有以下方式：

    -   手动安装驱动，请参见[挂载文件系统]()。
    -   Flexvolume-cpfs部署时自动安装驱动，但只支持部分内核版本。

        您可以通过在节点上执行命令`uname -a`查看内核版本。目前容器场景下您可以在以下内核版本中安装CPFS客户端驱动。

        ```
        3.10.0-957.5.1
        3.10.0-957.21.3
        3.10.0-1062.9.1
        ```

    **说明：**

    -   目前Flexvolume只支持安装CPFS Client驱动，不支持cpfs-client驱动升级，即发现节点安装了CPFS客户端，不再继续安装驱动。
    -   升级Flexvolume版本，只会升级Flexvolume驱动（容器驱动），而不会升级cpfs-client版本。
    -   在已经部署了cpfs-client、lustre驱动的节点上安装cpfs flexvolume不会再安装新版本的cpfs-client。
    -   cpfs-client驱动升级需要手动进行，具体操作请参见[挂载文件系统]()。

1.  在集群节点中部署YAML模板。

    1.  在客户端计算机中[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

    2.  创建名为flexvolume-cpfs.yaml的文件。

    3.  将以下模板拷贝至文件中。

        ```
        apiVersion: apps/v1
        kind: DaemonSet
        metadata:
          name: flexvolume-cpfs
          namespace: kube-system
          labels:
            k8s-volume: flexvolume-cpfs
        spec:
          selector:
            matchLabels:
              name: flexvolume-cpfs
          template:
            metadata:
              labels:
                name: flexvolume-cpfs
            spec:
              hostPID: true
              hostNetwork: true
              tolerations:
              - operator: "Exists"
              priorityClassName: system-node-critical
              affinity:
                nodeAffinity:
                  requiredDuringSchedulingIgnoredDuringExecution:
                    nodeSelectorTerms:
                    - matchExpressions:
                      - key: type
                        operator: NotIn
                        values:
                        - virtual-kubelet
              containers:
              - name: flexvolume-cpfs
                image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.14.8.96-0d85fd1-aliyun
                imagePullPolicy: Always
                securityContext:
                  privileged: true
                env:
                - name: ACS_CPFS
                  value: "true"
                - name: FIX_ISSUES
                  value: "false"
                livenessProbe:
                  exec:
                    command:
                    - sh
                    - -c
                    - ls /acs/flexvolume
                  failureThreshold: 8
                  initialDelaySeconds: 15
                  periodSeconds: 60
                  successThreshold: 1
                  timeoutSeconds: 15
                volumeMounts:
                - name: usrdir
                  mountPath: /host/usr/
                - name: etcdir
                  mountPath: /host/etc/
                - name: logdir
                  mountPath: /var/log/alicloud/
                - mountPath: /var/lib/kubelet
                  mountPropagation: Bidirectional
                  name: kubeletdir
              volumes:
              - name: usrdir
                hostPath:
                  path: /usr/
              - name: etcdir
                hostPath:
                  path: /etc/
              - name: logdir
                hostPath:
                  path: /var/log/alicloud/
              - hostPath:
                  path: /var/lib/kubelet
                  type: Directory
                name: kubeletdir
          updateStrategy:
            rollingUpdate:
              maxUnavailable: 10%
            type: RollingUpdate
        ```

    4.  执行以下命令部署上述YAML文件至集群节点中。

        ```
        `kubectl create -f flexvolume-cpfs.yaml`
        ```

2.  检查部署情况。

    1.  执行以下命令查看存储插件的部署情况。

        ```
        kubectl get pod -nkube-system | grep flex
        ```

        返回结果如下：

        ```
        flexvolume-97psk                                  1/1     Running   0          27m
        flexvolume-cpfs-dgxfq                             1/1     Running   0          98s
        flexvolume-cpfs-qpbcb                             1/1     Running   0          98s
        flexvolume-cpfs-vlrf9                             1/1     Running   0          98s
        flexvolume-cpfs-wklls                             1/1     Running   0          98s
        flexvolume-cpfs-xtl9b                             1/1     Running   0          98s
        flexvolume-j8zjr                                  1/1     Running   0          27m
        flexvolume-pcg4l                                  1/1     Running   0          27m
        flexvolume-tjxxn                                  1/1     Running   0          27m
        flexvolume-x7ljw                                  1/1     Running   0          27m
        ```

        **说明：** 以flexvolume-cpfs开头的Pod表示部署的CPFS存储卷插件。不含CPFS字样的flexvolume Pod表示集群默认部署的NAS、云盘、OSS存储卷插件。两个插件可以同时部署。

    2.  执行以下命令查看cpfs-client驱动是否安装完成。

        ```
        rpm -qa | grep cpfs
        ```

        返回结果如下：

        ```
        kmod-cpfs-client-2.10.8-202.el7.x86_64
        cpfs-client-2.10.8-202.el7.x86_64
        ```

    3.  执行以下命令查看mount.lustre是否安装完成。

        ```
        which mount.lustre
        ```

        返回结果如下：

        ```
        /usr/sbin/mount.lustre
        ```


## 步骤二、使用CPFS数据卷

在ACK中使用CPFS数据卷，需要您先到CPFS控制台创建一个CPFS卷和挂载点，参见[创建文件系统]()。

**说明：** 创建CPFS挂载点时，选择的VPC网络需要和ACK集群在同一个VPC内。

在以下示例中，获取的挂载点为 *cpfs-\*-alup.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp:cpfs--ws5v.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp*；文件系统ID为 *0237ef41*。

1.  创建PV。

    1.  创建名为pv-cpfs.yaml的文件。

    2.  拷贝以下模板至文件中。

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: pv-cpfs
          labels:
            alicloud-pvname: pv-cpfs
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          flexVolume:
            driver: "alicloud/cpfs"
            options:
              server: "cpfs-****-alup.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp:cpfs-***-ws5v.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp"
              fileSystem: "0237ef41"
              subPath: "/k8s"
              options: "ro"
        ```

        |参数|描述|
        |--|--|
        |server|配置为CPFS的挂载点。|
        |fileSystem|配置为CPFS文件系统ID。|
        |subPath|配置为期望挂载的CPFS子目录，相对于文件系统根目录。|
        |options|可选，挂载配置选项。|

    3.  执行以下命令创建PV。

        ```
        kubectl create -f pv-cpfs.yaml
        ```

2.  创建PVC。

    1.  创建pvc-cpfs文件，并拷贝以下模板至文件中。

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: pvc-cpfs
        spec:
          accessModes:
            - ReadWriteMany
          resources:
            requests:
              storage: 5Gi
          selector:
            matchLabels:
              alicloud-pvname: pv-cpfs
        ```

    2.  执行命令`kubectl create -f pvc-cpfs`创建PVC。

3.  创建应用。

    1.  创建nas-cpfs文件，并拷贝以下模板至文件中。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nas-cpfs
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
                  - name: pvc-cpfs
                    mountPath: "/data"
              volumes:
                - name: pvc-cpfs
                  persistentVolumeClaim:
                    claimName: pvc-cpfs
        ```

    2.  执行命令`kubectl create -f nas-cpfs`创建应用。


执行以下命令检查Pod挂载情况。

```
kubectl get pod
```

返回结果如下：

```
NAME                        READY   STATUS    RESTARTS   AGE
nas-cpfs-79964997f5-kzrtp   1/1     Running   0          45s
```

执行以下命令进入Pod查看挂载目录。

```
kubectl exec -ti nas-cpfs-79964997f5-kzrtp sh
mount | grep k8s
```

返回结果如下：

```
192.168.1.12@tcp:192.168.1.10@tcp:/0237ef41/k8s on /data type lustre (ro,lazystatfs)
```

执行以下命令进入Pod所在节点，查看挂载目录。

```
mount | grep cpfs
```

返回结果如下：

```
192.168.1.12@tcp:192.168.1.10@tcp:/0237ef41/k8s on /var/lib/kubelet/pods/c4684de2-26ce-11ea-abbd-00163e12e203/volumes/alicloud~cpfs/pv-cpfs type lustre (ro,lazystatfs)
```

**相关文档**  


[CSI中如何使用CPFS服务](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/docs/cpfs.md)

