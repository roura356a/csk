---
keyword: [资源调度, CPU拓扑感知调度, Kube-scheduler, CPU Policy]
---

# CPU拓扑感知调度

ACK基于新版的Scheduling framework实现CPU拓扑感知调度及控制自动绑核策略，针对CPU敏感型的工作负载提供更好的性能。本文介绍如何使用CPU拓扑感知调度，及如何通过设置CPU Policy控制自动绑核策略。

-   已创建ACK Pro版集群。具体操作，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

    **说明：** 目前CPU拓扑感知调度仅支持ACK Pro托管版集群。如果您需要专有版集群，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请白名单。

-   在使用CPU拓扑感知调度前，需要预先部署组件resource-controller。具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。
-   系统组件版本要求具体如下表所示。

    |组件|版本要求|
    |--|----|
    |Kubernetes|1.16及以上|
    |Helm版本|3.0及以上版本|
    |Docker版本|19.03.5|
    |操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04、Ubuntu 18.04、Alibaba Cloud Linux 2|


Kubernetes的节点会运行多个Pod，其中部分的Pod属于CPU密集型工作负载。在这种情况下，Pod之间会争抢节点的CPU资源。当争抢剧烈的时候，Pod会在不同的CPU Core之间进行频繁的切换，更糟糕的是在NUMA Node之间的切换。这种大量的上下文切换，会影响程序运行的性能。Kubernetes虽然有CPU Manager解决方案处理以上问题，但是因为CPU Manager特性是节点级别的CPU调度选择，所以无法在集群维度中选择最优的CPU Core组合。同时CPU Manager特性要求Pod是Guaranteed时（Pod中的每个容器必须指定CPU请求和CPU限制，并且两者要相等）才能生效，且无法适用于所有类型的Pod。

在以下场景中，建议使用CPU拓扑感知调度：

-   工作负载为计算密集型。
-   应用程序对CPU敏感。
-   运行在神龙裸金属（Intel、AMD）等多核机器上。

    通过在物理机上部署两个4核8 GB的Nginx并进行压测，可以发现在Intel（104核）、AMD（256核）的物理机上，使用CPU拓扑感知调度能够将应用性能提升22%~43%。具体如以下两个图所示：

    ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5639584261/p289461.png)

    ![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5639584261/p289462.png)

    |性能指标|Intel|AMD|
    |----|-----|---|
    |QPS|提升22.9%|提升43.6%|
    |AVG RT|下降26.3%|下降42.5%|


在激活CPU拓扑感知调度时，可通过在所创建Pod的annotations处设置cpu-policy为static-burst，从而控制自动绑核策略。该策略能够对计算密集性负载，有效避免进程的物理核心争用及跨片内存访问，最大化利用碎片化CPU，在不改变硬件及虚拟机资源的前提下为密集计算型负载优化可用资源，进一步提升CPU使用率。

## 注意事项

-   在使用CPU拓扑感知调度前，请保证已部署组件resource-controller。
-   在使用CPU拓扑感知调度时，请保证节点的`cpu-policy=none`。

## 使用CPU拓扑感知调度

激活CPU拓扑感知调度前，需要在提交Pod时设置annotations和Containers的值。满足以下条件：

-   在annotations处，设置cpuset-scheduler为true，激活CPU拓扑感知调度。
-   需要设置Containers的`resources.limit.cpu`值，且该值为整数。

1.  使用以下模板创建cal-pi.yaml文件，在Pod中使用CPU拓扑感知调度。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: cal-pi
      annotations: 
        cpuset-scheduler: 'true' #添加注解，表示CPU拓扑感知调度。
        #cpu-policy: 'static-burst' #添加注解，控制自动绑核策略同时提升CPU碎片利用率。
    spec:
      restartPolicy: Never
      containers:
      - image: registry.cn-zhangjiakou.aliyuncs.com/xianlu/java-pi
        name: cal-pi
        resources:
          requests:
            cpu: 4
          limits:
            cpu: 4  #需要设置resources.limit.cpu值。
        env:
        - name: limit
          value: "20000"
        - name: threadNum
          value: "3000"
    ```

    **说明：** 在配置CPU拓扑感知调度时，可通过在annotations下设置cpu-policy为static-burst，从而控制自动绑核策略。需要配置时，请删除cpu-policy前的`#`。

