---
keyword: [Knative, elastic container instance, use resources based on requirements]
---

# Use elastic container instances in Knative

You can create elastic container instances for a Knative Service. Then, you can use Knative and Virtual Kubelet to create pods on elastic container instances based on your requirements. This topic describes how to create an elastic container instance for a Knative Service by using the following methods.

-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md)
-   [Step 1: Deploy ack-virtual-node in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Method 1: Directly create an elastic container instance for a Knative Service

-   If the virtual-node-affinity-injection=enabled label is not added to the namespace, you can create an elastic container instance by adding the following configurations when you create resource objects for a Knative Service.

    -   Specify the specification of the elastic container instance in the annotation parameter.
    -   Specify virtual-kubelet in the nodeAffinity parameter.
    -   Specify virtual-kubelet.io/provider in the tolerations parameter.
    Sample template:

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

-   If the virtual-node-affinity-injection=enabled label is added to the namespace, you need only to specify the specification of the elastic container instance in the annotation field to create an elastic container instance.

    ```
    kubectl label namespace vk virtual-node-affinity-injection=enabled
    ```

    Sample template:

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


## Method 2: Automatically create an elastic container instance for a Knative Service by specifying an ECS instance

You can automatically create an elastic container instance for a Knative Service by specifying an Elastic Compute Service \(ECS\) instance with specified labels in the nodeAffinity parameter. This way, the pod is deployed in both the ECS instance and the elastic container instance.

1.  Add a label to the ECS instance. For more information, see [Manage node labels](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Manage node labels.md).

    The resource-role:ecs label is added to the ECS instance in this example.

2.  In the resource object template, set the nodeAffinity parameter.

    Specify the label that is added to the ECS instance and virtual-kubelet in the nodeAffinity parameter.

    Sample template:

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


Run the following command to verify the result. You can view that two elastic container instance-based pods are created.

```
kubectl -n vk get pod -o wide
```

Expected output:

```
NAME                                              READY   STATUS    RESTARTS   AGE   IP             NODE                 NOMINATED NODE   READINESS GATES
helloworld-go-dqqhv-deployment-6d54c9c8dc-hkjwn   2/2     Running   0          40s   192.1xx.x.xx   virtual-node-eci-0   <none>           <none>
```

