# Use Istio route rules to manage inbound TCP traffic in a unified manner

This topic describes how to use standard Istio route rules to manage inbound TCP traffic in a unified manner.

The Istio traffic management model decouples traffic flow from infrastructure scaling. You can use Pilot to specify rules for traffic routing. Envoy proxies are deployed as sidecars to services. This sidecar proxy model allows you to manage traffic without modifying the application code.

A typical mesh has one or more load balancers that are referred to as Istio gateways. Istio gateways terminate TLS connections established from the Internet and allow traffic to flow into the mesh. Then, traffic flows to internal services through sidecar gateways. The following diagram shows the principles of gateways in an Istio mesh.

![Istio](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4002852061/p172850.png)

An Istio gateway enables load balancing for HTTP/TCP traffic and manages inbound traffic at the edge of a mesh. A mesh can have one or more gateways. We recommend that you do not use the Ingress APIs in Kubernetes to manage inbound traffic. The Ingress APIs cannot express the routing needs of Istio. An ingress in Kubernetes attempts to draw a common denominator across HTTP proxies. As a result, the ingress only supports the basic HTTP routing. You can only use non-portable annotations to enable advanced features of modern proxies. Different proxies use different annotation modes.

A Kubernetes ingress does not support the TCP protocol. Therefore, you cannot enable TCP load balancing by deploying an NGINX ingress controller. To enable TCP load balancing, create a ConfigMap in Kubernetes to configure NGINX. For more information, see [Support for TCP/UDP load balancing](https://github.com/nginxinc/kubernetes-ingress/tree/master/examples/tcp-udp). Such configurations are non-portable and have low compatibility among proxies.

To address this issue, Istio gateways allow you to configure only layer 4 to layer 6 settings, such as port and TLS settings. All major proxies enable these features in a unified manner. For layer 7 settings of the inbound traffic, Istio allows you to bind a VirtualService to a gateway. In this case, you can use standard Istio rules to manage HTTP and TCP traffic that flows through the gateway.

## Prerequisites

-   A Kubernetes cluster is created. The cluster version must be V1.11.2 or later. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   A master node is connected. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).
-   The netcat tool is installed. You can use yum or apt-get to install netcat.

## Deploy Istio

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Log on to the ACK console. In the left-side navigation pane, choose **Service Mesh** \> **Istio Management**.

3.  Configure the parameters for Istio based on the following table.

    |Parameter|Description|
    |---------|-----------|
    |Cluster|The cluster where you want to deploy Istio.|
    |Namespace|By default, the namespace is istio-system.|
    |Release Name|By default, the release name is Istio.|
    |Latest Version|The latest version of Istio.|
    |Pilot Settings|**Trace Sampling Percentage \(Valid values: 0 to 100\)**: By default, the value is 100.

**Enable Locality Load Balancing**: Istio enables global load balancing for application microservices through Envoy proxies. You can deploy microservice instances to clusters across multiple regions. Istio collects information about microservice instances, such as the running statuses, routing, and backend servers, and then sends the information to Envoy proxies. This allows Envoy proxies to distribute network traffic along the optimal routes to microservice instances that are deployed across multiple regions. If you enable this feature, Istio prioritizes traffic to the workload instances based on the localities where Envoy proxies send requests. If all instances run properly, the requests remain within the same locality. |
    |Control Egress Traffic|    -   **CIDR Blocks for Internal Access**: the range of IP addresses that cannot be accessed by services in the Istio mesh. By default, this IP address range contains the pod CIDR block and service CIDR block. Use commas \(,\) to separate multiple IP address ranges.

**All**: Select this check box to block all the IP addresses from being accessed.

    -   **CIDR Blocks for External Access**: the range of IP addresses that can be accessed by services in the Istio mesh. Use commas \(,\) to separate multiple CDIR blocks. By default, this parameter is left empty.
**Note:** The setting of CIDR Blocks for External Access takes priority over that of CIDR Blocks for Internal Access.

