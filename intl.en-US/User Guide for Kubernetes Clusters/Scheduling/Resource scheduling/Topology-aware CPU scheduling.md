---
keyword: [resource scheduling, topology-aware CPU scheduling, kube-scheduler, CPU policy]
---

# Topology-aware CPU scheduling

Container Service for Kubernetes \(ACK\) provides the topology-aware CPU scheduling feature based on the new Kubernetes scheduling framework. This feature allows you to configure automatic CPU core binding policies and improves the performance of CPU-sensitive workloads. This topic describes how to enable topology-aware CPU scheduling and how to configure automatic CPU core binding policies by using CPU policies.

-   A professional Kubernetes cluster is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

    **Note:** Topology-aware CPU scheduling is available only for professional Kubernetes clusters. To enable topology-aware CPU scheduling for dedicated Kubernetes clusters, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to a whitelist.

-   resource-controller is deployed in the cluster before you enable topology-aware CPU scheduling. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).
-   The following table describes the versions that are required for the installed system components.

    |Component|Version|
    |---------|-------|
    |Kubernetes|V1.16 and later|
    |Helm|V3.0 and later|
    |Docker|19.03.5|
    |Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, Ubuntu 18.04, and Alibaba Cloud Linux 2.|


Multiple pods can run on a node in a Kubernetes cluster, and some of the pods may belong to CPU-intensive workloads. In this case, pods compete for CPU resources. When the competition becomes intense, the CPU cores that are allocated to each pod may frequently change. This situation intensifies when Non-Uniform Memory Access \(NUMA\) nodes are used. These changes degrade the performance of the workloads. The Kubernetes CPU manager provides a CPU scheduling solution to fix this issue within a node. However, the Kubernetes CPU manager cannot find an optimal allocation of CPU cores within a cluster. The Kubernetes CPU manager works only on guaranteed pods and does not apply to other types of pods. In a guaranteed pod, each container is configured with requests and limits on CPU resources, and the request and limit are set to the same value for each container.

Topology-aware CPU scheduling applies to the following scenarios:

-   The workload is compute-intensive.
-   The application is CPU-sensitive.
-   The workload runs on multi-core Elastic Compute Service \(ECS\) bare metal instances with Intel CPUs or AMD CPUs.

    To test the effect of topology-aware CPU scheduling, stress tests are performed on two NGINX applications that both request 4 CPU cores and 8 GB of memory. The tests are performed on applications that are deployed on an ECS bare metal instance with 104 Intel CPU cores and also applications that are deployed on an ECS bare metal instance with 256 AMD CPU cores. The results show that the application performance is improved by 22% to 43% when topology-aware CPU scheduling is enabled. The following figures show the details.

    ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4565007261/p289461.png)

    ![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4565007261/p289462.png)

    |Performance metric|Intel|AMD|
    |------------------|-----|---|
    |QPS|Improved by 22.9%|Improved by 43.6%|
    |AVG RT|Reduced by 26.3%|Reduced by 42.5%|


When you enable topology-aware CPU scheduling for an application, you can set cpu-policy to static-burst in the pod annotations. This allows you to configure automatic CPU core binding policies. This CPU policy is suitable for compute-intensive workloads. It can effectively reduce CPU core competition among processes and memory access between NUMA nodes. This maximizes the utilization of fragmented CPU resources and optimizes resource allocation for compute-intensive workloads without the need to modify the hardware and VM resources. This further improves CPU usage.

## Usage notes

-   resource-controller is deployed in the cluster before you enable topology-aware CPU scheduling.
-   When you enable topology-aware CPU scheduling, make sure that `cpu-policy=none` is configured for the node.

## Enable topology-aware CPU scheduling

To enable topology-aware CPU scheduling, you must set the annotations and containers parameters when you configure pods. Set the parameters in the following ways:

-   annotations: Set cpuset-scheduler to true to enable topology-aware CPU scheduling.
-   containers: Set `resources.limit.cpu` to an integer.

1.  Create a file named cal-pi.yaml by using the following template. You can use this file to create a pod with topology-aware CPU scheduling enabled.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: cal-pi
      annotations: 
        cpuset-scheduler: 'true' # Add this annotation to enable topology-aware CPU scheduling. 
        #cpu-policy: 'static-burst' # Add this annotation to configure automatic CPU core binding policies and improve the usage of fragmented CPU resources. 
    spec:
      restartPolicy: Never
      containers:
      - image: registry.cn-zhangjiakou.aliyuncs.com/xianlu/java-pi
        name: cal-pi
        resources:
          requests:
            cpu: 4
          limits:
            cpu: 4  # Specify the value of resources.limit.cpu. 
        env:
        - name: limit
          value: "20000"
        - name: threadNum
          value: "3000"
    ```

    **Note:** When you enable topology-aware CPU scheduling, you can set cpu-policy to static-burst in the annotations section. This allows you to configure automatic CPU core binding policies. When you add this configuration, delete `#` before cpu-policy.

