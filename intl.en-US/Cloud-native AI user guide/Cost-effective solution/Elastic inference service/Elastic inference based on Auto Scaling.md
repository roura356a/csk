---
keyword: [Auto Scaling, elastic inference, workload]
---

# Elastic inference based on Auto Scaling

AI tasks have two types: model training and model inference. Model inference tasks are dynamically invoked based on the workload requirements. Conventional deployment solutions cannot meet the requirements of large-scale and highly concurrent systems. Alibaba Cloud allows you to deploy workloads based on Auto Scaling \(previously known as ESS\) to enable elastic scaling for inference services. This topic describes how to run elastic inference workloads based on Auto Scaling.

-   Model training is completed. In this topic, an image recognition model trained by TensorFlow is used.
-   The ack-alibaba-cloud-metrics-adapter component is installed. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).
-   The AI Dashboard component is installed. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Install Cloud-native AI Component Set and access AI Dashboard.md).

## Procedure

1.  Create an elastic node pool.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

    5.  Click **Create Node Pool**. In the dialog box that appears, set the parameters and click **Confirm Order**. The following table describes the parameters. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

        |Parameter|Description|
        |---------|-----------|
        |Auto Scaling|Select **Enable Auto Scaling**.|
        |Billing Method|Select Preemptible Instance.|
        |Node Label|Set **Key** to seldon and **Value** to fashion-mnist.|
        |Multi-Zone Scaling Policy|Select **Cost Optimization**. Set **Percentage of Pay-as-you-go Instances** to 30% and select **Enable Supplemental Pay-as-you-go Instances**.|

2.  Upload the trained model to an Object Storage Service \(OSS\) bucket. For more information, see [Upload objects](/intl.en-US/Quick Start/OSS console/Upload objects.md).

