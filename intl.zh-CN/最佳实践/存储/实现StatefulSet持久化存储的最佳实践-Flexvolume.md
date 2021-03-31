---
keyword: [StatefulSet, 持久化存储]
---

# 实现StatefulSet持久化存储的最佳实践-Flexvolume

有状态服务StatefulSet支持通过VolumeClaimTemplate为每个Pod创建PV和PVC。并且删除和减少Pod时，不会删除StatefulSet的PV和PVC。本文为您介绍如何通过VolumeClaimTemplate实现StatefulSet持久化存储。

## 背景信息

有状态服务-StatefulSet的应用场景：

-   稳定的部署次序：有序部署或扩展，需要根据定义的顺序依次进行（即从0到N-1，在下一个Pod运行之前，所有之前的Pod必须都是Running和Ready状态）。
-   稳定的扩展次序：有序收缩或删除，需要根据定义的顺序依次进行（即从N-1到0，在下一个Pod运行之前，所有之前的Pod必须都是Running和Ready状态）。
-   稳定的网络标志：Pod重新调度后其PodName和HostName不变。
-   稳定的持久化存储：基于PVC，Pod重新调度后仍能访问到相同的持久化数据。

有状态服务-StatefulSet的使用方式：

通过`volumeClaimTemplates`自动创建PVC及PV。

文本将介绍：

-   创建StatefulSet服务
-   伸缩StatefulSet服务
-   删除StatefulSet服务
-   StatefulSet服务的持久化存储

## 前提条件

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

## 部署StatefulSet服务

**说明：** `volumeClaimTemplates`：表示一类PVC的模板，系统会根据有状态服务-StatefulSet配置的replicas数量，创建相应数量的PVC。这些PVC除了名字不一样之外其他配置都是一样的。

1.  创建statefulset.yaml文件。

    **说明：** `storageClassName`配置为alicloud-disk-ssd，表示使用的是阿里SSD类型的云盘。

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
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      replicas: 2
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
              name: web
            volumeMounts:
            - name: disk-ssd
              mountPath: /data
      volumeClaimTemplates:
      - metadata:
          name: disk-ssd
        spec:
          accessModes: [ "ReadWriteOnce" ]
          storageClassName: "alicloud-disk-ssd"
          resources:
            requests:
              storage: 20Gi
    ```

2.  执行以下命令，部署StatefulSet服务。

    ```
    kubectl create -f statefulset.yaml
    ```

3.  同时在另一个窗口中，执行以下命令，查看Pod按照次序部署。

    ```
    kubectl get pod -w -l app=nginx
    ```

    预期输出：

    ```
    NAME         READY   STATUS    RESTARTS   AGE
    web-0        0/1     Pending   0          0s
    web-0        0/1     Pending   0          0s
    web-0        0/1     ContainerCreating    0          0s
    web-0        1/1     Running   0          20s
    web-1        0/1     Pending   0          0s
    web-1        0/1     Pending   0          0s
    web-1        0/1     ContainerCreating    0          0s
    web-1        1/1     Running   0          7s
    ```

4.  执行以下命令，查看已部署的Pod。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          6m
    web-1                         1/1     Running   0          6m
    ```

5.  执行以下命令，查看PVC。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   7m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   6m
    ```


## 伸缩StatefulSet服务

扩容StatefulSet服务

1.  执行以下命令，扩容StatefulSet服务到3个Pod。

    ```
    kubectl scale sts web --replicas=3
    ```

    预期输出：

    ```
    statefulset.apps/web scaled
    ```

2.  执行以下命令，查看扩容后的Pod。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          34m
    web-1                         1/1     Running   0          33m
    web-2                         1/1     Running   0          26m
    ```

3.  执行以下命令，查看扩容后的PVC。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   35m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   34m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   27m
    ```


## 缩容StatefulSet服务

1.  执行以下命令，缩减StatefulSet服务到2个Pod。

    ```
    kubectl scale sts web --replicas=2
    ```

    预期输出：

    ```
    statefulset.apps/web scaled
    ```

2.  执行以下命令，查看缩容后的Pod。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          38m
    web-1                         1/1     Running   0          38m
    ```

    Pod的数量已缩减为2。

3.  执行以下命令，查看缩容后的PVC。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   31m
    ```

    PVC和PV并没有随Pod一起缩减。


## 再次扩容StatefulSet服务

1.  执行以下命令，扩容StatefulSet服务到3个Pod。

    ```
    kubectl scale sts web --replicas=3
    ```

    预期输出：

    ```
    statefulset.apps/web scaled
    ```

2.  执行以下命令，查看扩容后的Pod。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          1h
    web-1                         1/1     Running   0          1h
    web-2                         1/1     Running   0          8s
    ```

3.  执行以下命令，查看扩容后的PVC。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    扩容后新创建的Pod仍会使用原来的PVC和PV。


## 删除StatefulSet服务

1.  执行以下命令，查看名称为web-1的Pod，引用的PVC。

    ```
    kubectl describe pod web-1 | grep ClaimName
    ```

    预期输出：

    ```
    ClaimName:  disk-ssd-web-1
    ```

2.  执行以下命令，删除名称为web-1的Pod。

    ```
    kubectl delete pod web-1
    ```

    预期输出：

    ```
    pod "web-1" deleted
    ```

3.  执行以下命令，查看Pod。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          1h
    web-1                         1/1     Running   0          25s
    web-2                         1/1     Running   0          9m
    ```

    重新创建的Pod与删除前的Pod名称一致。

4.  执行以下命令，查看PVC。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    重新创建的Pod所使用的PVC与删除前的Pod一致。

5.  同时在另一个窗口中，执行以下命令，查看Pod删除和重新创建的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

    预期输出：

    ```
    NAME    READY   STATUS    RESTARTS   AGE
    web-0   1/1     Running   0          102m
    web-1   1/1     Running   0          69s
    web-2   1/1     Running   0          10m
    web-1   1/1   Terminating   0     89s
    web-1   0/1   Terminating   0     89s
    web-1   0/1   Terminating   0     90s
    web-1   0/1   Terminating   0     90s
    web-1   0/1   Pending   0     0s
    web-1   0/1   Pending   0     0s
    web-1   0/1   ContainerCreating   0     0s
    web-1   1/1   Running   0     20s
    ```


## StatefulSet服务的持久化存储

1.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec web-1 ls /data
    ```

    预期输出：

    ```
    lost+found
    ```

2.  执行以下命令，在/data路径下创建文件statefulset。

    ```
    kubectl exec web-1 touch /data/statefulset
    ```

3.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec web-1 ls /data
    ```

    预期输出：

    ```
    lost+found
    statefulset
    ```

4.  执行以下命令，删除名称为web-1的Pod。

    ```
    kubectl delete pod web-1
    ```

    预期输出：

    ```
    pod "web-1" deleted
    ```

5.  执行以下命令，查看/data路径下的文件，刚刚创建的文件statefulset并没有被删除，说明云盘的数据可持久保存。

    ```
    kubectl exec web-1 ls /data
    ```

    预期输出：

    ```
    lost+found
    statefulset
    ```


