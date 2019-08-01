# Use Knative to deploy a Hello World application {#concept_525886 .concept}

This topic describes how to use Knative to deploy an application.

## Prerequisites {#section_klu_a1g_mjo .section}

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).
-   Knative is deployed on the Kubernetes cluster. For more information, see [Deploy Knative on a Kubernetes cluster](reseller.en-US/User Guide for Kubernetes Clusters/Knative management/Deploy Knative on a Kubernetes cluster.md#).
-   The Serving component is deployed on the Kubernetes cluster. For more information, see [Deploy a Knative component](reseller.en-US/User Guide for Kubernetes Clusters/Knative management/Deploy a Knative component.md#).

## Procedure {#section_zag_dev_oq9 .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Knative** \> **Services**.
4.  In the upper-righter corner, click **Create Service**.
5.  Set the following parameters:
    -   **Service Name**: Set the service name. In this example, the service name is set as helloworld-go.
    -   **Image Name**: Select an image by clicking **Select Image**, or in the box, enter a private registry that must be in the format of domainname/namespace/imagename:tag.

        **Note:** In this example, the registry registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used.

    -   **Image Version**: Select an image by clicking **Select Image Version**, or in the box, enter an image version. By default, if you do not set this parameter, the latest image version is used. In this example, the image version is set as 73fbdd56.
    -   **Environment Variables**: Set an environment variable by entering a key-value pair. In this example, the environment variable is set as `TARGET=Knative`.
6.  Click **Create**.

    On the Knative Service Manage page, the created service is then displayed.


## Verify the result {#section_h2i_6p5_hnm .section}

To access the created Knative service through its URL, follow these steps:

1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Knative** \> **Component** to view the IP address of the gateway of the Knative service.
2.  In the host file of your local host, associate the IP address of the gateway and the domain name of the Knative service with your local host by adding the following information:

    ``` {#codeblock_d5g_bzc_1u3}
    the IP address of the gateway + the domain name
    ```

    The following is a sample:

    ``` {#codeblock_tpb_of9_d8p}
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  In your browser, enter the URL http://helloworld-go.default.example.com to access the Knative service.

    ![Access the service](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156465951152568_en-US.png)


