# Use Knative to deploy a Hello World application {#concept_525886 .concept}

This topic describes how to use Knative to deploy an application. In this topic, an example application named Hello World is deployed by using Knative.

## Prerequisites {#section_klu_a1g_mjo .section}

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Knative is deployed on the Kubernetes cluster. For more information, see [Deploy Knative on a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Knative management/Deploy Knative on a Kubernetes cluster.md#).
-   The Serving component is deployed on the Kubernetes cluster. For more information, see [Deploy a Knative component](reseller.en-US/User Guide/Kubernetes cluster/Knative management/Deploy a Knative component.md#).

## Procedure {#section_zag_dev_oq9 .section}

1.  Connect to the target Kubernetes cluster by using kubectl. For more information, see [kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
2.  Create the file helloworld-go.yaml, and copy the following code into the file:

    ``` {#codeblock_tez_w80_ehb}
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    metadata:
      name: helloworld-go
      namespace: default
    spec:
      template:
        spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:0529
              env:
                - name: TARGET
                  value: "Go Sample v1"
    ```

3.  Run the `kubectl apply -f helloworld-go.yaml` command to deploy an application.
4.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
5.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
6.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Services**.
7.  Select the target cluster and the istio-system namespace, and record the external endpoint of the istio-ingressgateway service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/474495/156143093348926_en-US.png)

8.  Run the following command to obtain the domain name:

    ``` {#codeblock_x5b_xal_t36}
    $ kubectl get ksvc helloworld-go  --output=custom-columns=NAME:.metadata.name,DOMAIN:.status.domain
    NAME            DOMAIN
    helloworld-go   helloworld-go.default.example.com
    ```

9.  Run the following command to verify that the deployed application is accessible:

    **Note:** You must replace 112.124.XX.XX with the IP address of the istio-ingressgateway service obtained in step 6.

    ``` {#codeblock_lt9_o8j_7e5}
    $ curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    Hello Go Sample v1!
    ```


