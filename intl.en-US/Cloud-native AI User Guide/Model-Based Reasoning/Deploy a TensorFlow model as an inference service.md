---
keyword: [TensorFlow model, inference service]
---

# Deploy a TensorFlow model as an inference service

This topic describes how to use Arena to deploy a TensorFlow model as an inference service.

-   [A Container Service for Kubernetes \(ACK\) cluster that contains GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use GPU scheduling for ACK clusters.md).
-   [Nodes in the cluster can access the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Access the Kubernetes API server over the Internet.md).
-   [Install Arena]().
-   A standalone TensorFlow training job is completed. For more information, see [Use Arena to submit standalone TensorFlow training jobs](/intl.en-US/Cloud-native AI User Guide/Model training/Use Arena to submit standalone TensorFlow training jobs.md).

    **Note:** In this topic, a BERT model trained with TensorFlow 1.15 is used to deploy an inference service. The model is exported as a saved model.


## Procedure

1.  Run the following command to query the GPU resources available in the cluster:

    ```
    arena top node
    ```

    Expected output:

    ```
    NAME                      IPADDRESS      ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-beijing.192.168.0.100  192.168.0.100  <none>  Ready   1           0
    cn-beijing.192.168.0.101  192.168.0.101  <none>  Ready   1           0
    cn-beijing.192.168.0.99   192.168.0.99   <none>  Ready   1           0
    ---------------------------------------------------------------------------------------------------
    Allocated/Total GPUs of nodes which own resource nvidia.com/gpu In Cluster:
    0/3 (0.0%)
    ```

    The preceding output shows that the cluster has three GPU-accelerated nodes on which you can deploy the model.

2.  Upload the model file to your Object Storage Service \(OSS\) bucket. For more information, see [Upload objects](/intl.en-US/Quick Start/OSS console/Upload objects.md).

3.  Use the following YAML file to create a persistent volume \(PV\) and a persistent volume claim \(PVC\):

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
        volumeHandle: model-csi-pv   // The value must be the same as the name of the PV. 
        volumeAttributes:
          bucket: "Your Bucket"
          url: "Your oss url"
          akId: "Your Access Key Id"
          akSecret: "Your Access Key Secret"
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

4.  Run the following command to deploy the model by using `TensorFlow Serving`:

    ```
    arena serve tensorflow \
      --name=bert-tfserving \
      --model-name=chnsenticorp  \
      --gpus=1  \
      --image=tensorflow/serving:1.15.0-gpu \
      --data=model-pvc:/models \
      --model-path=/models/tensorflow \
      --version-policy=specific:1623831335
    ```

    Expected output:

    ```
    configmap/bert-tfserving-202106251556-tf-serving created
    configmap/bert-tfserving-202106251556-tf-serving labeled
    configmap/bert-tfserving-202106251556-tensorflow-serving-cm created
    service/bert-tfserving-202106251556-tensorflow-serving created
    deployment.apps/bert-tfserving-202106251556-tensorflow-serving created
    INFO[0003] The Job bert-tfserving has been submitted successfully
    INFO[0003] You can run `arena get bert-tfserving --type tf-serving` to check the job status
    ```

5.  Run the following command to query the deployment result in `TensorFlow Serving`:

    ```
    arena serve list
    ```

    Expected output:

    ```
    NAME            TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS        PORTS
    bert-tfserving  Tensorflow  202106251556  1        1          172.16.95.171  GRPC:8500,RESTFUL:8501
    ```

6.  Run the following command to query the details about the inference service:

    ```
    arena serve get bert-tfserving
    ```

    Expected output:

    ```
    Name:       bert-tfserving
    Namespace:  inference
    Type:       Tensorflow
    Version:    202106251556
    Desired:    1
    Available:  1
    Age:        4m
    Address:    172.16.95.171
    Port:       GRPC:8500,RESTFUL:8501
    
    
    Instances:
      NAME                                                             STATUS   AGE  READY  RESTARTS  NODE
      ----                                                             ------   ---  -----  --------  ----
      bert-tfserving-202106251556-tensorflow-serving-8554d58d67-jd2z9  Running  4m   1/1    0         cn-beijing.192.168.0.88
    ```

    The preceding output shows that the model is successfully deployed by using `TensorFlow Serving`. Port 8500 is exposed for gRPC and port 8501 is exposed for HTTP.

7.  Configure an Internet-facing Ingress. For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Create an Ingress.md).

    **Note:** By default, the inference service that is deployed by running the `arena serve tensorflow` command is assigned only a cluster IP address. Therefore, the service cannot be accessed over the Internet. You must create an Ingress for the inference service based on the following parameter settings:

    -   Set **Namespace** to **inference**.
    -   Set Service **Port** to 8501. This port is exposed for the REST API.
8.  After you create the Ingress, go to the **Ingresses** page and find the Ingress. The value in the **Rules** column contains the endpoint of the Ingress.

    ![12](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1208687261/p295140.png)

9.  Run the following command to call the inference service through the Ingress endpoint. For more information about `TensorFlow Serving`, see [TensorFlow Serving API](https://github.com/tensorflow/serving/blob/master/tensorflow_serving/g3doc/api_rest.md).

    ```
    curl "http://<Ingress endpoint>"
    ```

    Expected output:

    ```
    {
     "model_version_status": [
      {
       "version": "1623831335",
       "state": "AVAILABLE",
       "status": {
        "error_code": "OK",
        "error_message": ""
       }
      }
     ]
    }
    ```

    The preceding output shows that the inference service is available. This indicates that the inference service is successfully deployed.


