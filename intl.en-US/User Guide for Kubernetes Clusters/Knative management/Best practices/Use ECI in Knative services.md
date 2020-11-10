---
keyword: [Knative, ECI, use resources based on requirements]
---

# Use ECI in Knative services

You can create elastic container instances \(ECIs\) for a Knative Service. Then, you can use Knative and Virtual Kubelet to create pods on ECIs based on your requirements. This topic describes how to create an ECI for a Knative Service in the following methods.

-   [Deploy Knative components in a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative components in a Kubernetes cluster.md)
-   [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Method 1: Directly create an ECI for a Knative Service

-   If the virtual-node-affinity-injection=enabled label is not added to a namespace, you can create an ECI by setting the following configurations when you create resource objects for a Knative Service.

    -   Specify the ECI specification in an annotation.
    -   Specify virtual-kubelet in the nodeAffinity parameter.
    -   Specify virtual-kubelet.io/provider in the tolerations parameter.
    Set the parameters based on the following template:

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go
      namespace: vk
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-use-specs: "2-4Gi"
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: type
                    operator: In
                    values: 
                    - virtual-kubelet   
          tolerations:
          - key: virtual-kubelet.io/provider
            operator: Exists                
          containers:
          - env:
            - name: TARGET
              value: "Knative"
            image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:73fbdd56
    ```

-   If the virtual-node-affinity-injection=enabled label is added to the namespace, you only need to specify the ECI specification in the annotation to directly create an ECI.

    ```
    kubectl label namespace vk virtual-node-affinity-injection=enabled
    ```

    Set the parameters in the following template:

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go
      namespace: vk
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-use-specs: "2-4Gi"
        spec:         
          containers:
          - env:
            - name: TARGET
              value: "Knative"
            image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:73fbdd56
    ```


## Method 2: Specify an ECS instance to automatically create an ECI for a Knative Service

You can specify an Elastic Compute Service \(ECS\) instance with specified labels in the nodeAffinity parameter to automatically create an ECI for a Knative Service. This way, the pod is deployed in both the ECS instance and ECI.

1.  Add a label to the ECS instance. For more information, see [Manage node labels](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Manage node labels.md).

    The resource-role:ecs label is added to the ECS instance in this example.

2.  In the resource object template, set the nodeAffinity parameter.

    Specify the label that is added to the ECS instance and virtual-kubelet in the nodeAffinity parameter.

    Set the parameters in the following template:

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: helloworld-go
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-use-specs: "2-4Gi"
       spec:
          containerConcurrency: 10
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: resource-role
                    operator: In
                    values:
                    - ecs
                - matchExpressions:
                  - key: type
                    operator: In
                    values: 
                    - virtual-kubelet
          tolerations:
          - key: virtual-kubelet.io/provider
            operator: Exists                 
          containers:
          - env:
            - name: TARGET
              value: "Knative"
            image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:73fbdd56
    ```


Run the following command to query the result. You can find that two pods are created on the ECI.

```
kubectl -n vk get pod -o wide
```

```
NAME                                              READY   STATUS    RESTARTS   AGE   IP             NODE                 NOMINATED NODE   READINESS GATES
helloworld-go-dqqhv-deployment-6d54c9c8dc-hkjwn   2/2     Running   0          40s   192.1xx.x.xx   virtual-node-eci-0   <none>           <none>
```

