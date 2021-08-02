---
keyword: [ECS, elastic inference]
---

# Run elastic inference workloads on ECS instances

After a model training job is completed, the model is usually deployed as an inference service. The number of calls to an inference service dynamically changes based on the business requirements. Elastic scaling is required to handle different loads and reduce costs. Conventional deployment solutions cannot meet the requirements of large-scale and highly concurrent systems. Alibaba Cloud allows you to deploy workloads in node pools to enable elastic scaling for inference services. This topic describes how to run elastic inference workloads on Elastic Compute Service \(ECS\) instances.

-   The model is trained. In this topic, a BERT model trained with TensorFlow 1.15 is used.
-   The ack-alibaba-cloud-metrics-adapter component is installed. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).
-   The O&M console is installed. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).
-   [Install Arena]().

## Procedure

1.  Create an elastic node pool.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

    5.  In the upper-right corner of the **Node Pools** page, click **Create Node Pool**.

    6.  Click **Create Node Pool**. In the dialog box that appears, set the parameters and click **Confirm Order**. The following table describes the parameters. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

        |Parameter|Description|
        |---------|-----------|
        |Auto Scaling|Select **Enable Auto Scaling**.|
        |Billing Method|Select Preemptible Instance.|
        |Node Label|Set **Key** to inference and set **Value** to tensorflow.|
        |Multi-Zone Scaling Policy|Select **Cost Optimization**. Set **Percentage of Pay-as-you-go Instances** to 30% and select **Enable Supplemental Pay-as-you-go Instances**.|

2.  Upload the trained model to an Object Storage Service \(OSS\) bucket. For more information, see [Upload objects](/intl.en-US/Quick Start/OSS console/Upload objects.md).

3.  Create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

    1.  Create a file named pvc.yaml and copy the following code to the file:

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: model-csi-pv
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          persistentVolumeReclaimPolicy: Retain
          csi:
            driver: ossplugin.csi.alibabacloud.com
            volumeHandle: model-csi-pv // The value must be the same as the name of the PV. 
            volumeAttributes:
              bucket: "<Your Bucket>"
              url: "<Your oss url>"
              akId: "<Your Access Key Id>"
              akSecret: "<Your Access Key Secret>"
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: model-pvc
        spec:
          accessModes:
          - ReadWriteMany
          resources:
            requests:
              storage: 5Gi
        ```

        -   `bucket`: the name of the OSS bucket.
        -   `url`: the endpoint of the OSS bucket.
        -   `akId`: The AccessKey ID that is created in your account. Your account must have the permissions to access OSS resources.
        -   `akSecret`: The AccessKey secret that is created in your account. Your account must have the permissions to access OSS resources.
    2.  Run the following command to create the PV and PVC:

        ```
        kubectl apply -f pvc.yaml
        ```

4.  Run the following command to deploy the inference service:

    ```
    arena serve tensorflow \
      --name=bert-tfserving \
      --model-name=chnsenticorp  \
      --selector=inference=tensorflow \
      --gpus=1  \
      --image=tensorflow/serving:1.15.0-gpu \
      --data=model-pvc:/models \
      --model-path=/models/tensorflow \
      --version-policy=specific:1623831335
    ```

    **Note:** You must set the --selector parameter to deploy the model to the node pool that you created in Step 1.

    The following list describes the parameters of the inference service:

    -   `nodeSelector`: Set the value to the label that is added to the elastic node pool. The inference service uses nodeSelector to schedule pods to the elastic node pool. In this example, the value of this parameter is set to `inference: tensorflow`.
    -   `limits: nvidia.com/gpu`: the maximum number of GPUs that can be used by the service.
    -   `requests: nvidia.com/gpu`: the minimum number of GPUs that are reserved for the service.
    -   `model-name`: the name of the model.
    -   `model-path`: the path of the model.
5.  Create a Horizontal Pod Autoscaler \(HPA\).

    1.  Create a file named hpa.yaml and copy the following code to the file:

        ```
        apiVersion: autoscaling/v2beta1
        kind: HorizontalPodAutoscaler
        metadata:
          name: bert-tfserving-hpa
        spec:
          scaleTargetRef:
            apiVersion: apps/v1
            kind: Deployment
            name: bert-tfserving-202107141745-tensorflow-serving
          minReplicas: 1
          maxReplicas: 10
          metrics:
          - type: External
            external:
              metricName: sls_ingress_qps
              metricSelector:
                matchLabels:
                  sls.project: "k8s-log-c210fbedb96674b9eaf15f2dc47d169a8"
                  sls.logstore: "nginx-ingress"
                  sls.ingress.route: "default-bert-tfserving-202107141745-tensorflow-serving-8501"
              targetAverageValue: 10
        ```

        The following list describes the HPA parameters:

        -   -   `scaleTargetRef`: the object that is bound to the HPA.
-   `minReplicas`: the minimum number of pods.
-   `maxReplicas`: the maximum number of pods.
-   `sls.project`: the name of the Log Service project that is used by the cluster. The value of this parameter must be in the format of `k8s-log-{cluster id}`.
-   `sls.logstore`: the name of the Logstore that is used by the cluster. ``
-   `sls.ingress.route`: the name of the Ingress. The value of this parameter must be in the format of \{namespace\}-\{service name\}-\{service port\}.
-   `metricName`: the name of the metric that indicates the number of queries per second \(QPS\).
-   `targetAverageValue`: the QPS threshold that must be exceeded to trigger a scale-out event. In this example, the value of this parameter is set to 10. A scale-out event is triggered when the QPS is greater than 10.
    2.  Run the following command to deploy the HPA:

        ```
        kubectl apply -f hpa.yaml
        ```

6.  Configure an Internet-facing Ingress. For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Create an Ingress.md).

    **Note:** By default, the inference service that is deployed by running the `arena serve tensorflow` command is assigned only a cluster IP address. Therefore, the service cannot be accessed over the Internet. You must create an Ingress for the inference service based on the following parameter settings:

    -   Set **Namespace** to **inference**.
    -   Set Service **Port** to 8501. This port is exposed for the REST API.
7.  After you create the Ingress, go to the **Ingresses** page and find the Ingress. The value in the **Rules** column contains the endpoint of the Ingress.

    ![12](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1208687261/p295140.png)

8.  Perform a stress test on the inference service. Then, log on to the O&M console.

    Before you log on to the O&M console, you must install the cloud-native AI component set and configure access to the O&M console. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).

9.  In the left-side navigation pane of the O&M console page, choose **Elastic Job** \> **Job List**. You can view the details about the inference service.

    The following figure shows that all pods that are created in a scale-out event run on ECS instances. Both pay-as-you-go and preemptible ECS instances are provisioned. The ratio of the two types of instances is based on the percentage value that you specified when you created the node pool.

    ![ESS](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0556887261/p240489.png)


