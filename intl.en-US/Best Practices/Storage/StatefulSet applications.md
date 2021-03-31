# StatefulSet applications

This topic describes the scenarios where StatefulSet applications are commonly used and how to use StatefulSet applications.

## Background information

Scenarios:

-   Predefined deployment order: Pods are deployed or scaled out from 0 to N-1 in sequence. The system must wait until all of the preceding pods reach the Running or Ready state before it can deploy another pod.
-   Predefined scale-in order: Pods are scaled in or deleted from N-1 to 0 in sequence. The system must wait until all of the preceding pods are deleted before it can delete another pod.
-   Consistent network identifiers: After a pod is rescheduled, its PodName and HostName values remain unchanged.
-   Stable data persistence: After a pod is rescheduled, the pod can still access the same persisted data.

How to use StatefulSet applications:

Persistent volume claims \(PVCs\) and persistent volumes \(PVs\) are automatically created based on `volumeClaimTemplates`.

This topic describes how to manage a StatefulSet application by performing the following operations:

-   Deploy the StatefulSet application
-   Scale the StatefulSet application
-   Delete the StatefulSet application
-   Manage persistent storage of the StatefulSet application

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Deploy the StatefulSet application

**Note:** `volumeClaimTemplates`: The system uses this template to create PVCs. The number of PVCs equals the number of replicas that are deployed for the StatefulSet application. The configurations of these PVCs are the same except for the PVC names.

1.  Create a statefulset.yaml file.

    **Note:** Set `storageClassName` to alicloud-disk-ssd, which specifies an Alibaba Cloud standard SSD.

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

2.  Run the following command to deploy the StatefulSet application:

    ```
    kubectl create -f statefulset.yaml
    ```

3.  Open another command-line interface \(CLI\) of kubectl and run the following command to check whether the pods are deployed in sequence:

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

4.  Run the following command to view the deployed pods:

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


## Scale the StatefulSet application

Scale out the StatefulSet application

1.  Run the following command to scale out the StatefulSet application to three pods:

    ```
    kubectl scale sts web --replicas=3
    ```

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the StatefulSet application is scaled out:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          34m
    web-1                         1/1     Running   0          33m
    web-2                         1/1     Running   0          26m
    ```

3.  Run the following command to view the PVCs after the StatefulSet application is scaled out:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   35m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   34m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   27m
    ```


## Scale in the StatefulSet application

1.  Run the following command to scale in the StatefulSet application to two pods:

    ```
    kubectl scale sts web --replicas=2
    ```

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the StatefulSet application is scaled in:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          38m
    web-1                         1/1     Running   0          38m
    ```

    Only two pods are deployed for the StatefulSet application.

3.  Run the following command to view the PVCs after the StatefulSet application is scaled in:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   31m
    ```

    PVCs and PVs are not deleted together with the pods during the scale-in operation.


## Scale out the StatefulSet application again

1.  Run the following command to scale out the StatefulSet application to three pods:

    ```
    kubectl scale sts web --replicas=3
    ```

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the StatefulSet application is scaled out:

    ```
    kubectl get pod
    ```

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          1h
    web-1                         1/1     Running   0          1h
    web-2                         1/1     Running   0          8s
    ```

3.  Run the following command to view the PVCs after the StatefulSet application is scaled out:

    ```
    kubectl get pvc
    ```

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    The newly created pod uses an existing PVC and PV.


## Delete the StatefulSet application

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

3.  Run the following command to view the pod:

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

5.  Open another CLI of kubectl and run the following command to view how the pod is deleted and recreated:

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


## Manage persistent storage of the StatefulSet application

1.  Run the following command to view the files in the /data path:

    ```
    kubectl exec web-1 ls /data
    ```

    ```
    lost+found
    ```

2.  Run the following command to create a statefulset file in the /data path:

    ```
    kubectl exec web-1 touch /data/statefulset
    ```

3.  Run the following command to view the files in the /data path:

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

5.  Run the following command to verify that the file statefulset exists in the /data path. This indicates that data is persisted to the disk.

    ```
    kubectl exec web-1 ls /data
    ```

    ```
    lost+found
    statefulset
    ```


