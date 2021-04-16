---
keyword: ECI elastic scheduling
---

# Use ECI elastic scheduling

Elastic Container Instance \(ECI\) elastic scheduling is an elastic scheduling strategy provided by Alibaba Cloud. You can add annotations to specify the resources that you want to use when you deploy applications. You can use only Elastic Compute Service \(ECS\) instances or elastic container instances, or automatically request elastic container instances when ECS resources are insufficient. ECI elastic scheduling can meet your resource requirements in different workload scenarios.

-   A professional Kubernetes cluster is created and the Kubernetes version is 1.18 or later. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The **ack-virtual-node** component is installed in the cluster. For more information, see [Use Elastic Container Instance in ACK clusters]().

## Procedure

**Note:** You can add annotations to specify the type of resource that you want to use. The `alibabacloud.com/burst-resource` annotation can be set to one of the following values:

-   If the value is left empty, only existing ECS resources in the cluster are used. This is the default value.
-   **eci**: Elastic container instances are used when the ECS resources in the cluster are insufficient.
-   **eci\_only**: Only elastic container instances are used. The ECS resources in the cluster are not used.

1.  Create the nginx-deployment.yaml file and copy the following content to the file:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      replicas: 4
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          name: nginx
          annotations:
            alibabacloud.com/burst-resource: eci # This annotation specifies the type of resource that you want to use.
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            resources:
              limits:
                cpu: 2
              requests:
                cpu: 2
    ```

2.  Run the following command to create pods that use elastic container instances:

    ```
    kubectl apply -f nginx-deployment.yaml
    ```


