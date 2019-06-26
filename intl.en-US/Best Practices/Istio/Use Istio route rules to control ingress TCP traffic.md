# Use Istio route rules to control ingress TCP traffic {#concept_qzh_bb5_cfb .concept}

This topic describes how to use standard Istio route rules to control ingress TCP traffic

## Background information {#section_gvz_cb5_cfb .section}

The routing model provided by Istio for traffic management decouples traffic from infrastructure. This means that you can directly manage the traffic generated to access an application by setting route rules for the traffic through Istio Pilot. This capability is supported by deployed sidecar proxies.

In most cases, an Istio service mesh contains one or more load balancers \(also referred to as gateways\). The ingress gateways terminate TLS from external networks and allow traffic to come into the service mesh. Then, the traffic flows through internal services through sidecar gateways. For more information, see [Gateways in an Istio service mesh](https://preliminary.istio.io/blog/2018/v1alpha3-routing/). An Istio gateway configures a load balancer that usually operates at the edge of the service mesh to process incoming or outgoing HTTP or TCP traffic. Multiple gateways can coexist within the same service mesh.

## Prerequisites {#section_6sp_nj2_su3 .section}

-   A Kubernetes cluster of V1.11.2 or later is created with ACK. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The Master node of the Kubernetes cluster is accessible. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
-   Netcat is installed in two local hosts.

    **Note:** 

    -   If you have not installed Netcat, use the tool \(yum, apt-get, or other tools\) according to your operating system to install it.
    -   Netcat is used in two local hosts as the terminals to access the target application \(specifically, a TCP server in this topic\). In this topic, the two terminals are referred to Terminal A and Terminal B.

## Step 1: Deploy Istio {#section_arq_klx_fhb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  Find the target cluster. Then, in the **Action** column, choose **More** \> **Deploy Istio**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156152158948684_en-US.png)

4.  Set the following parameters.

    |Setting|Description|
    |-------|-----------|
    | **Clusters** |The target cluster on which Istio is deployed.|
    | **Enable Prometheus for metrics/logs collection** |Indicates whether to enable Prometheus for metrics and logs collection. This is enabled by default.|
    | **Enable Grafana for metrics display** |Indicates whether to allow Grafana to display metrics. This is enabled by default.|
    | **Enable the Kiali Visualization Service Mesh** |Indicates whether to enable the Kiali visualization service mesh. Disabled by default. To enable this feature, set the following parameters:

     -    **Username**: The default is admin.
    -    **Password**: The default is admin.
 |
    | **Tracing Analysis Settings** |     -    **Enable Distributed Tracing with Jaeger**: Indicates whether to enable Jaeger \(the distributed tracing system\). To use Jaeger, select this radio button, and activate Alibaba Cloud Log Service.

**Note:** If you select this radio button, Log Service automatically creates a project namedistio-tracing-\{ClusterID\}that is used to store the tracking data.

    -    **Activate Tracing Analysis**: Indicates whether to activate the Tracing Analysis service. To activate this service, select this radio button, and then click **Activate now**. You must enter an endpoint address in the format ofhttp://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans.

**Note:** 

        -   An address of this format indicates an Internet or intranet endpoint that is used by a Zipkin client. This client is used to transmit collected data to the Tracing Analysis service, which then uses the API from v1 release.

        -   If you use an intranet endpoint, you must ensure that your Kubernetes cluster and the [Tracing Analysis](https://tracing-analysis.console.aliyun.com/) instance are in the same region to maintain stable network performance.
 |
    | **Pilot Settings** |The trace sampling percentage. The value range is from 0 to 100. The default value is 1.|
    | **Control Egress Traffic** |     -    **Permitted Addresses for External Access**: range of IP addresses that can be used to directly access services inthe Istio service mesh. By default, this field is left blank. Use commas \(,\) to separate multiple IP address ranges.
    -    **Blocked Addresses for External Access**: range of IP addresses that are blocked against external accesses. By default, this IP address range contains the cluster pod CIDR block and service CIDR block. Use commas \(,\) to separate multiple IP address ranges.
    -    **ALL**: Select this check box to block all the IP addresses used to access the Internet.
 **Note:** 

If the settings of these two parameters conflict with each other, the Permitted Addresses for External Access prevails.

For example, if an IP address is listed in both IP address ranges that you set for these two parameters, the IP address can be still accessed. That is, the setting of Permitted Addresses for External Access prevails.

 |

5.  Click **Deploy Istio**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156152158948685_en-US.png)


## Step 2: Build an image of a TCP server {#section_qsv_339_b8b .section}

You can directly use a built image file \(such as registry.cn-hangzhou.aliyuncs.com/wangxining/tcptest:0.1\), or following these steps to build an image of a TCP server:

1.  Clone a code repository from this address: https://github.com/osswangxining/Istio-TCPRoute-Sample.
2.  Switch to the code directory to verify that a file named `Dockerfile` is created.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156152158948687_en-US.png)

