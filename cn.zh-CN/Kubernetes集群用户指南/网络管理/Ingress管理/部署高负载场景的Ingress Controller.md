---
keyword: [Ingress Controller, 高负载]
---

# 部署高负载场景的Ingress Controller

在Kubernetes集群中，Ingress对集群服务（Service）中外部可访问的API对象进行管理，提供七层负载均衡能力。Ingress Controller负责实现Ingress的功能，根据规则配置负载均衡并提供访问入口。在高负载场景下，CPU资源和网络连接数的不足会影响应用的性能。本文介绍如何通过部署Ingress Controller来支撑高负载应用。

-   已创建Kubernetes集群。具体操作步骤，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   ACK集群中的Ingress Controller运行正常。
-   已安装[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令行工具。

## 部署说明

部署高负载场景下的Ingress Controller需要考虑以下三个方面。

-   硬件选型

    在高并发场景下，Ingress对CPU资源和网络连接数占用都非常高，所以可选增强型ECS实例，例如：

    -   计算型实例：ecs.c6e.8xlarge（32 Core 64 GB，600万PPS）
    -   网络型实例：ecs.g6e.8xlarge（32 Core 128 GB，600万PPS）
-   K8s配置
    -   设置Ingress Pod独占节点资源，添加污点和节点标签：

        ```
        kubectl label nodes $node_name ingress-pod="yes"
        kubectl taint nodes $node_name ingress-pod="yes":NoExecute
        ```

    -   设置CPU Policy为`static`。
    -   推荐调整ingress-controller service对应的SLB规格为超强型（slb.s3.large）。
    -   推荐集群使用Terway网络插件及配置独占ENI。
-   Ingress配置
    -   设置Ingress Pod为Guaranteed类型。
        -   设置nginx-ingress-controller container的资源限制`requests`和`limits`: 15 Core 20 GiB。
        -   设置initContainer init-sysctl的资源限制`requests`和`limits`: 100 m 70 MiB。
    -   删除Ingress Pod中的`podAntiAffinity`，使一个节点上可调度两个Pod。
    -   调整Deployment Replicas数为新增节点数的2倍。
    -   设置ConfigMap的`worker-processes`数为15（预留部分给系统使用）。
    -   调整ConfigMap的`keepalive`链接最大请求数。
    -   关闭日志访问记录。

## 步骤一：添加节点

1.  选择指定集群，创建新的节点池。

    创建节点池需要配置的主要参数如下所示。具体操作，请参见[创建节点池](/cn.zh-CN/Kubernetes集群用户指南/节点管理/节点池管理/创建节点池.md)。

    -   选择**操作系统**为**Alibaba Cloud Linux 2.1903**。
    -   配置**节点标签**和**污点（Taints）**。
        -   设置**污点（Taints）**的**键**为ingress-pod，**值**为yes，**Effect**为**NoExecute**。
        -   设置**节点标签**的**键**为ingress-pod，**值**为yes。
    -   选择**CPU Policy**为**Static**。
    ![创建节点池](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7991981161/p236352.png)

2.  添加2~3台节点到节点池。具体操作，请参见[添加已有节点](/cn.zh-CN/Kubernetes集群用户指南/节点管理/添加已有节点.md)。


## 步骤二：配置Ingress Controller

执行`kubectl edit deploy nginx-ingress-controller -n kube-system`命令打开Ingress Controller的配置文件，根据以下内容更新Ingress Controller的配置：

-   删除Pod反亲和性配置。

    ```
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
          operator: In
          values:
          - ingress-nginx
       topologyKey: kubernetes.io/hostname
    ```

-   配置InitContainer的`requests`和`limits`。

    ```
    resources:
      limits:
        cpu: 100m
        memory: 70Mi
      requests:
        cpu: 100m
        memory: 70Mi
    ```

-   修改nginx-ingress-controller container的`requests`和`limits`都为15 Core 20 GiB。

    ```
    resources:
      limits:
        cpu: "15"
        memory: 20Gi
      requests:
        cpu: "15"
        memory: 20Gi
    ```

-   设置节点亲和性和容忍性。

    ```
    nodeSelector:
      ingress-pod: "yes"
    tolerations:
    - effect: NoExecute
      key: ingress-pod
      operator: Equal
      value: "yes"
    ```

-   调整Replicas数为等于新增节点数的2倍。
-   关闭Metrics采集，修改启动参数，添加`--enable-metrics=false`。

    **说明：** 如果不需要获取Metrics信息，推荐关闭Metrics采集。

    ```
    containers:
    - args:
      - /nginx-ingress-controller
      - --configmap=$(POD_NAMESPACE)/nginx-configuration
      - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
      - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
      - --annotations-prefix=nginx.ingress.kubernetes.io
      - --publish-service=$(POD_NAMESPACE)/nginx-ingress-lb
      - --enable-metrics=false
      - --v=1
    ```


## 步骤三：配置Ingress ConfigMap

1.  使用`kubectl edit cm nginx-ingress-controller -n kube-system`更新ConfigMap，可参考以下代码示例：

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: nginx-configuration
      namespace: kube-system
    data:
      allow-backend-server-header: "true"
      enable-underscores-in-headers: "true"
      generate-request-id: "true"
      ignore-invalid-headers: "true"
      log-format-upstream: $remote_addr - [$remote_addr] - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time [$proxy_upstream_name] $upstream_addr $upstream_response_length $upstream_response_time $upstream_status $req_id $host [$proxy_alternative_upstream_name]
      max-worker-connections: "65536"
      proxy-body-size: 20m
      proxy-connect-timeout: "3"
      proxy-read-timeout: "5"
      proxy-send-timeout: "5"
      reuse-port: "true"
      server-tokens: "false"
      ssl-redirect: "false"
      upstream-keepalive-timeout: "900"
      worker-processes: 15
      worker-cpu-affinity: auto
      upstream-keepalive-connections: "300"
      upstream-keepalive-requests: "1000"
      keep-alive: "900"
      keep-alive-requests: "10000"
      disable-access-log: "true"
    ```

    **说明：**

    -   worker-processes：设置启动Nginx进程数。
    -   keep-alive-requests：设置单个长连接最多可以发送多个。
    -   disable-access-log：关闭日志访问记录。
2.  采用文件方式记录访问日志并对日志轮转。

    默认的日志访问记录输出到/dev/stdout，在高并发场景下，会占用大量CPU，故采用文件方式记录访问日志并对日志设置轮转。

    1.  以SSH方式登录部署ingress-controller Pod的ECS节点上。具体操作，请参见[如何使用SSH登录Linux服务器？](https://developer.aliyun.com/article/701186)。

    2.  在/etc/crontab文件结尾添加以下内容：

        ```
        */15 * * * *  root /root/nginx-log-rotate.sh
        ```

        **说明：** 此处以每隔15分钟对日志轮转一次为例，可根据实际需求进行调整。

    3.  在/root目录下添加文件nginx-log-rotate.sh。

        ```
        #!/bin/bash
        # 最多保留日志文件个数，可根据需求进行调整。
        keep_log_num=5
        ingress_nginx_container_ids=$(docker ps | grep nginx-ingress-controller  | grep -v pause | awk '{print $1}')
        if [[ -z "$ingress_nginx_container_ids" ]]; then
            echo "error: failed to get ingress nginx container ids"
            exit 1
        fi
        #随机睡眠5~10秒
        sleep $(( RANDOM % (10 - 5 + 1 ) + 5 ))
        for id in $ingress_nginx_container_ids; do
            docker exec $id  bash -c "cd /var/log/nginx; if [[ \$(ls access.log-* | wc -l) -gt $keep_log_num ]]; then rm -f \$(ls -t access.log-* | tail -1); fi ; mv access.log  access.log-\$(date +%F:%T) ; kill -USR1 \$(cat /tmp/nginx.pid)"
        done
        ```

    4.  执行以下命令，对nginx-log-rotate.sh文件添加可执行权限。

        ```
        chmod 755 /root/nginx-log-rotate.sh
        ```