2.  使用以下模板创建go-demo.yaml文件，在Deployment中使用CPU拓扑感知调度。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: go-demo
    spec:
      replicas: 4
      selector:
        matchLabels:
          app: go-demo
      template:
        metadata:
          annotations:
            cpuset-scheduler: "true" #添加注解，表示CPU拓扑感知调度。
            #cpu-policy: 'static-burst' #添加注解，控制自动绑核策略，提升CPU碎片利用率。
          labels:
            app: go-demo
        spec:
          containers:
          - name: go-demo
            image: registry.cn-hangzhou.aliyuncs.com/haoshuwei24/go-demo:1k
            imagePullPolicy: Always
            ports:
            - containerPort: 8080
            resources:
              requests:
                cpu: 1
              limits: 
                cpu: 4  #需要设置resources.limit.cpu值。
    ```

    **说明：**

    -   在配置CPU拓扑感知调度时，可通过在annotations下设置cpu-policy为static-burst，从而控制自动绑核策略。需要配置时，请删除cpu-policy前的`#`。
    -   在`template.metadata`下配置Pod的`annotations`。
3.  执行以下命令，创建Pod及Deployment。

    ```
    kubectl create -f cal-pi.yaml go-demo.yaml
    ```


## 验证CPU拓扑感知对应用性能的提升

本示例使用场景为：

-   ACK Pro版集群版本为1.20。
-   所选实例的处理器型号为Intel Xeon\(Cascade Lake\) Platinum 8269CY或AMD Rome 7H12。
-   需要包含两个节点：一台压测机、一台测试机。

1.  执行以下命令，为测试机打上标签。

    ```
     kubectl label node 192.168.XX.XX policy=intel/amd
    ```

2.  在测试机上部署Nginx服务。

    1.  使用以下YAML示例，创建Nginx服务配置文件。
        -   service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service-nodeport
spec:
  selector:
      app: nginx
  ports:
    - name: http
      port: 8000
      protocol: TCP
      targetPort: 80
      nodePort: 32257
  type: NodePort
```

        -   configmap.yaml

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configmap
data:
  nginx_conf: |-
    user  nginx;
    worker_processes  4;
    error_log  /var/log/nginx/error.log warn;
    pid        /var/run/nginx.pid;
    events {
        worker_connections  65535;
    }
    http {
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;
        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
        access_log  /var/log/nginx/access.log  main;
        sendfile        on;
        #tcp_nopush     on;
        keepalive_timeout  65;
        #gzip  on;
        include /etc/nginx/conf.d/*.conf;
    }
```

        -   nginx.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      annotations:
        #cpuset-scheduler: "true"    默认情况，不使用CPU拓扑感知调度。
      labels:
        app: nginx
    spec:
      nodeSelector:
        policy: intel7
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 4
            memory: 8Gi
          limits:
            cpu: 4
            memory: 8Gi
        volumeMounts:
           - mountPath: /etc/nginx/nginx.conf
             name: nginx
             subPath: nginx.conf
      volumes:
        - name: nginx
          configMap:
            name: nginx-configmap
            items:
              - key: nginx_conf
                path: nginx.conf
