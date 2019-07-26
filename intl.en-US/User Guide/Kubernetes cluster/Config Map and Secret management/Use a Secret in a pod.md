# Use a Secret in a pod {#concept_e2n_vkn_vdb .concept}

This topic describes how to use a Secret in a pod. You can use a Secret to configure volumes and environment variables in a pod.

## Prerequisites {#section_gtv_cln_vdb .section}

-   A Secret named secret-test is created by using the following YAML template:

    ``` {#codeblock_clc_pfz_t7k}
    apiVersion: v1
    kind: Secret
    metadata:
      name: secret-test
    type: Opaque
    data:
      username: admin
      password: 12345  #You must encode your password by using Base64.
    ```

    For more information about how to create a secret, see [Create a Secret](reseller.en-US/User Guide/Kubernetes cluster/Config Map and Secret management/Create a Secret.md#).

-   The Secret and pod share the same cluster and namespace.
-   The Master node of your Kubernetes cluster is connected. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Use a Secret to configure pod volumes {#section_x41_rnn_vdb .section}

You can configure pod volumes by using either of the following methods:

-   Method 1: Configure pod volumes by running the `kubectl apply -f <example0.yaml>` command.

    **Note:** You must replace the example0.yaml file with the name of the target YAML file.

-   Method 2: Configure pod volumes in the Container Service console.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.
    3.  In the upper-right corner, click **Create from Image**. For information about how to create a deployment application by using an image, see [Create a deployment application by using an image](reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

        In the **Volume** section of the **Container** tab, click **Add Local Volume**, select Secret from the PV Type drop-down list, select secret-test from the Mount Source drop-down list, and set Container Path to the path where secret-test is saved.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/531954/156410822349492_en-US.png)


A Secret can be used as a file in a pod. For example, the username and password of the Secret \(secret-test\) is saved to the /srt directory as a file.

``` {#codeblock_4cn_vqm_z4f}
apiVersion: v1
kind: Pod
metadata:
  name: pod0
spec:undefined containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: srt
      mountPath: "/srt "
      readOnly: true
  volumes:
  - name: srt
    secret:
      secretName: secret-test
```

## Use a Secret to configure environment variables {#section_v19_8gc_ijm .section}

You can configure environment variables by using either of the following methods:

-   Method 1: Configure environment variables by running the `kubectl apply -f <example1.yaml>` command.

    **Note:** You must replace the example1.yaml file with the name of the target YAML file.

-   Method 2: Configure environment variables in the Container Service console.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.
    3.  In the upper-right corner, click **Create from Image**. For information about how to create a deployment application by using an image, see [Create a deployment application by using an image](reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

        In the **Environment Variable** section of the **Container** tab, click **Add**, select Secret from the Type drop-down list, select secret-test from the Value/ValueFrom drop-down list, select the target keys, and enter a name for the variables.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/531954/156410822349494_en-US.png)


For more information about Secrets, see [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

