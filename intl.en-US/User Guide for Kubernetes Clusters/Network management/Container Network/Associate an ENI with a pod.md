# Associate an ENI with a pod

This topic describes how to associate an Elastic Network Interface \(ENI\) with a pod.

-   When you create a Kubernetes cluster, you need to select **Network Plugin** as **Terway**. For more information, see [t16639.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   If you use a Kubernetes cluster that is installed with the Terway network plugin, you must make sure that the Terway plugin is V1.0.0.1 or later.

    **Note:**

    1.  Log on to the Container Service console, click **Clusters** under the Kubernetes menu.
    2.  In the action column of the target cluster, choose **More** \> **Addon Upgrade**.
    3.  On the Addon Upgrade page, view your current version of Terway.
    4.  Determine whether to upgrade according to **Current Version** and **Upgradeable Version**. If you want to upgrade Terway, click **Upgrade** in the action column.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1545359951/p32549.png)


1.  Log on to the [Container Service console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.

3.  In the upper-right corner, click **Create by Template**.

    You can use the following YAML template to create a pod:

    ```
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

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1545359951/p32570.png)

2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Click the name of the target cluster to view the cluster details.
4.  In the Cluster Resource area, click **VPC** to view the VPC CIDR block of the cluster.
5.  Run the following command to obtain the IP address of the deployed pod and verify that the IP address is within the VPC CIDR block of the cluster:

    ```
    $ kubectl get pod -o wide
    ```


