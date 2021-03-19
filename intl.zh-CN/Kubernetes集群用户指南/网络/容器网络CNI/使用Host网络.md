# 使用Host网络

Pod使用Host网络即让Pod使用Node节点的网络空间和网络资源。将Pod的网络配置成Host网络后，Pod可以直接访问到Node上Loopback设备，可以在Node节点上监听地址，也可以用于监控Node上别的Pod的网络流量。本文介绍如何使用Host网络。

-   您已经成功创建一个Kubernetes集群，参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。
-   您可以通过Kubectl连接到Kubernetes集群，参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

1.  打开您的YAML文件host-network.yaml，在spec下指定`hostNetwork: true`。

    YAML文件完整样例如下。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      hostNetwork: true
      containers:
      - name: nginx
        image: nginx
    ```

2.  执行以下命令，创建一个Pod。

    ```
    kubectl apply -f host-network.yaml
    ```

3.  执行以下命令，查看Pod的网络和其所在的Node网络是否一致。

    ```
    kubectl get pod -o wide
    ```

    当显示结果中，Pod的IP地址和宿主机的IP地址一致时，表示Host网络配置完成。

    ```
    NAME    READY   STATUS    RESTARTS   AGE   IP              NODE                           NOMINATED NODE
    nginx   1/1     Running   0          29s   192.168.XX.XX   cn-zhangjiakou.192.168.XX.XX   <none>
    ```


