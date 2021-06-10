---
keyword: [elastic quota group, capacity scheduling]
---

# Capacity Scheduling

Kubernetes uses resource quotas to allocate resources based on fixed amounts. This method does not ensure high resource utilization in a Kubernetes cluster. To improve the resource utilization of a Kubernetes cluster, Alibaba Cloud has developed the capacity scheduling feature to optimize resource allocation. This feature is designed on top of the Yarn capacity scheduler and the Kubernetes scheduling framework. This feature allows you to meet the resource requests in a Kubernetes cluster and improve resource utilization by sharing idle resources. This topic describes how to use the capacity scheduling feature.

-   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The Kubernetes version of the created cluster is V1.20 or later.

If a Kubernetes cluster is used by multiple users, the cluster administrator allocates a fixed amount of resources to each user to meet the resource requirements. The traditional method is to use Kubernetes resource quotas to allocate resources. These users may use resources in different ways at different times. As a result, some users may encounter resource shortages, but the resources allocated to other users are not being used. In this case, the resource utilization of the cluster becomes lower and a considerable amount of resources are wasted.

## Core features

To address this issue, Alibaba Cloud provides the capacity scheduling feature based on the Kubernetes scheduling framework to optimize resource allocation. This feature allows you to meet the resource requests in a Kubernetes cluster and improve resource utilization by sharing idle resources. The capacity scheduling feature provides the following features:

-   Support hierarchical resource quotas. You can configure hierarchical resource quotas based on your requirements \(such as enterprise scenarios\), as shown in the following figure. In an elastic resource quota group, each namespace belongs to only one leaf and a leaf can contain multiple namespaces.

    ![37](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2042779161/p268242.png)

-   Support resource sharing and reclaiming between resource quotas.

    -   Min: the minimum amount of resources that are guaranteed for use. If the resources of a cluster become insufficient, the total amount of minimum resources for all users must be lower than the total amount of resources of the cluster.
    -   Max: The maximum amount of resources that you can use.

        ![39](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2003779161/p268433.png)

    **Note:**

    -   Idle resource quotas of other users can be temporarily used by your workloads. However, the total amount of resources used by your workloads cannot exceed the maximum amount of the corresponding resource quota.
    -   If the minimum amount of resources allocated to your workloads are idle, they can be temporarily used by other users. When you require these resources, they are reclaimed and preempted by your workloads.
-   Support multiple resource types. You can configure CPU and memory resource quotas. You can also configure resource quotas for extended resources that are supported by Kubernetes, such as GPUs.

## Examples of capacity scheduling

In this topic, an Elastic Compute Service \(ECS\) instance of the ecs.sn2.13xlarge type \(56 vCPUs and 224 GiB\) is used to show how to configure resource quotas.

1.  Run the following command to create namespaces:

    ```
    kubectl create ns namespace1
    kubectl create ns namespace2
    kubectl create ns namespace3
    kubectl create ns namespace4
    ```

2.  Create an elastic quota group by using the following YAML template:

    ```
    apiVersion: scheduling.sigs.k8s.io/v1beta1
    kind: ElasticQuotaTree
    metadata:
      name: elasticquotatree
      namespace: kube-system # The elastic quota group takes effect only if it is created in the kube-system namespace.
    spec:
      root:
        name: root # Configure the resource quota of the root. The maximum amount of resources for the root must equal the minimum amount of resources for the root.
        max:
          cpu: 40
          memory: 40Gi
          nvidia.com/gpu: 4
        min:
          cpu: 40
          memory: 40Gi
          nvidia.com/gpu: 4
        children: # Configure resource quotas for the leaves of the root.
          - name: root.a
            max:
              cpu: 40
              memory: 40Gi
              nvidia.com/gpu: 4
            min:
              cpu: 20
              memory: 20Gi
              nvidia.com/gpu: 2
            children: # Configure resource quotas of the farthest leaves.
              - name: root.a.1
                namespaces: # Configure resource quotas of the namespaces.
                  - namespace1
                max:
                  cpu: 20
                  memory: 20Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
              - name: root.a.2
                namespaces: # Configure resource quotas of the namespaces.
                  - namespace2
                max:
                  cpu: 20
                  memory: 40Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
          - name: root.b
            max:
              cpu: 40
              memory: 40Gi
              nvidia.com/gpu: 4
            min:
              cpu: 20
              memory: 20Gi
              nvidia.com/gpu: 2
            children: # Configure resource quotas of the farthest leaves.
              - name: root.b.1
                namespaces: # Configure resource quotas of the namespaces.
                  - namespace3
                max:
                  cpu: 20
                  memory: 20Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
              - name: root.b.2
                namespaces: # Configure resource quotas of the namespaces.
                  - namespace4
                max:
                  cpu: 20
                  memory: 20Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
    ```

    In the preceding YAML template, namespaces created from the node are specified in the `namespaces` fields. The resource quotas of the leaves under a parent leaf are specified in the `children` parameters. The quota configurations must meet the following requirements:

    -   The minimum amount of resources for a leaf cannot exceed the maximum amount of resources for the leaf.
    -   The total amount of minimum resources for all leaves under a parent leaf cannot exceed the minimum amount of resources for the parent leaf.
    -   The minimum amount of resources for the root must equal the maximum amount of resources for the root. This amount cannot exceed the total amount of resources of the cluster.
    -   Each namespace belongs to only one leaf. A leaf can contain multiple namespaces.