3.  Create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

    1.  Create a pvc.yaml file and add the following code to the file:

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: oss-csi-pv
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          persistentVolumeReclaimPolicy: Retain
          csi:
            driver: ossplugin.csi.alibabacloud.com
            volumeHandle: oss-csi-pv // The specified value must be the same as the name of the PV.
            volumeAttributes:
              bucket: "Your Bucket"
              url: "oss-cn-beijing.aliyuncs.com"
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
              akId: "Your Access Key Id"
              akSecret: "Your Access Key Secret"
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: oss-pvc
        spec:
          accessModes:
          - ReadWriteMany
          resources:
            requests:
              storage: 5Gi
        ```

        -   `bucket`: the name of the OSS bucket.
        -   `otherOpts`: the access path of the OSS bucket.
        -   `akId`: The AccessKey ID that is created in your account. Your account must have OSS permissions.
        -   `akSecret`: The AccessKey secret that is created in your account. Your account must have OSS permissions.
    2.  Run the following command to create the PV and PVC:

        ```
        kubectl apply -f pvc.yaml
        ```

4.  Deploy an inference service and a Horizontal Pod Autoscaler \(HPA\).

    1.  Create a fashion.yaml file and add the following code to the file:

        ```
        apiVersion: machinelearning.seldon.io/v1
        kind: SeldonDeployment
        metadata:
          name: fashion-mnist-ecs
          namespace: default
        spec:
          name: seldon
          replicas: 1
          annotations:
            workload: "seldon-94a0874115534be9acc71dd995d8e18d"
          predictors:
          - name: predictor
            replicas: 1
            componentSpecs:
            - hpaSpec:
                minReplicas: 1
                maxReplicas: 10
                metrics:
                - type: External
                  external:
                    metricName: sls_ingress_qps
                    metricSelector:
                      matchLabels:
                        sls.project: "k8s-log-c9522b942edaf4ec98c2cbc351ec8ade6"
                        sls.logstore: "nginx-ingress"
                        sls.ingress.route: "default-fashion-mnist-ecs-predictor-8000"
                    targetAverageValue: 10 
              spec:
                containers:
                - image: registry.cn-beijing.aliyuncs.com/deepai/seldonio-tfserving-proxy_rest:1.3.0
                  name: tfserving-proxy
                  env:
                    - name: SELDON_LOG_LEVEL
                      value: DEBUG
                  resources:
                    requests:
                      cpu: '0.5'
                - image:   tensorflow/serving:2.3.0-gpu
                  name: fashion-mnist
                  args:
                  - "/usr/bin/tensorflow_model_server"
                  - "--port=7001"
                  - "--model_name=fashion-mnist"
                  - "--model_base_path=/mnt/arena/models/fashion-mnist"
                  volumeMounts:
                  - name: model-storage
                    mountPath: /mnt
                  ports:
                  - containerPort: 7001
                    protocol: TCP
                  resources:
                    limits:
                      nvidia.com/gpu: 1
                    requests:
                      nvidia.com/gpu: 1
                  securityContext:
                    runAsUser: 1000
                nodeSelector:
                  seldon: "fashion-mnist"
                volumes:
                - name: model-storage
                  persistentVolumeClaim:
                    claimName: oss-pvc
                terminationGracePeriodSeconds: 1
            graph:
              name: tfserving-proxy
              endpoint:
                type: REST
              type: MODEL
              children: []
              parameters:
              - name: rest_endpoint
                type: STRING
                value: "http://127.x.x.x:8501"
              - name: model_name
                type: STRING
                value: fashion-mnist
              - name: signature_name
                type: STRING
                value: serving_default
        ```

        -   Configure the following parameters for the HPA:
            -   `minReplicas`: the minimum number of replicas.
            -   `maxReplicas`: the maximum number of replicas.
            -   `sls.project`: the name of the Log Service project that is used by the cluster.
            -   `sls.logstore`: the name of the Logstore that is used by the cluster.
            -   `sls.ingress.route`: the Ingress.
            -   `metricName`: the name of the metric that measures the number of queries per second \(QPS\).
            -   `targetAverageValue`: the QPS value that must be exceeded to trigger a scale-out event. In this example, the parameter value is set to 10. A scale-out event is triggered when the QPS value is larger than 10.
        -   Configure the following parameters for the inference service:
            -   `nodeSelector`: Set the value to the label that is added to the elastic node pool. The inference service schedules pods to the elastic node pool based on nodeSelector. In this example, the parameter value is set to `seldon: fashion-mnist`.
            -   `limits: nvidia.com/gpu`: the maximum number of GPUs that can be used by the service.
            -   `requests: nvidia.com/gpu`: the minimum number of GPUs that are reserved for the service.
            -   `model_name`: the name of the model.
            -   `model_base_path`: the path of the model.
    2.  Run the following command to deploy the inference service and the HPA:

        ```
        kubectl apply -f fashion.yaml
        ```

5.  Create an Ingress for the inference service.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

    5.  On the Ingresses page, click **Create** in the upper-right corner of the page.

    6.  In the Create dialog box, set the service to the inference service that is created in step [4](#step_xqi_62m_4su). For more information about the other parameters, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md). Click **Create**.

6.  Run the following command to query the Ingress:

    ```
    kubectl get ingress
    ```

    Expected output:

    ```
    NAME                CLASS    HOSTS                                                                             ADDRESS          PORTS   AGE
    fashion-mnist-ecs   <none>   fashion-mnist-ecs.c9522b942edaf4ec98c2cbc351ec8****.cn-beijing.alicontainer.com   101.200.xx.xxx   80      2m58
    ```

    You can visit the domain name of the Ingress to access the inference service.

7.  Test the inference service and then log on to AI Dashboard.

    Before you can log on to AI Dashboard, you must install and check whether you have access to AI Dashboard. For more information about how to install and access AI Dashboard, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Install Cloud-native AI Component Set and access AI Dashboard.md).

8.  In the left-side navigation pane of AI Dashboard, choose **Elastic Tasks** \> **Tasks** to view the details of the inference service.

    The following figure indicates that pods that are created during scale-out all run on Elastic Compute Service \(ECS\) instances. Both pay-as-you-go and preemptible ECS instances are provisioned. The ratio of the two instance types is based on the percentage value that you specified when you created the node pool.


