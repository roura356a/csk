# Create a Secret {#task_vjn_p2z_b2b .task}

This topic describes how to use the Container Service console to create a Secret.

A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

We recommend that you use Secrets for sensitive configurations in Kubernetes clusters, such as passwords and certificates.

Secrets have many types. For example:

-   Service Account: Automatically created by Kubernetes, which is used to access Kubernetes APIs and is automatically mounted to the pod directory /run/secrets/kubernetes.io/serviceaccount.
-   Opaque: Secret in the base64 encoding format, which is used to store sensitive information such as passwords and certificates.

By default, you can only create secrets of the Opaque type in the Container Service console. Opaque data is of the map type, which requires the value to be in the base64 encoding format. Alibaba Cloud Container Service supports creating Secrets with one click and automatically encoding the clear data to base64 format.

You can also manually create Secrets by using command lines. For more information, see [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) .

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Configuration** \> **Secrets**.
3.  Select the target cluster and namespace. Then, in the upper-right corner, click **Create**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16704/156035110510766_en-US.png)

4.  Complete the configurations to create a Secret. 

    **Note:** To enter the clear data of the secret, select the **Encode data values using Base64** check box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16704/156035110510767_en-US.png)

    1.  Name: Enter the Secret name, which must be 1–253 characters long, and can only contain lowercase letters, numbers, hyphens \(-\), and dots \(.\).
    2.  Configure the Secret data. Click the **add** icon next to Name and enter the name and value of the Secret, namely, the key-value pair. In this example, the Secret contains two values: `username:admin` and`passwrod：1f2d1e2e67df`.
    3.  Click **OK**.
5.  The Secret page appears. You can view the created Secret in the Secret list. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16704/156035110510768_en-US.png)