3.  Run the following command to check whether the elastic quota group is created:

    ```
    kubectl get ElasticQuotaTree -n kube-system
    ```

    Expected output:

    ```
    NAME               AGE
    elasticquotatree   68s
    ```

4.  Create a Deployment in `namespace1` by using the following YAML template. The Deployment runs five pods and each pod requests five CPU cores.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx1
      namespace: namespace1
      labels:
        app: nginx1
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx1
      template:
        metadata:
          name: nginx1
          labels:
            app: nginx1
        spec:
          containers:
          - name: nginx1
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

5.  Run the following command to query the states of these pods:

    ```
    kubectl get pods -n namespace1
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-52dbg   1/1     Running   0          70s
    nginx1-744b889544-6l4s9   1/1     Running   0          70s
    nginx1-744b889544-cgzlr   1/1     Running   0          70s
    nginx1-744b889544-w2gr7   1/1     Running   0          70s
    nginx1-744b889544-zr5xz   0/1     Pending   0          70s
    ```

    -   The total CPU resource requests of the pods in `namespace1` exceed `10 CPU cores`, which is the minimum number of CPU cores for `root.a.1`. The maximum number of CPU cores for the root is `40`. Therefore, these pods can use idle CPU cores in the cluster. The maximum number of CPU cores used by these pods cannot exceed `20`, which is the maximum number of CPU cores for `root.a.1`.
    -   If the number of CPU cores used by the pods in namespace1 reaches `20`, the next pod to be scheduled becomes pending. Among the five pods of this Deployment, four are in the running state and one is in the pending state.
6.  Create a second Deployment in `namespace2` by using the following YAML template. The Deployment runs five pods and each pod requests five CPU cores.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx2
      namespace: namespace2
      labels:
        app: nginx2
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx2
      template:
        metadata:
          name: nginx2
          labels:
            app: nginx2
        spec:
          containers:
          - name: nginx2
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

7.  Run the following command to query the states of these pods:

    ```
    kubectl get pods -n namespace1
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-52dbg   1/1     Running   0          111s
    nginx1-744b889544-6l4s9   1/1     Running   0          111s
    nginx1-744b889544-cgzlr   1/1     Running   0          111s
    nginx1-744b889544-w2gr7   1/1     Running   0          111s
    nginx1-744b889544-zr5xz   0/1     Pending   0          111s
    ```

    ```
    kubectl get pods -n namespace2
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx2-556f95449f-4gl8s   1/1     Running   0          111s
    nginx2-556f95449f-crwk4   1/1     Running   0          111s
    nginx2-556f95449f-gg6q2   0/1     Pending   0          111s
    nginx2-556f95449f-pnz5k   1/1     Running   0          111s
    nginx2-556f95449f-vjpmq   1/1     Running   0          111s
    ```

    -   This Deployment is similar to the `nginx1` Deployment. The total CPU resource requests of the pods in `namespace2` exceed `10 CPU cores`, which is the minimum number of CPU cores for `root.a.2`. The maximum number of CPU cores for the root is `40`. Therefore, these pods can use idle CPU cores in the cluster. The maximum number of CPU cores used by these pods cannot exceed `20`, which is the maximum number of CPU cores for `root.a.2`.
    -   If the number of CPU cores used by the pods in namespace2 reaches `20`, the next pod to be scheduled becomes pending. Among the five pods of this Deployment, four are in the running state and one is in the pending state.
    -   After you create the preceding two Deployments, the pods in `namespace1` and `namespace2` have used `40` CPU cores, which is the maximum number of CPU cores for the `root`.
8.  Create a third Deployment in `namespace3` by using the following YAML template. This Deployment runs five pods and each pod requests five CPU cores.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx3
      namespace: namespace3
      labels:
        app: nginx3
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx3
      template:
        metadata:
          name: nginx3
          labels:
            app: nginx3
        spec:
          containers:
          - name: nginx3
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

