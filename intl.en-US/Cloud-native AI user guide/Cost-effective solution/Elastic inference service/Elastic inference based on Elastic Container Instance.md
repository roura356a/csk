---
keyword: [Elastic Container Instance, elastic inference, workload]
---

# Elastic inference based on Elastic Container Instance

AI tasks have two types: model training and model inference. Model inference tasks are dynamically invoked based on the workload requirements. Conventional deployment solutions cannot meet the requirements of large-scale and highly concurrent systems. Alibaba Cloud allows you to deploy workloads on Elastic Container Instance to enable elastic scaling for inference services. This topic describes how to run elastic inference workloads on elastic container instances.

-   Model training is completed. In this topic, an image recognition model trained by TensorFlow is used.
-   The following components are installed. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md). The required components are:
    -   seldon-core
    -   ack-virtual-node
    -   ack-kubernetes-elastic-workload
    -   ack-alibaba-cloud-metrics-adapter
-   The AI Dashboard component is installed. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Install Cloud-native AI Component Set and access AI Dashboard.md).

## Procedure

1.  Upload the trained model to an Object Storage Service \(OSS\) bucket. For more information, see [Upload objects](/intl.en-US/Quick Start/OSS console/Upload objects.md).

2.  Create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

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

3.  Deploy an inference service.

    1.  Create a fashion.yaml file and add the following code to the file:

        ```
        apiVersion: machinelearning.seldon.io/v1
        kind: SeldonDeployment
        metadata:
          name: fashion-mnist-eci
          namespace: default
        spec:
          name: seldon
          annotations:
            workload: "seldon-4f51ae301054417c8bacf0fea6fb4321"
          replicas: 1
          predictors:
          - name: predictor
            replicas: 1
            componentSpecs:
            - spec:
                containers:
                - image: registry.cn-beijing.aliyuncs.com/deepai/seldonio-tfserving-proxy_rest:1.3.0
                  name: tfserving-proxy
                  resources:
                    requests:
                      cpu: '0.5'
                - image: tensorflow/serving:2.3.0-gpu
                  name: tfserving-gpu
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

        -   `limits: nvidia.com/gpu`: the maximum number of GPUs that can be used by the service.
        -   `requests: nvidia.com/gpu`: the minimum number of GPUs that are reserved for the service.
        -   `model_name`: the name of the model.
        -   `model_base_path`: the path of the model.
    2.  Run the following command to deploy the inference service:

        ```
        kubectl apply -f fashion.yaml
        ```

4.  Deploy the ElasticWorkload component.

    1.  Create an elasticworkload.yaml file and add the following code to the file:

        ```
        apiVersion: autoscaling.alibabacloud.com/v1beta1
        kind: ElasticWorkload
        metadata:
          name: fashion-mnist-eci-ew
        spec:
          sourceTarget:
            name: fashion-mnist-eci-predictor-0-tfserving-proxy-tfserving-gpu      
            kind: Deployment
            apiVersion: apps/v1
            min: 0                                                         
            max: 1                              
          replicas: 1                        
          elasticUnit:
          - name: virtual-kubelet-1
            annotations:                                                      
              virtual-kubelet: "true"
              k8s.aliyun.com/eci-use-specs: ecs.gn6i-c4g1.xlarge
            nodeSelector:                                                
              type: "virtual-kubelet"
            tolerations:                                                 
            - key: "virtual-kubelet.io/provider"
              operator: "Exists"
            min: 1
            max: 3
          - name: virtual-kubelet-2
            annotations:                                                      
              virtual-kubelet: "true"
              k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
              k8s.aliyun.com/eci-use-specs: ecs.gn6i-c4g1.xlarge
            nodeSelector:                                                
              type: "virtual-kubelet"
            tolerations:                                                 
            - key: "virtual-kubelet.io/provider"
              operator: "Exists"
            min: 1
            max: 10
        ```

        By default, one replica and two elastic units are created.

        -   The first elastic unit is a standard elastic container instance and can provide 1 to 3 replicas.
        -   The second elastic unit is a preemptible elastic container instance and can provide 1 to 10 replicas.
        When a scale-out event occurs and the number of required replicas exceeds 1, the elastic container instance specified by the first elastic unit is created. When the number of required replicas exceeds the maximum number of replicas provided by the first elastic unit, the elastic container instance specified by the second elastic unit is created.

    2.  Run the following command to deploy the ElasticWorkload component:

        ```
        kubectl apply -f elasticworkload.yaml
        ```

5.  Create a Horizontal Pod Autoscaler \(HPA\).

    1.  Create an hpa.yaml file and add the following code to the file:

        ```
        apiVersion: autoscaling/v2beta1
        kind: HorizontalPodAutoscaler
        metadata:
          name: fashion-mnist-eci-hpa
          namespace: default
        spec:
          scaleTargetRef:
            apiVersion: autoscaling.alibabacloud.com/v1beta1
            kind: ElasticWorkload
            name: fashion-mnist-eci-ew
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
                  sls.ingress.route: "default-fashion-mnist-eci-predictor-8000"
              targetAverageValue: 10
        ```

        -   `scaleTargetRef`: The `scaleTargetRef` parameter specifies the object that is bound to the HPA. In this example, the ElasticWorkload named fashion-mnist-eci-ew is bound to the HPA.
        -   `minReplicas`: the minimum number of replicas.
        -   `maxReplicas`: the maximum number of replicas.
        -   `sls.project`: the name of the Log Service project that is used by the cluster.
        -   `sls.logstore`: the name of the Logstore that is used by the cluster.
        -   `sls.ingress.route`: the Ingress.
        -   `metricName`: the name of the metric that measures the number of queries per second \(QPS\).
        -   `targetAverageValue`: the QPS value that must be exceeded to trigger a scale-out event. In this example, the parameter value is set to 10. A scale-out event is triggered when the QPS value is larger than 10.
    2.  Run the following command to create the HPA:

        ```
        kubectl apply -f hpa.yaml
        ```

6.  Create an Ingress for the inference service.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

    5.  On the Ingresses page, click **Create** in the upper-right corner of the page.

    6.  In the Create dialog box, set the service to the inference service that is created in step [3](#step_h71_3x3_zcf). For more information about the other parameters, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md). Click **Create**.

7.  Run the following command to query the Ingress:

    ```
    kubectl get ingress
    ```

    Expected output:

    ```
    NAME                CLASS    HOSTS                                                                             ADDRESS          PORTS   AGE
    fashion-mnist-eci   <none>   fashion-mnist-eci.c9522b942edaf4ec98c2cbc351ec8****.cn-beijing.alicontainer.com   101.200.XX.XX    80      19m
    ```

    You can visit the domain name of the Ingress to access the inference service.

8.  Test the inference service and then log on to AI Dashboard.

    Before you can log on to AI Dashboard, you must install and check whether you have access to AI Dashboard. For more information about how to install and access AI Dashboard, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Install Cloud-native AI Component Set and access AI Dashboard.md).

9.  In the left-side navigation pane of AI Dashboard, choose **Elastic Tasks** \> **Tasks** to view the details of the inference service.

    The following figure indicates that both standard and preemptible elastic container instances are created during scale-out.


