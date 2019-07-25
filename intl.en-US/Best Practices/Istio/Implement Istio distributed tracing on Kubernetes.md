# Implement Istio distributed tracing on Kubernetes {#concept_q3c_4hd_5db .concept}

This topic details an example about how to use the distributed tracing system Jaegar in an Istio-enabled application.

When applications are transformed from the monolithic architecture to the microservices architecture, traditional monitoring tools cannot provide visibility across different services. This means that a distributed tracing tool must be implemented.

The OpenTracing specification has been created to address API incompatibility between different distributed tracing systems. Designed to be a lightweight, standardized layer, OpenTracing is located between application/class libraries and tracing or log analysis programs. OpenTracing has joined the Cloud Native Computing Foundation \(CNCF\) to provide unified concepts and data standards for global distributed tracing systems. It provides platform-independent and vendor-independent APIs. With OpenTracing, developers can easily add or replace tracing systems.

Jaeger is an open source distributed tracing system released by [CNCF](https://www.cncf.io/), and is compatible with OpenTracing APIs.

## Prerequisites {#section_llz_ljq_kfb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have a local Linux environment in which you have configured the kubectl tool and used the too to connected to the cluster. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
-   You have downloaded the project code of an Istio version and run the relevant commands in the Istio file directory. See [https://github.com/istio/istio/releases](https://github.com/istio/istio/releases).

## Step 1: Deploy the Jaeger tracing system in one click {#section_njx_szf_mfb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Clusters**. Select a cluster and choose **More** \> **Deploy Istio**.

    **Note:** This examples uses Istio V 1.0. For more information, see [Deploy Istio on a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Istio management/Deploy Istio on a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/156404693310115_en-US.png)

3.  On the Istio deployment page, select **Enable Log Service\(SLS\) and Jaeger**. With Alibaba Cloud Container Service, you can deploy the Jaeger tracing system in one click in the Web interface.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156404693413746_en-US.png)

4.  After you complete the deployment, choose **Application** \> **Service** in the left-side navigation tree, and select the target cluster and namespace to find the tracing-on-sls-query service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/156404693410117_en-US.png)

5.  Click the external endpoint of the tracing-on-sls-query service to display the Jaeger UI page.

    You can use Jaeger to observe the traces between services, diagnose performance, and analyze system faults.


## Step 2: Deploy a Bookinfo sample application of the distributed tracing service {#section_w1c_phd_5db .section}

1.  Label the `default` namespace with `istio-injection=enabled`.

    **Note:** Kubernetes clusters of Alibaba Cloud Container Service support one-click Istio deployment and automatic sidecar injection. For more information, see [Bookinfo Application](https://istio.io/docs/examples/bookinfo/).

    ``` {#codeblock_7x2_7j6_s2a}
    $ kubectl label namespace default istio-injection=enabled
    ```

2.  Use the following kubectl command to deploy a Bookinfo sample application:

    ``` {#codeblock_gjr_m3q_532}
    $ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
    ```

    The preceding command start all the four microservices. All three `reviews` service versions \(v1, v2, and v3\) are also started.

3.  Confirm all services and pods are properly defined and started.

    ``` {#codeblock_gcg_x9m_lx1}
    $ kubectl get svc,pods
    ```

4.  If you need to access the application from outside of your Kubernetes cluster, for example, from a browser, you must create an [Istio Gateway](https://istio.io/docs/concepts/traffic-management/#gateways) to define the ingress gateway for the application.

    ``` {#codeblock_c2t_fv6_aco}
    $ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
    ```

    Verify that the gateway has been created:

    ``` {#codeblock_xv4_yni_wgs}
    $ kubectl get gateway
    NAME               AGE
    bookinfo-gateway   32s
    ```

5.  Confirm the IP address of`istio-ingressgateway`. You can quickly query the IP address by running the following command:

    ``` {#codeblock_jwr_yae_9dl}
    $ kubectl get svc istio-ingressgateway -n istio-system
    ```

    You can also view the IP address of`istio-ingressgateway` through the Container Service console. In the left-side navigation pane, choose **Application** \> **Service**, and select the cluster and the Istio-system namespace.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/156404693410116_en-US.png)

6.  Access the Bookinfo home page. The access address is `http://{EXTERNAL-IP}/productpage`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/156404693413776_en-US.png)

    If you refresh the browser, different versions of the reviews are displayed on the productpage in a round-robin manner \(starting with a red star, to a black star, to no star\). This indicates that Istio is currently not being used to control the version routing.


## Step 3: View the tracing results displayed by Jaeger {#section_ign_cbg_mfb .section}

The Jaeger UI displays the results of the distributed service tracing.

1.  Click the external endpoint of the tracing-on-sls-query service to display the Jaeger UI page.
2.  On the left side, select a service from the **Service** drop-down list, set other parameters, and click **Find Traces**. The scatter diagram of time and duration displayed in the upper-right corner displays the results and supports drill-down capability.

    **Note:** In this example, the Bookinfo sample application is used to view the productpage service calling.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/156404693410119_en-US.png)

3.  You can select multiple views to visualize the tracing results, for example, a histogram within a tracing period, or the service duration within the tracing process.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/156404693510120_en-US.png)


## Istio distributed tracing mechanism {#section_kbc_phd_5db .section}

Although Istio proxies can automatically send spans, they need identifiers to tie together the entire trace. Applications need to propagate the appropriate HTTP headers so that when the proxies send span information, the spans can be correlated correctly into a single trace.

Therefore, an application needs to collect the following headers from incoming requests and propagate the headers to any outgoing requests:

``` {#codeblock_0g5_udl_5ic}
x-request-id
x-b3-traceid
x-b3-spanid
x-b3-parentspanid
x-b3-sampled
x-b3-flags
x-ot-span-context
```

The productpage service \(Python\) extracts the required headers from an HTTP request as follows:

``` {#codeblock_rou_13p_obt}
defgetForwardHeaders(request):
    headers = {}

    user_cookie = request.cookies.get("user")
    if user_cookie:
        headers['Cookie'] = 'user=' + user_cookie

    incoming_headers = [ 'x-request-id',
                         'x-b3-traceid',
                         'x-b3-spanid',
                         'x-b3-parentspanid',
                         'x-b3-sampled',
                         'x-b3-flags',
                         'x-ot-span-context'
    ]

    for ihdr in incoming_headers:
        val = request.headers.get(ihdr)
        if val isnotNone:
            headers[ihdr] = val
            #print "incoming: "+ihdr+":"+valreturn headers
```

When you call other services in your application, these headers are propagated to form a trace.

For information about the code, see the Istio document [https://istio.io/docs/tasks/telemetry/distributed-tracing.html](https://istio.io/docs/tasks/telemetry/distributed-tracing.html).

## Conclusion {#section_qbc_phd_5db .section}

You can use Alibaba Cloud Container Service for Kubernetes to quickly build an Istio open platform for connecting, managing, and securing microservices, and to introduce and configure multiple relevant services for applications. This topic uses an example to demonstrate how to use the distributed tracing system Jaeger in an Istio-enabled application. We recommend that you use Alibaba Cloud Container Service for Kubernetes to quickly build an Istio open platform of microservices and integrate Istio with microservices development in your project.

