# Upgrade the Heapster components to the metrics-server component {#concept_cfx_ryj_wgb .concept}

This topic describes how to upgrade the Heapster components to the metrics-server component without upgrading the target Kubernetes cluster.

## Prerequisites {#section_y3f_r2f_xgb .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The Kubernetes cluster version is earlier than v1.12.

## Procedure {#section_pzr_r2f_xgb .section}

You must reset the data collection component, reset the monitoring data link, and then modify component compatibility settings.

-   Reset the data collection component.

    To change the data collection component from Heapster to metrics-server, follow these steps:

    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746039478_en-US.png)

    3.  In the upper-right corner, click **Create by Template**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746039496_en-US.png)

    4.  Select the target cluster from the **Clusters** drop-down list, and then select **kube-system** from the **Namespace** drop-down list.
    5.  Select **Custom** from the **Resource Type**, copy the following code and paste it to the **Template** area, then click **DEPLOY**.

        ```
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          name: admin
          namespace: kube-system
        ---
        apiVersion: rbac.authorization.k8s.io/v1beta1
        kind: ClusterRoleBinding
        metadata:
          name: admin
        roleRef:
          apiGroup: rbac.authorization.k8s.io
          kind: ClusterRole
          name: cluster-admin
        subjects:
        - kind: ServiceAccount
          name: admin
          namespace: kube-system
        ---
        apiVersion: v1
        kind: Service
        metadata:
          labels:
            task: monitoring
            # For use as a Cluster add-on (https://github.com/kubernetes/kubernetes/tree/master/cluster/addons)
            # If you are NOT using this as an addon, you should comment out this line.
            kubernetes.io/cluster-service: 'true'
            kubernetes.io/name: metrics-server
          name: heapster
          namespace: kube-system
        spec:
          ports:
          - port: 80
            targetPort: 8082
          selector:
            k8s-app: metrics-server
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: metrics-server
          namespace: kube-system
          labels:
            kubernetes.io/name: metrics-server
        spec:
          selector:
            k8s-app: metrics-server
          ports:
          - port: 443
            protocol: TCP
            targetPort: 443
        ---
        apiVersion: apiregistration.k8s.io/v1beta1
        kind: APIService
        metadata:
          name: v1beta1.metrics.k8s.io
        spec:
          service:
            name: metrics-server
            namespace: kube-system
          group: metrics.k8s.io
          version: v1beta1
          insecureSkipTLSVerify: true
          groupPriorityMinimum: 100
          versionPriority: 100
        ---
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: metrics-server
          namespace: kube-system
          labels:
            k8s-app: metrics-server
        spec:
          selector:
            matchLabels:
              k8s-app: metrics-server
          template:
            metadata:
              name: metrics-server
              labels:
                k8s-app: metrics-server
            spec:
              serviceAccountName: admin
              containers:
              - name: metrics-server
                image: registry.##REGION##.aliyuncs.com/acs/metrics-server:v0.2.1-9dd9511-aliyun
                imagePullPolicy: Always
                command:
                - /metrics-server
                - '--source=kubernetes:https://kubernetes.default'
                - '--sink=socket:tcp://monitor.csk.##REGION##.aliyuncs.com:8093?clusterId=##CLUSTER_ID##&public=true'
        ```

    **Note:** You need to replace `##REGION##` with the region \(for example, China East 1:cn-hangzhou\) to which your target cluster is located, and replace `##CLUSTER_ID##` with your target cluster ID.

-   Reset the monitoring data link.
    1.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.
    2.  Select the target **Cluster**.
    3.  Click the instance ID of one Master node. In this step, the master-01 node is used.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139497_en-US.png)

    4.  Click **Connect**. On the displayed page, enter the remote connection password and click **OK**. After you log on to the ECS instance, run the following command:

        ```
        sed -i 's/--horizontal-pod-autoscaler-use-rest-clients=false/--horizontal-pod-autoscaler-use-rest-clients=true/' /etc/kubernetes/manifests/kube-controller-manager.yaml
        
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139498_en-US.png)

    5.  Repeat step [c](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Upgrade the Heapster components to the metrics-server component.md#li_03) to step [d](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Upgrade the Heapster components to the metrics-server component.md#li_04) on the master-02 and master-03 nodes.

        After you complete the preceding operations, kubelet automatically starts and updates the kube-controller-manager component.

-   Reset the component compatibility settings.
    1.  In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Service**.
    2.  Select the target **Cluster** and the **kube-system** namespace. Then click **View YAML** on the right of **heapster**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139499_en-US.png)

    3.  In the displayed dialog box, reset the k8s-app parameter of the selector field to metrics-server. Then click **Update**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139500_en-US.png)

    4.  In the left-side navigation pane, choose **Application** \> **Deployment**.
    5.  Select the target **Cluster** and the kube-system **Namespace**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139506_en-US.png)

    6.  Delete the components related to Heapster. In this example, you need to delete the heapster and monitoring-influxdb components.

        -   On the right of the heapster component, choose **More** \> **Delete**. In the displayed dialog box, click **OK**.
        -   On the right of the monitoring-influxdb component, choose **More** \> **Delete**. In the displayed dialog box, select the **Delete associated services monitoring-influxdb** check box, and then click **OK**.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139501_en-US.png)

    7.  Check the new data link status.

        Data link initialization takes about three minutes.

        In the left-side navigation pane, choose **Application** \> **Pods**. You can verify that the CPU and memory columns show normal values. This means the data link has been reset.

        **Note:** If both the displayed CPU and memory columns of each component are 0, it indicates an exception.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155228746139502_en-US.png)