For example, if an IP address is contained in the values of both parameters, the setting of CIDR Blocks for External Access prevails. This IP address can be accessed by services in the Istio mesh. |
    |Gateway|Specifies whether to create a default ingress gateway. To create a default ingress gateway, select **Create Default Ingress Gateway** and set the following parameters:     -   **SLB Network Type**: **Public Network** and **Internal Network** are available.
    -   **Use Existing SLB Instance**: Select an existing Server Load Balancer \(SLB\) instance. If no SLB instance is available, click **Create SLB** next to the Use Existing SLB Instance drop-down list. For more information, see [Create an SLB instance](/intl.en-US/Quick Start (New Console)/Create an SLB instance.md).

**Note:** If you use an existing SLB instance, the existing listeners of the SLB instance are overwritten.

    -   **Port Mapping**: Set **Service Port** and **Container Port**. |
    |Certificate Management|Select or clear **Enable cert-manager**. You can use cert-manager to retrieve certificates by using key pairs stored in Kubernetes secrets.|
    |Observability|If you select **Enable Prometheus for Metrics/Logs Collection**, the following options are available:     -   **Install Prometheus**:

If you select this option, you can select **Persistent Storage**, and then set the TSDB Endpoint field.

**Note:** To enable persistent storage, log on to the [TSDB console](https://tsdb.console.aliyun.com/?spm=5176.2020520152.0.0.21bd16ddl1nNb7#/cluster/cn-hangzhou) and activate the time series database service.

    -   **Use Existing Prometheus**: If you select this option, you must enter the Prometheus service endpoint in the Endpoint field.

If the Prometheus service and Istio are deployed within the same cluster, you can set the Prometheus service endpoint in the following format: \[Service name\].\[ Namespace\]:\[Port\]. For example, http://prometheus-default-server.mygw1:80. If the Prometheus service and Istio are not deployed in the same cluster, you must specify an IP address that can be accessed by Istio. For example, http://1.2.3.4:9090. |
    |If you select **Enable Grafana for Metrics Display**, you must set the Username and Password fields. By default, both fields are set to admin.|
    |If you select **Enable Kiali Visualized Service Mesh**, you must set the Username and Password fields. By default, both fields are set to admin.|
    |Tracing Analysis Settings|Specifies whether to activate the tracing analysis service. To activate the service, select **Activate Tracing Analysis \(Activate Now\)**, and enter the endpoint of the tracing analysis service in the Endpoint field. For example, you can enter an endpoint in the format of http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans. An endpoint of this format indicates a public endpoint or an internal endpoint of the Zipkin API V1. A Zipkin client uses the endpoint to transmit collected data to the Tracing Analysis service.**Note:** If you use an internal endpoint, make sure that your Kubernetes cluster and the [Tracing Analysis instance](https://tracing-analysis.console.aliyun.com/) are deployed in the same region. In this case, the cluster and the Tracing Analysis instance can communicate with each other. |

4.  Click **Deploy Istio**. In the Message dialog box, click **OK**.

    At the bottom of the deployment page, you can view the deployment progress and status in real time.

    ![Deploy Istio](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3755359951/p48685.png)


## Build an image of a TCP server

You can use the following image file: registry.cn-hangzhou.aliyuncs.com/wangxining/tcptest:0.1. You can also perform the following steps to build an image of a TCP server.

1.  Clone a repository from the following address: https://github.com/osswangxining/Istio-TCPRoute-Sample.

2.  Switch to the code directory to verify that a file named Dockerfile is created.

    ![Dockerfile](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3755359951/p48687.png)

3.  Run the following command to build an image:

    ```
    docker build -t \{Image repository URL\}
    ```


## Deploy an application

1.  Run the following commands to deploy an application:

    ```
    cd k8s
    kubectl apply -f deployment.yml
    kubectl apply -f service.yml
    ```

    **Note:** The preceding commands create service `tcp-echo` and two deployments `tcp-echo-v1` and `tcp-echo-v2`. Both deployments have the app: tcp-echo label. Service `tcp-echo` is associated with the two deployments by using the selector field.

    ```
    selector:
        app: "tcp-echo"
    ```

2.  Run the following command to query the pod status on the TCP server: `kubectl get pods --selector=app=tcp-echo`

    ```
    NAME                           READY     STATUS    RESTARTS   AGE
    tcp-echo-v1-7c775f57c9-frprp   2/2       Running   0          1m
    tcp-echo-v2-6bcfd7dcf4-2sqhf   2/2       Running   0          1m
    ```

3.  Run the following command to query the service status on the TCP server: `kubectl get service --selector=app=tcp-echo`

    ```
    NAME       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
    tcp-echo   ClusterIP   172.19.46.255   <none>        3333/TCP   17h
    ```


## Create gateways

Add the following content to the gateway.yaml file and run the `kubectl apply -f gateway.yaml` command to create gateways. Two gateways are created. One gateway listens to port 31400, and the other listens to port 31401.

```
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: tcp-echo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 31400
      name: tcp
      protocol: TCP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: tcp-echo-gateway-v2
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 31401
      name: tcp
      protocol: TCP
    hosts:
    - "*"
```

The two gateways share the ingress gateway service of the LoadBalancer type. This service provides a public IP address for the TCP server.

![ingressgateway](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4755359951/p48688.png)

## Create Istio route rules

Add the following content to the destination-rule-all.yaml and virtualservice.yaml files. Then, run the `kubectl apply -f destination-rule-all.yaml` and `kubectl apply -f virtualservice.yaml` commands.

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: tcp-echo
spec:
  hosts:
  - "*"
  gateways:
  - tcp-echo-gateway
  - tcp-echo-gateway-v2
  tcp:
  - match:
    - port: 31400
      gateways:
        - tcp-echo-gateway
    route:
    - destination:
        host: tcp-echo.default.svc.cluster.local
        subset: v1
        port:
          number: 3333
  - match:
    - port: 31401
      gateways:
        - tcp-echo-gateway-v2
    route:
    - destination:
        host: tcp-echo.default.svc.cluster.local
        subset: v2
        port:
          number: 3333
```

## Verify TCP traffic routing

1.  Query the endpoint of the ingress gateway service.

    Log on to the Container Service for Kubernetes \(ACK\) console. In the left-side navigation pane, choose Ingresses and Load Balancing \> Services. On the page that appears, select the cluster and namespace that you want to manage, and then obtain the public endpoint of the ingress gateway service.

2.  Open the command-line interface \(CLI\) and run the following command:

    ```
    nc INGRESSGATEWAY_IP 31400
    ```

3.  Enter hello, app1 to verify that the traffic destined for port 31400 is forwarded to the pod on tcp-echo-v1.

    ```
    Welcome, you are connected to node cn-beijing.i-2zeij4aznsu1dvd4mj5c.
    Running on Pod tcp-echo-v1-7c775f57c9-frprp.
    In namespace default.
    With IP address 172.16.2.90.
    Service default.
    hello, app1
    hello, app1
    continue..
    continue..
    ```

4.  View the logs of the pod on tcp-echo-v1.

    ```
    kubectl logs -f tcp-echo-v1-7c775f57c9-frprp -c tcp-echo-container | grep Received
    2018/10/17 07:32:29 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Raw Data: [104 101 108 108 111 44 32 97 112 112 49 10]
    2018/10/17 07:32:29 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Data (converted to string): hello, app1
    2018/10/17 07:34:40 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Raw Data: [99 111 110 116 105 110 117 101 46 46 10]
    2018/10/17 07:34:40 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Data (converted to string): continue..
    ```

5.  Open another CLI and run the following command:

    ```
    nc INGRESSGATEWAY_IP 31401
    ```

6.  Enter hello, app2 to verify that the traffic destined for port 31401 is forwarded to the pod on tcp-echo-v2.

    ```
    Welcome, you are connected to node cn-beijing.i-2zeij4aznsu1dvd4mj5b.
    Running on Pod tcp-echo-v2-6bcfd7dcf4-2sqhf.
    In namespace default.
    With IP address 172.16.1.95.
    Service default.
    hello, app2
    hello, app2
    yes,this is app2
    yes,this is app2
    ```

7.  View the logs of the pod on tcp-echo-v2.

    ```
    kubectl logs -f tcp-echo-v2-6bcfd7dcf4-2sqhf -c tcp-echo-container | grep Received
    2018/10/17 07:36:29 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Raw Data: [104 101 108 108 111 44 32 97 112 112 50 10]
    2018/10/17 07:36:29 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Data (converted to string): hello, app2
    2018/10/17 07:36:37 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Raw Data: [121 101 115 44 116 104 105 115 32 105 115 32 97 112 112 50 10]
    2018/10/17 07:36:37 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Data (converted to string): yes,this is app2
    ```


