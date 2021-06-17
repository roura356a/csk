# Install the metrics-server component

This topic describes how to install the metrics-server component. In this case, you do not need to upgrade your Kubernetes cluster.

-   A Kubernetes cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   Kubernetes version 1.12.6 or earlier is used for the cluster.

To install the metrics-server component, perform the following steps: change the metric collector, switch the monitoring link, and update component settings.

## Change the metric collector

1.  Create a metrics-server.yaml file and copy the following content to the file. In the command-line interface \(CLI\), enter `kubectl apply -f metrics-server.yaml` to change the metric collector from Heapster to metrics-server.

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
    apiVersion: apps/v1
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
            image: registry. ##REGION##.aliyuncs.com/acs/metrics-server:v0.2.1-9dd9511-aliyun
            imagePullPolicy: Always
            command:
            - /metrics-server
            - '--source=kubernetes:https://kubernetes.default'
            - '--sink=socket:tcp://monitor.csk. ##REGION##.aliyuncs.com:8093? clusterId=##CLUSTER_ID##
    &public=true'
    ```

    **Note:** Replace `##REGION##` and `##CLUSTER_ID##` respectively with the region \(such as cn-hangzhou\) and ID of the selected cluster.


## Switch the monitoring link

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Find the master nodes in the cluster. Click the ID of a master node to go to the Instance Details page.

5.  In the Basic Information section, click **Connect**.

    The Enter VNC Password dialog box appears. Enter the VNC password and click **OK**. After you log on to the instance, run the following command:

    ```
    sed -i 's/--horizontal-pod-autoscaler-use-rest-clients=false/--horizontal-pod-autoscaler-use-rest-clients=true/' /etc/kubernetes/manifests/kube-controller-manager.yaml                            
    ```

    ![Switch the monitoring link 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6535359951/p39498.png)

6.  Repeat the preceding steps to switch the monitoring link for the other master nodes.


After the command is executed, the kubelet updates the controller manager.

## Update component settings

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

5.  Select the **kube-system** namespace. Find the **heapster** service and click **View in YAML** in the **Actions** column of the service.

6.  In the dialog box that appears, set the value of the k8s-app field to metrics-server. Click **Update**.

    ![Update component settings 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6535359951/p39500.png)

7.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

8.  On the Deployments tab, select the **kube-system** namespace.

9.  Select Heapster-related components such as heapster and monitoring-influxdb. In the **Actions** column, choose **More** \> **Delete**.

10. In the dialog box that appears, click **OK**.

    **Note:** To delete the monitoring-influxdb component, in the Delete monitoring-influxdb dialog box, select **Delete the associated monitoring-influxdb service** and click **OK**.

11. Check the link status.

    It requires about three minutes to initialize the link.

    In the left-side navigation pane, choose **Workload** \> **Pods**. On the Pods page, if the CPU and memory usage are normal, it indicates that the link is switched.

    **Note:** If the CPU and memory usage of all components are zero, it indicates that an error has occurred.


