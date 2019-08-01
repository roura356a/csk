# Associate an ENI with a pod {#task_fp2_w3v_vfb .task}

This topic describes how to associate an Elastic Network Interface \(ENI\) with a pod.

-   When you create a Kubernetes cluster, you need to select **Network Plugin** as **Terway**. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).
-   If you use a Kubernetes cluster that is installed with the Terway network plugin, you must make sure that the Terway plugin is V1.0.0.1 or later.

    **Note:** 

    1.  Log on to the Container Service console, click **Clusters** under the Kubernetes menu.
    2.  In the action column of the target cluster, choose **More** \> **Addon Upgrade**.
    3.  On the Addon Upgrade page, view your current version of Terway.
    4.  Determine whether to upgrade according to **Current Version** and **Upgradeable Version**. If you want to upgrade Terway, click **Upgrade** in the action column.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64468/156464846632549_en-US.png)


1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.
3.  In the upper-right corner, click **Create by Template**. 

    You can use the following YAML template to create a pod:

    ``` {#codeblock_m5e_ki3_d9t}
    apiVersion: v1
    kind: Pod
    metadata:
      name: terway-pod
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        resources:
          limits:
            aliyun/eni: 1
    ```


1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Pods**.

    The pod named **terway-pod** is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64468/156464846632570_en-US.png)

2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Click the name of the target cluster to view the cluster details.
4.  In the Cluster Resource area, click **VPC** to view the VPC CIDR block of the cluster.
5.  Run the following command to obtain the IP address of the deployed pod and verify that the IP address is within the VPC CIDR block of the cluster:

    ``` {#codeblock_h3b_gtk_sil}
    $ kubectl get pod -o wide
    ```