2.  Create a file named go-demo.yaml by using the following template. You can use this file to create a Deployment with topology-aware CPU scheduling enabled.

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
            cpuset-scheduler: "true" # Add this annotation to enable topology-aware CPU scheduling. 
            #cpu-policy: 'static-burst' # Add this annotation to configure automatic CPU core binding policies and improve the usage of fragmented CPU resources. 
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
                cpu: 4  # Specify the value of resources.limit.cpu. 
    ```

    **Note:**

    -   When you enable topology-aware CPU scheduling, you can set cpu-policy to static-burst in the annotations section. This allows you to configure automatic CPU core binding policies. When you add this configuration, delete `#` before cpu-policy.
    -   In the `template.metadata` section, configure `annotations` of the pod.
3.  Run the following command to create the pod and Deployment:

    ```
    kubectl create -f cal-pi.yaml go-demo.yaml
    ```


## Test the application performance

In this example, the following conditions apply:

-   The Kubernetes version of the professional Kubernetes cluster is V1.20.
-   The processor model used by the nodes in this test is Intel Xeon Platinum 8269CY \(Cascade Lake\) or AMD EPYC Rome 7H12.
-   Two cluster nodes are used in the test. One is used to perform stress tests. The other runs the workload and serves as the tested machine.

1.  Run the following command to add a label to the tested machine:

    ```
     kubectl label node 192.168.XX.XX policy=intel/amd
    ```

2.  Deploy the NGINX service on the tested machine.

    1.  Use the following YAML templates to create resources for the NGINX service:
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
        #cpuset-scheduler: "true"    This is the default value, which disables topology-aware CPU scheduling. 
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

    2.  Run the following command to create the resources that are provisioned for the NGINX service:

        ```
        kubectl create -f service.yaml configmap.yaml nginx.yaml
        ```

3.  Log on to the node that is used to perform stress tests and run the following command to download `wrk`.

    **Note:** For more information about how to log on to a node of a Kubernetes cluster, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) or [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).

    ```
    wget https://caishu-oss.oss-cn-beijing.aliyuncs.com/wrk?versionId=CAEQEBiBgMCGk565xxciIDdiNzg4NWIzMzZhZTQ1OTlhYzZhZjFhNmQ2MDNkMzA2 -O wrk
    chmod +755 wrk
    mv wrk /usr/local/bin
    ```

4.  Run the following command to perform stress tests and record the test data:

    ```
    taskset -c 32-45 wrk --timeout 2s -t 20 -c 100 -d 60s --latency http://<IP address of the tested machine>:32257
    ```

    Expected output:

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

5.  Run the following command to delete the NGINX Deployment:

    ```
    kubectl delete deployment nginx
    ```

    Expected output:

    ```
    deployment "nginx" deleted
    ```

6.  Use the following YAML template to deploy an NGINX Deployment with topology-aware CPU scheduling enabled:

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

7.  Run the following command to perform stress tests and record the test data for comparison:

    ```
    taskset -c 32-45 wrk --timeout 2s -t 20 -c 100 -d 60s --latency http://<IP address of the tested machine>:32257
    ```

    Expected output:

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

    Compare the data of the preceding tests. This comparison indicates that the performance of the NGINX service is improved by 43% after topology-aware CPU scheduling is enabled.


## Verify that the automatic CPU core binding policies improve performance

In this example, a CPU policy is configured for a workload that runs on a node with 64 CPU cores. After you configure automatic CPU core binding policies for an application with topology-aware CPU scheduling enabled, the CPU usage can be further improved by 7% to 8%.

1.  After the pod or Deployment is started, run the following command to query the pod:

    ```
    kubectl get pods | grep cal-pi
    ```

    Expected output:

    ```
    NAME                 READY     STATUS    RESTARTS   AGE
    cal-pi-d****         1/1       Running   1          9h
    ```

2.  Run the following command to query the log of the `cal-pi-d****` application:

    ```
    kubectl logs cal-pi-d****
    ```

    Expected output:

    ```
    computing Pi with 3000 Threads...computed the first 20000 digets of pi in 620892 ms! 
    the first digets are: 3.14159264
    writing to pi.txt...
    finished!
    ```

3.  Enable topology-aware CPU scheduling for the pod or Deployment and configure an automatic CPU core binding policy as described in [Enable topology-aware CPU scheduling](#section_xr4_ec7_r5z). When you modify the configurations, delete `#` before cpu-policy.

4.  After the pod or Deployment is started, run the following command to query the pod:

    ```
    kubectl get pods | grep cal-pi
    ```

    Expected output:

    ```
    NAME                 READY     STATUS    RESTARTS   AGE
    cal-pi-e****         1/1       Running   1          9h
    ```

5.  Run the following command to query the log of the `cal-pi-e****` application:

    ```
    kubectl logs cal-pi-e****
    ```

    Expected output:

    ```
    computing Pi with 3000 Threads...computed the first 20000 digets of pi in 571221 ms!
    the first digets are: 3.14159264
    writing to pi.txt...
    finished!
    ```

    Compare the log data with the log data in [Step 2](#step_94g_fy7_thn). You can find that the performance of the pod configured with a CPU policy is improved by 8%.