```

    2.  执行以下命令，创建Nginx服务。

        ```
        kubectl create -f service.yaml configmap.yaml nginx.yaml
        ```

3.  登录压测机，执行以下命令下载`wrk`。

    **说明：** 关于如何登录到节点，请参见[通过密码认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)或[通过密码认证登录Windows实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Windows实例.md)。

    ```
    wget https://caishu-oss.oss-cn-beijing.aliyuncs.com/wrk?versionId=CAEQEBiBgMCGk565xxciIDdiNzg4NWIzMzZhZTQ1OTlhYzZhZjFhNmQ2MDNkMzA2 -O wrk
    chmod +755 wrk
    mv wrk /usr/local/bin
    ```

4.  执行以下命令，在压测机上进行测试并记录数据。

    ```
    taskset -c 32-45 wrk --timeout 2s -t 20 -c 100 -d 60s --latency http://<测试机IP>:32257
    ```

    预期输出：

    ```
    20 threads and 100 connections
    Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   600.58us    3.07ms 117.51ms   99.74%
    Req/Sec    10.67k     2.38k   22.33k    67.79%
    Latency Distribution
    50%  462.00us
    75%  680.00us
    90%  738.00us
    99%    0.90ms
    12762127 requests in 1.00m, 10.10GB read
    Requests/sec: 212350.15Transfer/sec:    172.13MB
    ```

5.  执行以下命令，删除已部署的Nginx。

    ```
    kubectl delete deployment nginx
    ```

    预期输出：

    ```
    deployment "nginx" deleted
    ```

6.  使用以下YAML示例，部署开启CPU拓扑感知调度的Nginx。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          annotations:
            cpuset-scheduler: "true"
          labels:
            app: nginx
        spec:
          nodeSelector:
            policy: intel7
          containers:
          - name: nginx
            image: nginx:latest
            ports:
            - containerPort: 80
            resources:
              requests:
                cpu: 4
                memory: 8Gi
              limits:
                cpu: 4
                memory: 8Gi
            volumeMounts:
               - mountPath: /etc/nginx/nginx.conf
                 name: nginx
                 subPath: nginx.conf
          volumes:
            - name: nginx
              configMap:
                name: nginx-configmap
                items:
                  - key: nginx_conf
                    path: nginx.conf
    ```

7.  执行以下命令，在压测机上进行测试并记录数据进行对比。

    ```
    taskset -c 32-45 wrk --timeout 2s -t 20 -c 100 -d 60s --latency http://<测试机IP>:32257
    ```

    预期输出：

    ```
    20 threads and 100 connections
    ls  Thread Stats   Avg         Stdev     Max       +/- Stdev
    Latency            345.79us    1.02ms    82.21ms   99.93%
    Req/Sec            15.33k      2.53k     25.84k    71.53%
    Latency Distribution
    50%  327.00us
    75%  444.00us
    90%  479.00us
    99%  571.00us
    18337573 requests in 1.00m, 14.52GB read
    Requests/sec: 305119.06Transfer/sec:    247.34MB
    ```

    通过两次数据的对比可以看出，开启CPU拓扑感知调度后，Nginx的性能提升43%。


## 验证自动绑核策略对应用性能的提升

本示例介绍在一个64核的节点上设置CPU Policy。在设置控制自动绑核策略后，相比于CPU拓扑感知调度一般可以提升7%~8%的CPU使用效率。

1.  Pod或Deployment成功运行后，执行以下命令，查看Pod。

    ```
    kubectl get pods | grep cal-pi
    ```

    预期输出：

    ```
    NAME                 READY     STATUS    RESTARTS   AGE
    cal-pi-d****         1/1       Running   1          9h
    ```

2.  执行以下命令，查看应用`cal-pi-d****`的日志。

    ```
    kubectl logs cal-pi-d****
    ```

    预期输出：

    ```
    computing Pi with 3000 Threads...computed the first 20000 digets of pi in 620892 ms! 
    the first digets are: 3.14159264
    writing to pi.txt...
    finished!
    ```

3.  根据[使用CPU拓扑感知调度](#section_xr4_ec7_r5z)，请删除cpu-policy前的`#`，在Pod及Deployment中使用CPU拓扑感知调度并控制自动绑核策略。

4.  Pod或Deployment成功运行后，执行以下命令，查看Pod。

    ```
    kubectl get pods | grep cal-pi
    ```

    预期输出：

    ```
    NAME                 READY     STATUS    RESTARTS   AGE
    cal-pi-e****         1/1       Running   1          9h
    ```

5.  执行以下命令，查看应用`cal-pi-e****`的日志。

    ```
    kubectl logs cal-pi-e****
    ```

    预期输出：

    ```
    computing Pi with 3000 Threads...computed the first 20000 digets of pi in 571221 ms!
    the first digets are: 3.14159264
    writing to pi.txt...
    finished!
    ```

    通过与[步骤2](#step_94g_fy7_thn)的日志数据对比，设置CPU Policy后Pod的性能提升8%。


