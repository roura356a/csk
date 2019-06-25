# Deploy Knative on a Kubernetes cluster {#concept_525886 .concept}

This topic descries how to deploy Knative on a Kubernetes cluster that is created with Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Prerequisites {#section_klu_a1g_mjo .section}

-   A Kubernetes cluster supported by at least three Worker nodes is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Istio is deployed on the Kubernetes cluster. For more information, see [Deploy Istio](reseller.en-US/User Guide/Kubernetes cluster/Istio management/Deploy Istio.md#).
-   The Kubernetes cluster must be a standard dedicated or managed cluster that runs on Kubernetes V1.10 and later.

## Procedure {#section_zag_dev_oq9 .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Knative** \> **Components**.
4.  In the upper-right corner, click **Deploy**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/423002/156143073848852_en-US.png)

5.  Select the required components for Knative as needed, and then click **Deploy**.

    -   **Bulid**: This component is used to obtain the source code of an application from a code repository, compile the code into container images, and then push the images to an image repository.
    -   **Serving**: This component can be used to manage serverless workloads. It can work well with the Eventing component. It provides the request-driven capability to auto scale workloads. If no request is needed to be processed, Serving can help to scale workload instances to zero instance.
    -   **Eventing**: This component can be used to manage events.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/423002/156143073848853_en-US.png)


## Verify the result {#section_tz4_npu_1cp .section}

On the Deploy Knative page, verify that Knative is deployed.

-   To view component information, click **Go to components**.
-   To view operations related to applications, click **Go to applications**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/423002/156143073848854_en-US.png)

