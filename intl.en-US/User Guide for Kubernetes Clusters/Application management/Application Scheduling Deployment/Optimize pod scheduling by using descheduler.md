---
keyword: [descheduler, optimize pod scheduling]
---

# Optimize pod scheduling by using descheduler

The descheduler component is used to schedule pods that cannot find suitable nodes. This optimizes pod scheduling, avoids resource waste, and improves resource utilization in Container Service for Kubernetes \(ACK\) clusters. This topic describes how to configure and use descheduler.

## Prerequisites

-   An ACK cluster of Kubernetes 1.14 or later is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Helm 3 is installed. For more information, see [Install Helm](https://github.com/helm/helm).
-   [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) is installed.

## Procedure

1.  Install descheduler by using Helm.

    1.  Run the following command to add the chart repository of descheduler:

        ```
        helm repo add descheduler https://kubernetes-sigs.github.io/descheduler/
        ```

        Expected output:

        ```
        "descheduler" has been added to your repositories
        ```

    2.  Run the following command to install descheduler:

        ```
        helm install descheduler --namespace kube-system descheduler/descheduler
        ```

        Expected output:

        ```
        NAME: descheduler
        LAST DEPLOYED: Thu Mar  4 19:29:23 2021
        NAMESPACE: kube-system
        STATUS: deployed
        REVISION: 1
        TEST SUITE: None
        NOTES:
        Descheduler installed as a cron job.
        ```

        **Note:** You may experience timeout errors when you pull the default image of descheduler due to network issues. In this case, we recommend that you replace the default image with the image provided by Alibaba Cloud: `registry.cn-hangzhou.aliyuncs.com/carsnow/descheduler:v0.20.0` .

2.  Check the installation progress.

    After the descheduler chart is installed, a CronJob is automatically created in the `kube-system` `namespace`. By default, this CronJob is configured to run every 2 minutes.

    ```
    kubectl get cronjob -n kube-system
    ```

    Expected output:

    ```
    NAME          SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
    descheduler   */2 * * * *   False     0        21s             47h
    ```

3.  Check the scheduling policy of descheduler.

    ```
    kubectl describe cm descheduler -n kube-system
    ```

    Expected output:

    ```
    Name:         descheduler
    Namespace:    kube-system
    Labels:       app.kubernetes.io/instance=descheduler
                  app.kubernetes.io/managed-by=Helm
                  app.kubernetes.io/name=descheduler
                  app.kubernetes.io/version=0.20.0
                  helm.sh/chart=descheduler-0.20.0
    Annotations:  meta.helm.sh/release-name: descheduler
                  meta.helm.sh/release-namespace: kube-system
    Data
    ====
    policy.yaml:
    ----
    apiVersion: "descheduler/v1alpha1"
    kind: "DeschedulerPolicy"
    strategies:
      "RemoveDuplicates":
         enabled: true
      "RemovePodsViolatingInterPodAntiAffinity":
         enabled: true
      "LowNodeUtilization":
         enabled: true
         params:
           nodeResourceUtilizationThresholds:
             thresholds:
               "cpu" : 20
               "memory": 20
               "pods": 20
             targetThresholds:
               "cpu" : 50
               "memory": 50
               "pods": 50
      "RemovePodsHavingTooManyRestarts":
         enabled: true
         params:
           podsHavingTooManyRestarts:
             podRestartThreshold: 100
             includingInitContainers: true
    Events:  <none>
    ```

    For more information about the policy settings in the `strategies` section, see [Descheduler](https://github.com/kubernetes-sigs/descheduler).

4.  Verify pod scheduling before the scheduling policy is modified.

    1.  Create a Deployment to test the scheduling.

        Create a file named nginx.yaml and copy the following content into the file:

        ```
        apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
        kind: Deployment
        metadata:
          name: nginx-deployment-basic
          labels:
            app: nginx
        spec:
          replicas: 3
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
                ports:
                - containerPort: 80
        ```

        Run the following command to deploy a Deployment with the nginx.yaml file:

        ```
        kubectl apply -f nginx.yaml
        ```

        Expected output:

        ```
        deployment.apps/nginx-deployment-basic created
        ```

    2.  Wait 2 minutes and run the following command to check the nodes to which the pods are scheduled:

        ```
        kubectl get pod -o wide | grep nginx
        ```

        Expected output:

        ```
        NAME                          READY   STATUS     RESTARTS   AGE    IP               NODE                         NOMINATED NODE   READINESS GATES
        nginx-deployment-basic-**1    1/1     Running    0          36s    172.25.XXX.XX1   cn-hangzhou.172.16.XXX.XX2   <none>           <none>
        nginx-deployment-basic-**2    1/1     Running    0          11s    172.25.XXX.XX2   cn-hangzhou.172.16.XXX.XX3   <none>           <none>
        nginx-deployment-basic-**3    1/1     Running    0          36s    172.25.XXX.XX3   cn-hangzhou.172.16.XXX.XX3   <none>           <none>
        ```

        The output shows that pod `nginx-deployment-basic-**2` and pod `nginx-deployment-basic-**3` are scheduled to the same node `cn-hangzhou.172.16.XXX.XX3`.

5.  Modify the scheduling policy.

    To avoid pod scheduling being affected by multiple scheduling strategies, modify the ConfigMap in [Step 3](#step_aly_k85_ulh) to retain only the RemoveDuplicates strategy.

    **Note:** The RemoveDuplicates strategy ensures that pods managed by replication controllers are scheduled to different nods.

6.  Verify pod scheduling after the scheduling policy is modified.

    1.  Deploy a new scheduling policy.

        Create a file named newPolicy.yaml and copy the following content into the file:

        ```
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: descheduler
          namespace: kube-system
          labels:
            app.kubernetes.io/instance: descheduler
            app.kubernetes.io/managed-by: Helm
            app.kubernetes.io/name: descheduler
            app.kubernetes.io/version: 0.20.0
            helm.sh/chart: descheduler-0.20.0  
          annotations:
            meta.helm.sh/release-name: descheduler
            meta.helm.sh/release-namespace: kube-system
        data: 
          policy.yaml: |-
            apiVersion: "descheduler/v1alpha1"
            kind: "DeschedulerPolicy"
            strategies:
              "RemoveDuplicates":
                 enabled: true
        ```

        Run the following command to apply the configurations in newPolicy.yaml:

        ```
        kubectl apply -f newPolicy.yaml
        ```

        Expected output:

        ```
        configmap/descheduler created
        ```

    2.  Wait 2 minutes and run the following command to check the nodes to which the pods are scheduled:

        ```
        kubectl get pod -o wide | grep nginx
        ```

        Expected output:

        ```
        NAME                          READY   STATUS     RESTARTS   AGE      IP               NODE                         NOMINATED NODE   READINESS GATES
        nginx-deployment-basic-**1    1/1     Running    0          8m26s    172.25.XXX.XX1   cn-hangzhou.172.16.XXX.XX2   <none>           <none>
        nginx-deployment-basic-**2    1/1     Running    0          8m1s     172.25.XXX.XX2   cn-hangzhou.172.16.XXX.XX1   <none>           <none>
        nginx-deployment-basic-**3    1/1     Running    0          8m26s    172.25.XXX.XX3   cn-hangzhou.172.16.XXX.XX3   <none>           <none>
        ```

        The output shows that pod `nginx-deployment-basic-**2` is rescheduled to `cn-hangzhou.172.16.XXX.XX1` by descheduler. In this case, each of the three test pods is scheduled to a different node. This balances pod scheduling among multiple nodes.


