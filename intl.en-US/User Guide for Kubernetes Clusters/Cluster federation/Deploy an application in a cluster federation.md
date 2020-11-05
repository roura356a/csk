# Deploy an application in a cluster federation

This topic describes how to deploy an application in a cluster federation.

-   Two Container Service for Kubernetes \(ACK\) clusters are created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   You are connected to the ACK clusters by using [Cloud Shell](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).
-   A cluster federation is deployed. For more information, see [Set up KubeFed for ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster federation/Set up KubeFed for ACK clusters.md).

1.  Create a YAML file named federated-namespace.yaml and copy the following content to the file:

    ```
    apiVersion: v1
    kind: Namespace
    metadata:
      name: test-namespace
    ---
    apiVersion: types.kubefed.io/v1beta1
    kind: FederatedNamespace
    metadata:
      name: test-namespace
      namespace: test-namespace
    spec:
      placement:
        clusters:
            - name: cluster1
            - name: cluster2
    ```

2.  Run the `kubectl apply -f federated-namespace.yaml` command in [Cloud Shell](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md) to create FederatedNamespace in the primary cluster Cluster 1. For more information about how to create the primary cluster Cluster 1, see [Set up KubeFed for ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster federation/Set up KubeFed for ACK clusters.md).

3.  Create a YAML file named federated-deployment.yaml and copy the following content to the file:

    ```
    apiVersion: types.kubefed.io/v1beta1
    kind: FederatedDeployment
    metadata:
      name: test-deployment
      namespace: test-namespace
    spec:
      template:
        metadata:
          labels:
            app: nginx
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              - image: nginx
                name: nginx
                resources:
                  limits:
                    cpu: 500m
                  requests:
                    cpu: 200m
      placement:
        clusters:
            - name: cluster1
            - name: cluster2
    ```

4.  Run the `kubectl apply -f federated-deployment.yaml` command in [Cloud Shell](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md) to deploy FederatedDeployment in the primary cluster Cluster 1.


Check details about each deployment in the cluster federation.

Run the following command to check details about the deployment in Cluster 1:

```
kubectl get deployment -n test-namespace --context cluster1
```

The following response is returned:

```
NAME              DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
test-deployment   2         2         2            2           71s
```

Run the following command to check details about the deployment in Cluster 2:

```
kubectl get deployment -n test-namespace --context cluster2
```

The following response is returned:

```
NAME              DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
test-deployment   2         2         2            2           77s
```

