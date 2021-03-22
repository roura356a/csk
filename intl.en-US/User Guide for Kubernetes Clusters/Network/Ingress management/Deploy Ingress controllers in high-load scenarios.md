---
keyword: [Ingress controller, high load]
---

# Deploy Ingress controllers in high-load scenarios

An Ingress is used to enable Layer 7 load balancing for external access to API objects in Kubernetes clusters. Ingress controllers are used to implement the features of Ingresses. This allows Ingresses to perform load balancing for external access based on Ingress rules. In high-load scenarios, insufficient CPU resources and network connections may downgrade application performance. This topic describes how to improve application performance in high-load scenarios by using Ingress controllers.

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An Ingress controller runs as normal in the ACK cluster.
-   [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) is installed.

## Considerations

Take note of the following items when you deploy the Ingress controller in a high-load scenario.

-   Elastic Compute Service \(ECS\) instance specifications

    When the cluster receives a large number of concurrent requests, Ingresses consume a large amount of CPU resources and network connections. We recommend that you use ECS instance types with enhanced performance, such as:

    -   ecs.c6e.8xlarge \(32 Core - 64 GB\): compute optimized instance type with enhanced performance. This instance type supports up to 6,000,000 packets per second \(PPS\).
    -   ecs.g6e.8xlarge \(32 Core - 128 GB\): general purpose instance type with enhanced performance. This instance type supports up to 6,000,000 packets per second \(PPS\).
-   Kubernetes configurations
    -   Use exclusive nodes to deploy the Ingress controller. Run the following commands to add labels and taints to the nodes:

        ```
        kubectl label nodes $node_name ingress-pod="yes"
        kubectl taint nodes $node_name ingress-pod="yes":NoExecute
        ```

    -   Set CPU Policy to `static`.
    -   We recommend that you select Super I \(slb.s3.large\) as the Server Load Balancer \(SLB\) specification for the ingress-controller Service.
    -   We recommend that you use Terway as the network plug-in and use the exclusive ENI mode.
-   Ingress controller configurations
    -   Configure guaranteed pods for the Ingress controller.
        -   Set the `requests` and `limits` parameters of the nginx-ingress-controller containers to 15 Core and 20 GiB.
        -   Set the `requests` and `limits` parameters of the init-sysctl init container to 100 m \(100 millicore\) and 70 MiB.
    -   Delete the `podAntiAffinity` parameters from the configurations of the Ingress controller pods. This way, a node can host two Ingress controller pods.
    -   Set the number of the pod replicas of the Ingress controller Deployment to a value that is twice the number of newly added nodes.
    -   Set `worker-processes` in the ConfigMap of the Ingress controller to 15. This reserves 15 worker processes for the system.
    -   Set `keepalive` in the ConfigMap of the Ingress controller to specify the maximum number of requests through a connection.
    -   Disable logging.

## Step 1: Add nodes

Create a node pool in the ACK cluster and add two nodes to the node pool.

Configure the node pool based on the following description. For more information, see [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md).

-   Set **Operating System** to **Alibaba Cloud Linux 2.1903**.
-   Set **Node Label** and **Taints**.
    -   Add a **taint**. Set **Key** to ingress-pod, set **Value** to yes, and set **Effect** to **NoExecute**.
    -   Add a **node label**. Set **Key** to ingress-pod and set **Value** to yes.
-   Set **CPU Policy** to **Static**.

![Create a node pool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9958936161/p236352.png)

## Step 2: Configure the Ingress controller

Run the `kubectl edit deploy nginx-ingress-controller -n kube-system` command to edit the configuration file of the Ingress controller based on the following description.

-   Delete the pod anti-affinity settings.

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

-   Set the `requests` and `limits` parameters for the init container.

    ```
    resources:
      limits:
        cpu: 100m
        memory: 70Mi
      requests:
        cpu: 100m
        memory: 70Mi
    ```

-   Set the `requests` and `limits` parameters of the nginx-ingress-controller containers to 15 Core and 20 GiB.

    ```
    resources:
      limits:
        cpu: "15"
        memory: 20Gi
      requests:
        cpu: "15"
        memory: 20Gi
    ```

-   Set node affinity and tolerations.

    ```
    nodeSelector:
      ingress-pod: "yes"
    tolerations:
    - effect: NoExecute
      key: ingress-pod
      operator: Equal
      value: "yes"
    ```

-   Set the number of the pod replicas of the Ingress controller Deployment to a value that is twice the number of the newly added nodes.
-   Disable metric collection by adding `--enable-metrics=false` to the startup parameters.

    **Note:** If you do not need metrics, we recommend that you disable metric collection.

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


## Step 3: Edit the ConfigMap of the Ingress controller

1.  Run the `kubectl edit cm nginx-ingress-controller -n kube-system` command to edit the ConfigMap of the Ingress controller. Modify the ConfigMap based on the following template:

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

    **Note:**

    -   worker-processes: specifies the number of NGINX processes to be started.
    -   keep-alive-requests: specifies the maximum number of requests through a connection.
    -   disable-access-log: disables logging.
2.  Import logs to a file and set up log rotation.

    By default, logs are written into the /dev/stdout file. When the cluster receives a large number of requests, the CPU usage is high. In this case, we recommend that you write logs into the /dev/stdout file and set up log rotation.

    1.  Use SSH to log on to the ECS instance where the ingress-controller pods are deployed. For more information, see [Log on to a Linux server by using SSH](https://developer.aliyun.com/article/701186).

    2.  Add the following content to the end of the /etc/crontab file:

        ```
        */15 * * * *  root /root/nginx-log-rotate.sh
        ```

        **Note:** In this example, the logs are rotated every 15 minutes. You can change the interval based on your requirements.

    3.  Create a file named nginx-log-rotate.sh in the /root directory.

        ```
        #! /bin/bash
        # Specify the maximum number of log files that are retained. You can change the number based on your requirements.
        keep_log_num=5
        ingress_nginx_container_ids=$(docker ps | grep nginx-ingress-controller  | grep -v pause | awk '{print $1}')
        if [[ -z "$ingress_nginx_container_ids" ]]; then
            echo "error: failed to get ingress nginx container ids"
            exit 1
        fi
        # Make the Ingress controller pods sleep for a random length of time period between 5 to 10 seconds.
        sleep $(( RANDOM % (10 - 5 + 1 ) + 5 ))
        for id in $ingress_nginx_container_ids; do
            docker exec $id  bash -c "cd /var/log/nginx; if [[ \$(ls access.log-* | wc -l) -gt $keep_log_num ]]; then rm -f \$(ls -t access.log-* | tail -1); fi ; mv access.log  access.log-\$(date +%F:%T) ; kill -USR1 \$(cat /tmp/nginx.pid)"
        done
        ```

    4.  Run the following command to make the nginx-log-rotate.sh file executable:

        ```
        chmod 755 /root/nginx-log-rotate.sh
        ```