3.  Run the following command to build the image:

    ``` {#codeblock_vtq_x6j_jt9}
    docker build -t *\{the address of the target image repository\}* .
    ```


## Step 3: Deploy a TCP server {#section_3ik_kdj_qqs .section}

1.  Run the following commands:

    ``` {#codeblock_zun_otr_044}
    cd k8s
    kubectl apply -f deployment.yml
    kubectl apply -f service.yml
    ```

    **Note:** The preceding commands creates one service `tcp-echo`, and two deployments `tcp-echo-v1` and `tcp-echo-v2` that are labeled with app: tcp-echo. The service `tcp-echo` is associated with these two deployments by using the selector field.

    ``` {#codeblock_1ml_sol_je4}
    selector:
        app: "tcp-echo"
    ```

2.  Run the kubectl get pods --selector=app=tcp-echo command to verify that the pods of the TCP server is in running status.

    ``` {#codeblock_sbd_o8v_5ao}
    NAME                           READY     STATUS    RESTARTS   AGE
    tcp-echo-v1-7c775f57c9-frprp   2/2       Running   0          1m
    tcp-echo-v2-6bcfd7dcf4-2sqhf   2/2       Running   0          1m
    ```

3.  Run the kubectl get service --selector=app=tcp-echo command to verify the `tcp-echo` service of the TCP server is created.

    ``` {#codeblock_m0j_f1s_jir}
    NAME       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
    tcp-echo   ClusterIP   172.19.46.255   <none>        3333/TCP   17h
    ```


## Step 4: Set gateways for the TCP server {#section_ssr_23v_scj .section}

1.  Create the file gateway.yaml, and copy the following code to the file:

    ``` {#codeblock_icv_6zf_zcs}
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

2.  Run the kubectl apply -f gateway.yaml command to create gateways.

    **Note:** 

    -   Two gateways are created. One listens to port 31400, and the other one listens to port 31401.
    -   The two gateways share one service `istio-ingressgateway` of the `LoadBalancer` type. This service provides an Internet IP address for the TCP server.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156152158948688_en-US.png)


## Step 5: Create Istio route rules {#section_bgh_b35_dwe .section}

Create the files destination-rule-all.yaml and virtualservice.yaml, copy the following code to these two files, and then run the kubectl apply -f destination-rule-all.yaml and kubectl apply -f virtualservice.yaml commands for these two files, respectively.

``` {#codeblock_pim_9rg_d3g}
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

## Step 6: Verify the traffic is routed according to the rules {#section_x0s_01s_af1 .section}

1.  Obtain the IP address \(namely, the the external endpoint displayed in the console\) of the `istio-ingressgateway` service.
    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Services**.
    4.  Select the target cluster and namespace, and then find the external endpoint of the `istio-ingressgateway` service.
2.  Enable Terminal A to run the following command:

    ``` {#codeblock_b5e_c1u_2f1}
    nc INGRESSGATEWAY_IP 31400
    ```

3.  Enter `hello, app1` to verify that the traffic destined for port 31400 is forwarded to pod V1.

    ``` {#codeblock_lr4_737_fad}
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

4.  View the logs of pod V1.

    ``` {#codeblock_tp5_dq3_dmr}
    kubectl logs -f tcp-echo-v1-7c775f57c9-frprp -c tcp-echo-container | grep Received
    2018/10/17 07:32:29 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Raw Data: [104 101 108 108 111 44 32 97 112 112 49 10]
    2018/10/17 07:32:29 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Data (converted to string): hello, app1
    2018/10/17 07:34:40 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Raw Data: [99 111 110 116 105 110 117 101 46 46 10]
    2018/10/17 07:34:40 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Data (converted to string): continue..
    ```

5.  Enable Terminal B to run the following command:

    ``` {#codeblock_ccd_zhy_q0i}
    nc INGRESSGATEWAY_IP 31401
    ```

6.  Enter `hello, app2` to verify that the traffic destined for port 31401 is forwarded to pod V2.

    ``` {#codeblock_ae9_7cw_bcg}
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

7.  View the logs of pod V2.

    ``` {#codeblock_3y5_wxc_apo}
    kubectl logs -f tcp-echo-v2-6bcfd7dcf4-2sqhf -c tcp-echo-container | grep Received
    2018/10/17 07:36:29 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Raw Data: [104 101 108 108 111 44 32 97 112 112 50 10]
    2018/10/17 07:36:29 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Data (converted to string): hello, app2
    2018/10/17 07:36:37 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Raw Data: [121 101 115 44 116 104 105 115 32 105 115 32 97 112 112 50 10]
    2018/10/17 07:36:37 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Data (converted to string): yes,this is app2
    ```