9.  Run the following command to query the states of these pods:

    ```
    kubectl get pods -n namespace1
    ```

    Expected output:

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-52dbg   1/1     Running   0          6m17s
    nginx1-744b889544-cgzlr   1/1     Running   0          6m17s
    nginx1-744b889544-nknns   0/1     Pending   0          3m45s
    nginx1-744b889544-w2gr7   1/1     Running   0          6m17s
    nginx1-744b889544-zr5xz   0/1     Pending   0          6m17s
    ```

    ```
    kubectl get pods -n namespace2
    ```

    Expected output:

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx2-556f95449f-crwk4   1/1     Running   0          4m22s
    nginx2-556f95449f-ft42z   1/1     Running   0          4m22s
    nginx2-556f95449f-gg6q2   0/1     Pending   0          4m22s
    nginx2-556f95449f-hfr2g   1/1     Running   0          3m29s
    nginx2-556f95449f-pvgrl   0/1     Pending   0          3m29s
    ```

    ```
    kubectl get pods -n namespace3
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx3-578877666-msd7f   1/1     Running   0          4m
    nginx3-578877666-nfdwv   0/1     Pending   0          4m10s
    nginx3-578877666-psszr   0/1     Pending   0          4m11s
    nginx3-578877666-xfsss   1/1     Running   0          4m22s
    nginx3-578877666-xpl2p   0/1     Pending   0          4m10s
    ```

    The `minimum number of CPU cores` for `root.b.1` is `10`. Therefore, when `nginx3` is created, the pods require the `minimum resources`. The scheduler reclaims the CPU cores that belong to `root.b` and are temporarily used by `root.a`. This ensures `a minimum of 10 CPU cores` for the pod scheduling of `nginx3`.

    Before the scheduler reclaims the temporarily used 10 CPU cores, it also considers other factors, such as the priority classes, availability, and creation time of the workloads of `root.a`. Therefore, after the pods of `nginx3` are scheduled based on the reclaimed `10` CPU cores, two pods are in the running state and the other three are in the pending state.

10. Create a fourth Deployment `nginx4` in `namespace4` by using the following YAML template. This Deployment runs five pods and each pod requests five CPU cores.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx4
      namespace: namespace4
      labels:
        app: nginx4
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx4
      template:
        metadata:
          name: nginx4
          labels:
            app: nginx4
        spec:
          containers:
          - name: nginx4
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

11. Run the following command to query the states of these pods:

    ```
    kubectl get pods -n namespace1
    ```

    Expected output:

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-cgzlr   1/1     Running   0          8m20s
    nginx1-744b889544-cwx8l   0/1     Pending   0          55s
    nginx1-744b889544-gjkx2   0/1     Pending   0          55s
    nginx1-744b889544-nknns   0/1     Pending   0          5m48s
    nginx1-744b889544-zr5xz   1/1     Running   0          8m20s
    ```

    ```
    kubectl get pods -n namespace2
    ```

    Expected output:

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx2-556f95449f-cglpv   0/1     Pending   0          3m45s
    nginx2-556f95449f-crwk4   1/1     Running   0          9m31s
    nginx2-556f95449f-gg6q2   1/1     Running   0          9m31s
    nginx2-556f95449f-pvgrl   0/1     Pending   0          8m38s
    nginx2-556f95449f-zv8wn   0/1     Pending   0          3m45s
    ```

    ```
    kubectl get pods -n namespace3
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx3-578877666-msd7f   1/1     Running   0          8m46s
    nginx3-578877666-nfdwv   0/1     Pending   0          8m56s
    nginx3-578877666-psszr   0/1     Pending   0          8m57s
    nginx3-578877666-xfsss   1/1     Running   0          9m8s
    nginx3-578877666-xpl2p   0/1     Pending   0          8m56s
    ```

    ```
    kubectl get pods -n namespace4
    ```

    Expected output:

    ```
    nginx4-754b767f45-g9954   1/1     Running   0          4m32s
    nginx4-754b767f45-j4v7v   0/1     Pending   0          4m32s
    nginx4-754b767f45-jk2t7   0/1     Pending   0          4m32s
    nginx4-754b767f45-nhzpf   0/1     Pending   0          4m32s
    nginx4-754b767f45-tv5jj   1/1     Running   0          4m32s
    ```

    Similarly, the `minimum number of CPU cores` for `root.b.2` is `10`. Therefore, when `nginx4` is created, the pods require the `minimum resources`. The scheduler reclaims the CPU cores that belong to `root.b` and are temporarily used by `root.a`. This ensures `a minimum of 10 CPU cores` for the pod scheduling of `nginx4`.

    Before the scheduler reclaims the temporarily used 10 CPU cores, it also considers other factors, such as the priority classes, availability, and creation time of the workloads in `root.a`. Therefore, after the pods of `nginx4` are scheduled based on the reclaimed `10` CPU cores, two pods are in the running state and the other three are in the pending state.

    After all the four Deployments are created, all pods in each namespace are running with the `minimum amount of resources` that are guaranteed for resource quotas.


