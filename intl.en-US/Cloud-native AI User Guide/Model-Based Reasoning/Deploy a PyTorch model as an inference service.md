---
keyword: [PyTorch model, inference service]
---

# Deploy a PyTorch model as an inference service

This topic describes how to use Arena to deploy a PyTorch model as an inference service.

-   [A Container Service for Kubernetes \(ACK\) cluster that contains GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use GPU scheduling for ACK clusters.md).
-   [Nodes in the cluster can access the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Access the Kubernetes API server over the Internet.md).
-   [Install Arena]().
-   A standalone PyTorch training job is completed. The PyTorch model is converted to TorchScript. For more information, see [Use Arena to submit standalone TensorFlow training jobs](/intl.en-US/Cloud-native AI User Guide/Model training/Use Arena to submit standalone TensorFlow training jobs.md).

    **Note:** In this topic, a BERT model trained with PyTorch 1.16 is used. The model is converted to TorchScript and saved in the triton directory of a persistent volume claim \(PVC\). The model is then deployed by using `NVIDIA Triton Server`.

    The following model directory structure is required by Triton:

    ```
    └── chnsenticorp # The name of the model. 
        ├── 1623831335 # The version of the model. 
        │   └── model.savedmodel # The model file. 
        │       ├── saved_model.pb
        │       └── variables
        │           ├── variables.data-00000-of-00001
        │           └── variables.index
        └── config.pbtxt # The configuration of Triton. 
    ```


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
    arena serve triton \
     --name=bert-triton \
     --namespace=inference \
     --gpus=1 \
     --replicas=1 \
     --image=nvcr.io/nvidia/tritonserver:20.12-py3 \
     --data=model-pvc:/models \
     --model-repository=/models/triton
    ```

    Expected output:

    ```
    configmap/bert-triton-202106251740-triton-serving created
    configmap/bert-triton-202106251740-triton-serving labeled
    service/bert-triton-202106251740-tritoninferenceserver created
    deployment.apps/bert-triton-202106251740-tritoninferenceserver created
    INFO[0001] The Job bert-triton has been submitted successfully
    INFO[0001] You can run `arena get bert-triton --type triton-serving` to check the job status
    ```

5.  Run the following command to query the deployment result in `TensorFlow Serving`:

    ```
    arena serve list -n inference
    ```

    Expected output:

    ```
    NAME            TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS        PORTS
    bert-triton     Triton      202106251740  1        1          172.16.70.14   RESTFUL:8000,GRPC:8001
    ```

6.  Run the following command to query the details about the inference service:

    ```
    arena serve get bert-tfserving -n inference
    ```

    Expected output:

    ```
    Name:       bert-triton
    Namespace:  inference
    Type:       Triton
    Version:    202106251740
    Desired:    1
    Available:  1
    Age:        5m
    Address:    172.16.70.14
    Port:       RESTFUL:8000,GRPC:8001
    
    
    Instances:
      NAME                                                             STATUS   AGE  READY  RESTARTS  NODE
      ----                                                             ------   ---  -----  --------  ----
      bert-triton-202106251740-tritoninferenceserver-667cf4c74c-s6nst  Running  5m   1/1    0         cn-beijing.192.168.0.89
    ```

    The preceding output shows that the model is successfully deployed by using `TensorFlow Serving`. Port 8001 is exposed for gRPC and port 8000 is exposed for the REST API.

7.  Configure an Internet-facing Ingress. For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Create an Ingress.md).

    **Note:** By default, the inference service that is deployed by running the `arena serve tensorflow` command is assigned only a cluster IP address. Therefore, the service cannot be accessed over the Internet. You must create an Ingress for the inference service based on the following parameter settings:

    -   Set **Namespace** to **inference**.
    -   Set Service **Port** to 8501. This port is exposed for the REST API.
8.  After you create the Ingress, go to the **Ingresses** page and find the Ingress. The value in the **Rules** column contains the endpoint of the Ingress.

    ![23](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2418687261/p295171.png)

9.  Run the following command to call the inference service by using the Ingress endpoint. `NVIDIA Triton Server` follows the interface specifications of KFServing. For more information, see [NVIDIA Triton Server API](https://github.com/triton-inference-server/server/blob/main/docs/inference_protocols.md).

    ```
    curl "http://<Ingress endpoint>"
    ```

    Expected output:

    ```
    {
        "name":"chnsenticorp",
        "versions":[
            "1623831335"
        ],
        "platform":"tensorflow_savedmodel",
        "inputs":[
            {
                "name":"input_ids",
                "datatype":"INT64",
                "shape":[
                    -1,
                    128
                ]
            }
        ],
        "outputs":[
            {
                "name":"probabilities",
                "datatype":"FP32",
                "shape":[
                    -1,
                    2
                ]
            }
        ]
    }
    ```

    The preceding output shows that the inference service is available. This indicates that the inference service is successfully deployed.


