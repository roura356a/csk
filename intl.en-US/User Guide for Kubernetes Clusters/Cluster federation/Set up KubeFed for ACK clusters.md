# Set up KubeFed for ACK clusters

This topic describes how to set up Kubernetes Cluster Federation \(KubeFed\) in the Container Service for Kubernetes \(ACK\) console.

-   Two ACK clusters are created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   You are familiar with how to [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   The [kubefedctl](https://github.com/kubernetes-sigs/kubefed/releases) command line tool is installed. The version of the kubefedctl must match the version of KubeFed.

1.  Deploy kubefed-operator.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

    3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-kubefed-init**.

        In the upper-right corner of the **App Catalog** page, you can enter **ack-kubefed-init** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

    4.  On the App Catalog - ack-kubefed-init page, select a cluster as the host cluster in the Deploy section.

    5.  On the App Catalog - ack-kubefed-init page, click the **Parameters** tab. On the Parameters tab, keep the default settings. Click Create in the Deploy section.

        In this example, **cluster1** is selected as the host cluster. Namespace is automatically set to kube-federation-system. Release Name is automatically set to kubefed-operator. On the page that appears after the application is deployed, you can find the related resource objects that are deployed.

        ![Release](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0319693061/p48998.png)

2.  Deploy the KubeFed environment.

    After ack-kubefed-init is deployed, you can use kubefed-operator to manage the cluster federation.

    1.  Prepare the required resources to deploy the KubeFed environment.

        ```
        apiVersion: kubefed.alibabacloud.com/v1beta1
        kind: Kubefed
        metadata:
          finalizers:
          - kubefed-operator.finializer.alibabacloud.com
          labels:
            controller-tools.k8s.io: "1.0"
          name: kubefed-config
          namespace: kube-federation-system
        spec:
          hub: registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog
          imagePullPolicy: IfNotPresent
          kubefed:
            replicaCount: 2
          logging:
            level: default:info
          scope: Cluster
          version: v0.1.0-rc6
        ```

        **Note:**

        -   Scope: specifies the scope of the KubeFed control plane.
        -   Version: specifies the version of KubeFed.
    2.  Create the required resources to delpoy the KubeFed environment.

        ```
        kubectl create -f kubefed-config.yaml
        ```

    3.  Check whether the KubeFed environment is deployed.

        Run the following command to check the status of the deployment:

        ```
        kubectl describe  kubefeds kubefed-config -n kube-federation-system
        ```

        The following output is returned:

        ```
        Name:         kubefed-config
        Namespace:    kube-federation-system
        Labels:       controller-tools.k8s.io=1.0
        Annotations:  <none>
        API Version:  kubefed.alibabacloud.com/v1beta1
        Kind:         Kubefed
        Metadata:
          Creation Timestamp:  2019-09-04T08:56:06Z
          Finalizers:
            kubefed-operator.finializer.alibabacloud.com
          Generation:        1
          Resource Version:  48747611
          Self Link:         /apis/kubefed.alibabacloud.com/v1beta1/namespaces/kube-federation-system/kubefeds/kubefed-config
          UID:               d4f26fff-cef1-11e9-b9f4-bec634a5****
        Spec:
          Hub:                registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog
          Image Pull Policy:  IfNotPresent
          Kubefed:
            Replica Count:  2
          Logging:
            Level:  default:info
          Scope:    Cluster
          Version:  v0.1.0-rc6
        Status:
          Error Message:  
          Status:         Available
        Events:           <none>
        ```

        If the value of the Status field is Available, it indicates that the KubeFed environment is deployed and you can add clusters to the cluster federation. The following table describes different states of the deployment.

        |State|Description|
        |-----|-----------|
        |Create|The deployment is being initialized.|
        |Reconciling|The deployment is in progress.|
        |ReconcileFailed|The deployment fails.|
        |Available|The deployment succeeds.|


