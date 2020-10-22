# 更新Kubernetes集群已过期的证书

当集群证书已过期时，通过kubectl或API接口调用的方式与集群API Server的通讯都将被禁止，因此无法通过模板部署的方式来自动更新集群各节点上的相应过期证书；此时集群管理员可以登录至集群各节点，通过如下`docker run`启动容器的方式执行目标节点的证书更新任务。

## 更新Master节点已过期的证书

1.  以Root权限登录任意Master节点。

2.  在任意目录下执行以下命令，更新Master节点已经过期的证书。

    ```
    docker run -it --privileged=true  -v /:/alicoud-k8s-host --pid host --net host \
      registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0 /renew/upgrade-k8s.sh --role master
    ```

3.  在集群的每个Master节点，重复上述步骤，完成所有Master节点已过期的证书更新。


## 更新Worker节点已过期的证书

1.  以Root权限登录任意Master节点。

2.  执行以下命令，获取集群RootCA私钥。

    ```
    cat /etc/kubernetes/pki/ca.key
    ```

3.  执行以下命令，获取通过base64编码后集群的根私钥。

    -   若获取的集群RootCA私钥，有一行空行，执行以下命令：

        ```
        sed '1d' /etc/kubernetes/pki/ca.key| base64 -w 0
        ```

    -   若获取的集群RootCA私钥，无空行，执行以下命令：

        ```
        cat /etc/kubernetes/pki/ca.key | base64 -w 0
        ```

4.  以Root权限登录任意Worker节点。

5.  在任意目录下执行如下命令，更新Worker节点已经过期的证书。

    ```
    docker run -it --privileged=true  -v /:/alicoud-k8s-host --pid host --net host \
      registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0 /renew/upgrade-k8s.sh --role node --rootkey ${base64CAKey}
    ```

    **说明：** $\{base64CAKey\}为步骤3获取的通过base64编码后的集群根私钥。

6.  在集群的每个Worker节点，重复上述步骤，完成所有Worker节点已过期的证书更新。


