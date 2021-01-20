# Use a StatefulSet service

This topic describes the common scenarios of StatefulSet services and how to use StatefulSet services.

## Background information

Scenarios of StatefulSet services:

-   A stable deployment order: Pods are deployed in sequence from 0 to N-1. Before a pod is deployed, all its predecessors must be in the Running or Ready state.
-   A stable scale-in order: Pods are scaled in sequence from N-1 to 0. Before a pod is deleted, all its predecessors must be deleted.
-   Stable and unique network identifiers: After a pod is rescheduled to another node, its PodName and HostName values remain unchanged.
-   Stable and persistent storage based on persistent volume claims \(PVCs\): After a pod is rescheduled, the pod can access the same persistent data.

Use of StatefulSet services:

StatefulSet services can be used to automatically create PVCs and PVs based on the `volumeClaimTemplates` field.

This topic describes how to manage a StatefulSet service. Perform the following steps:

-   Create a StatefulSet service
-   Scale the StatefulSet service
-   Delete the StatefulSet service
-   Manage persistent storage of the StatefulSet service

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Create a StatefulSet service

**Note:** `volumeClaimTemplates`: a type of PVC template. If you set this field, the system creates PVCs based on the number of replicas that are specified for the StatefulSet service. The configurations of these PVCs are the same except for the PVC names.

1.  Create a statefulset.yaml file.

    **Note:** The `storageClassName` field is set to alicloud-disk-ssd to specify an Alibaba Cloud standard SSD.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      replicas: 2
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
              name: web
            volumeMounts:
            - name: disk-ssd
              mountPath: /data
      volumeClaimTemplates:
      - metadata:
          name: disk-ssd
        spec:
          accessModes: [ "ReadWriteOnce" ]
          storageClassName: "alicloud-disk-ssd"
          resources:
            requests:
              storage: 20Gi
    ```

2.  Run the following command to deploy a StatefulSet service:

    ```
    kubectl create -f statefulset.yaml
    ```

3.  Open another command-line interface \(CLI\) of kubectl and run the following command to check the pods that are deployed in sequence:

    ```
    kubectl get pod -w -l app=nginx
    ```

    ```
    NAME         READY   STATUS    RESTARTS   AGE
    web-0        0/1     Pending   0          0s
    web-0        0/1     Pending   0          0s
    web-0        0/1     ContainerCreating    0          0s
    web-0        1/1     Running   0          20s
    web-1        0/1     Pending   0          0s
    web-1        0/1     Pending   0          0s
    web-1        0/1     ContainerCreating    0          0s
    web-1        1/1     Running   0          7s
    ```

4.  Run the following command to query the deployed pods:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          6m
    web-1                         1/1     Running   0          6m
    ```

5.  Run the following command to view the PVCs:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   7m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   6m
    ```


## Scale the StatefulSet service

Scale out the StatefulSet service

1.  Run the following command to scale out the StatefulSet service to three pods:

    ```
    kubectl scale sts web --replicas=3
    ```

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the scale-out process:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          34m
    web-1                         1/1     Running   0          33m
    web-2                         1/1     Running   0          26m
    ```

3.  Run the following command to view the PVCs after the scale-out process:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   35m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   34m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   27m
    ```


## Scale in the StatefulSet service

1.  Run the following command to scale in the StatefulSet service to two pods:

    ```
    kubectl scale sts web --replicas=2
    ```

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the scale-in process:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          38m
    web-1                         1/1     Running   0          38m
    ```

    The number of existing pods is 2.

3.  Run the following command to view the PVCs after the scale-in process:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   31m
    ```

    PVCs and PVs are not decreased during the scale-in process.


## Scale out the StatefulSet service again

1.  Run the following command to scale out the StatefulSet service to three pods:

    ```
    kubectl scale sts web --replicas=3
    ```

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the scale-out process:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          1h
    web-1                         1/1     Running   0          1h
    web-2                         1/1     Running   0          8s
    ```

3.  Run the following command to view the PVCs after the scale-out process:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    The newly created pods use the existing PVCs and PVs after the scale-out process is complete.


## Delete the StatefulSet service

1.  Run the following command to view the PVC that is used by the pod named web-1:

    ```
    kubectl describe pod web-1 | grep ClaimName
    ```

    ```
    ClaimName:  disk-ssd-web-1
    ```

2.  Run the following command to delete the pod named web-1:

    ```
    kubectl delete pod web-1
    ```

    ```
    pod "web-1" deleted
    ```

3.  Run the following command to view the running pods after the pod is deleted:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          1h
    web-1                         1/1     Running   0          25s
    web-2                         1/1     Running   0          9m
    ```

    The recreated pod has the same name as the deleted pod.

4.  Run the following command to view the PVCs:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    The recreated pod uses the same PVC as the deleted pod.

5.  Open another CLI of kubectl and run the following command to view the processes in which the pod is deleted and recreated.

    ```
    kubectl get pod -w -l app=nginx
    ```

    ```
    NAME    READY   STATUS    RESTARTS   AGE
    web-0   1/1     Running   0          102m
    web-1   1/1     Running   0          69s
    web-2   1/1     Running   0          10m
    web-1   1/1   Terminating   0     89s
    web-1   0/1   Terminating   0     89s
    web-1   0/1   Terminating   0     90s
    web-1   0/1   Terminating   0     90s
    web-1   0/1   Pending   0     0s
    web-1   0/1   Pending   0     0s
    web-1   0/1   ContainerCreating   0     0s
    web-1   1/1   Running   0     20s
    ```


## Manage persistent storage of the StatefulSet service

1.  Run the following command to view the files in the /data path of each pod:

    ```
    kubectl exec web-1 ls /data
    ```

    ```
    lost+found
    ```

2.  Run the following command to create the file statefulset in the /data path:

    ```
    kubectl exec web-1 touch /data/statefulset
    ```

3.  Run the following command to view the files in the /data path of each pod:

    ```
    kubectl exec web-1 ls /data
    ```

    ```
    lost+found
    statefulset
    ```

4.  Run the following command to delete the pod named web-1:

    ```
    kubectl delete pod web-1
    ```

    ```
    pod "web-1" deleted
    ```

5.  Run the following command to verify that the file statefulset exists in the /data path. This indicates that the data in the disk is persistently stored.

    ```
    kubectl exec web-1 ls /data
    ```

    ```
    lost+found
    statefulset
    ```


