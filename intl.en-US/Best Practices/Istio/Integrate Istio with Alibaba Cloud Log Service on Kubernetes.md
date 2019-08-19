# Integrate Istio with Alibaba Cloud Log Service on Kubernetes {#concept_dmz_rbt_cfb .concept}

With Alibaba Cloud Container Service for Kubernetes, you can integrate Istio with Log Service. This topic uses an official example to describe how to integrate Istio with the distributed tracing system based on Alibaba Cloud Log Service.

 

## Prerequisites {#section_llz_ljq_kfb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US//Create a Kubernetes cluster.md#).
-   You have a local Linux environment in which you have configured the kubectl tool and connected to the cluster with kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).
-   You have downloaded the project codes of an Istio version and run the relevant commands in the Istio file directory. For more information, see [https://github.com/istio/istio/releases](https://github.com/istio/istio/releases).
-   You have deployed the test application, BookInfo. For more information, see[Install the Istio official sample application](reseller.en-US/Best Practices/Istio/Use Istio to implement intelligent routing in Kubernetes.md#section_csj_jln_cfb) or [Bookinfo Application](https://istio.io/docs/examples/bookinfo/).

## Introduction to OpenTracing {#section_fsj_vct_cfb .section}

The OpenTracing specification has been created to address API incompatibility between different distributed tracing systems. OpenTracing is a lightweight and standardized layer that is located between applications/class libraries and tracing or log analysis programs. OpenTracing has joined the Cloud Native Computing Foundation \(CNCF\) and provided unified concepts to provide data standards for global distributed tracing systems. It provides platform-independent and vendor-independent APIs. With OpenTracing, developers can easily add or replace tracing systems.

Jaeger is an open source distributed tracing system released by CNCF, and is compatible with OpenTracing APIs.

## Alibaba Cloud Log Service and the distributed tracing system Jaeger {#section_gsj_vct_cfb .section}

Log Service \(LOG for short, formerly called SLS\) is a one-stop service for real-time data. It has been used extensively by Alibaba Group in big data scenarios. Log service helps you collect, consume, ship, query, and analyze massive log data with improved processing capabilities.

Jaeger is an open source distributed tracing system released by Uber for microservice scenarios. It is used to analyze the calling process of multiple servers and display the service calling tracks with graphs. It is a powerful tool for diagnosing performance and analyzing system failures.

Jaeger on Aliyun Log Service is a distributed tracing system developed on Jaeger. With this system, you can store the collected tracing data to Alibaba Cloud Log Service permanently, and query and present the data through the native interfaces of Jaeger.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350612607_en-US.png)

|Component|Description|
|---------|-----------|
|Jaeger client|The Jaeger client provides SDKs that conform to the OpenTracing standard for different languages. Applications write data to the client through APIs. The client library delivers the traces to the Jaeger agent according to the sampling policy specified by the applications. The tracing data is serialized by Thrift and transmitted through UDP.|
|Jaeger agent|The Jaeger agent is a network daemon that listens to spans sent over UDP and sends data in batches to the Jaeger collector. The Jaeger agent is designed as an infrastructure component that is deployed to all hosts. The Jaeger agent decouples the Jaeger client and Jaeger collector, and makes the collectors invisible to the client. Collectors are then no longer routed or discovered by the client.|
|Jaeger collector|The Jaeger collector receives traces from the Jaeger agent and writes the traces into the backend storage system. The backend storage is a pluggable component. Jaeger on Aliyun Log Service supports Alibaba Cloud Log Service.|
|Alibaba Cloud Log Service|The Jaeger collector stores the received spans to Log Service permanently. Query retrieves data from Log Service.|
|Query & UI|Query is a service that receives query requests, retrieves traces from the backend storage system, and hosts a UI to display traces.|

## Step 1: Deploy Istio with a single click through the Web interface {#section_osj_vct_cfb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Clusters**, select a cluster, and click **More** \> **Deploy Istio**.

    **Note:** In this example, you need to enable Log Service \(SLS\) and Jaeger. For more information, see [Deploy Istio on a Kubernetes cluster](../../../../reseller.en-US/User Guide for Kubernetes Clusters/Istio management/Deploy Istio on a Kubernetes cluster.md#). This example uses Istio V1.0.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350612608_en-US.png)

3.  On the Istio deployment page, select **Log Service\(SLS\) and Jaeger**.

    Container Service for Kubernetes is integrated with Log Service. Its distributed tracing data is saved to the Logstore of Log Service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350613746_en-US.png)

    The descriptions of Log Service parameters are as follows.

    |Name|Parameter|Description|
    |----|---------|-----------|
    |**Endpoint**|storage.aliyun\_sls.endpoint|Specifies the endpoint in which the Project resides to store spans.|
    |**Project**|storage.aliyun\_sls.project|Specifies the Project that stores spans. The Project name must be a string of 3 to 63 bytes and must contain only lowercase letters, numbers, and hyphens \(-\). In addition, the name must start and end with lowercase letters or numbers.|
    |**Logstore**|storage.aliyun\_sls.logstore|Specifies the Logstore that stores spans. The Logstore name must be a string of 3 to 63 bytes that must contain lowercase letters, numbers, hyphens \(-\), and underscores \(\_\). The name must start and end with lowercase letters or numbers. A Logstore name must be unique in the Project to which it belongs.|
    |**AccessKeyID**|storage.aliyun\_sls.accesskey.id|Specify the user Access Key ID.|
    |**AccessKeySecret**|storage.aliyun\_sls.accesskey.secret|Specify the user Access Key Secret.|

    **Note:** If you specify a Project that does not exist, the system automatically creates and initializes a new Project.

4.  Click **Deploy Istio**. After a few minutes, the Istio integrated with Alibaba Cloud Log Service is created.
5.  In the left-side navigation pane, when you choose **Ingresses and Load Balancing** \> **Service**, the Istio services, including the integrated Log Service, are displayed on the right.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350712692_en-US.png)


## Step 2: View Jaeger on Aliyun Log Service {#section_ewt_4fg_mfb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Ingresses and Load Balancing** \> **Service**, click a cluster and the istio-system namespace, and then click the external endpoint of the tracing-on-sls-query service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350712696_en-US.png)

3.  On the left side, select a service from the **Service** drop-down list, set other parameters, and click **Find Traces**. The scatter diagram of time and duration displayed in the upper-right corner displays the results and provides drill-down capability.

    **Note:** In this example, the Bookinfo sample application is used to view the productpage service calling.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350712697_en-US.png)

4.  You can select from multiple views that display trace results in different ways. For example, you can select a histogram within a tracing period, or the service duration time of the tracing process.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/156620350812698_en-US.png)


## Step 3: View Log Service {#section_etj_vct_cfb .section}

1.  Log on to the [Log Service console](https://partners-intl.console.aliyun.com/#/sls).
2.  Select a Project and click the Project name.****
3.  Click **Search** on the right of a Logstore.

    ![](images/12699_en-US.png)

4.  Select a log query time range, and then click **Search & Analysis**.

    With Log Service, you can use query and analysis statements and other extended functions to optimize your query. For example, you can use analysis charts.

    ![](images/12700_en-US.png)

    The following is a statistical chart.

    ![](images/12701_en-US.png)


## Conclusion {#section_gtj_vct_cfb .section}

Alibaba Cloud Container Service for Kubernetes can integrate Istio with Log Service. This topic uses an official example to describe how to integrate Istio with the distributed tracing system based on Alibaba Cloud Log Service.

We recommend that you use Alibaba Cloud Container Service for Kubernetes to quickly build Istio, an open management platform for microservices, and integrate Istio with the microservice development of your project.

