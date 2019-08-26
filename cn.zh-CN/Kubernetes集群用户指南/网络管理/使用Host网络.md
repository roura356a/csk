# 使用Host网络 {#task_1797775 .task}

Pod使用Host网络，就是让Pod使用Node的网络空间和网络资源。配置成Host网络后，Pod可以直接访问到Node上loopback设备，可以在Node上监听地址，也可以用于监控Node上别的Pod的网络流量。

-   您已经成功创建一个 Kubernetes 集群，参见[ZH-CN\_TP\_16639.md\#](cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
-   您可以通过Kubectl连接到Kubernetes 集群，参见[通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。

1.  打开您的Yaml文件host-network.yaml，在spec下指定`hostNetwork: true`。 Yaml文件完整样例如下：

    ``` {#codeblock_xy1_tp3_jvv}
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

2.  执行如下命令，创建一个Pod。 

    ``` {#codeblock_5n2_640_h6l}
    kubectl apply -f host-network.yaml
    ```

3.  执行如下命令，查看Pod的网络和其所在的Node网络是否一致。 

    ``` {#codeblock_4n4_ka7_d1m}
    kubectl get pod -o wide
    ```

    当显示结果中，Pod的IP地址和宿主机的IP地址一致时，表示Host网络配置完成。

    ``` {#codeblock_9mr_ksl_7yn}
    NAME    READY   STATUS    RESTARTS   AGE   IP              NODE                           NOMINATED NODE
    nginx   1/1     Running   0          29s   192.168.XX.XX   cn-zhangjiakou.192.168.XX.XX   <none>
    ```


