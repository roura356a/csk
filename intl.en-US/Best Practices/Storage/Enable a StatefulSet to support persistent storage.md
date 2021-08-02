---
keyword: [StatefulSet, persistent storage, CSI]
---

# Enable a StatefulSet to support persistent storage

You can create a pair of persistent volume \(PV\) and persistent volume claim \(PVC\) for each pod by setting the VolumeClaimTemplate in a StatefulSet. If pods are deleted or scaled in, PVs and PVCs of the StatefulSet application are not deleted. This topic describes how to enable a StatefulSet to support persistent storage by setting the VolumeClaimTemplate.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

## Scenarios

When to use a StatefulSet:

-   Predefined deployment order: Pods are deployed or scaled out from 0 to N-1 in sequence. The system must wait until all of the preceding pods reach the Running or Ready state before it can deploy another pod.
-   Predefined scale-in order: Pods are scaled in or deleted from N-1 to 0 in sequence. The system must wait until all of the preceding pods are deleted before it can delete another pod.
-   Consistent network identifiers: After a pod is rescheduled, its PodName and HostName values remain unchanged.
-   Stable data persistence: After a pod is rescheduled, the pod can still access the same persisted data.

How to use a StatefulSet:

PVCs and PVs are automatically created based on the `VolumeClaimTemplates`.

## Deploy a StatefulSet application

**Note:** volumeClaimTemplates: The system uses this template to create PVCs. The number of PVCs equals the number of replicas that are deployed for the StatefulSet application. The configurations of these PVCs are the same except for the PVC names.

1.  Use the following template to create a file named statefulset.yaml.

    Deploy a Service and a StatefulSet, and provision two pods for the StatefulSet.

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

    -   `replicas`: the parameter is set to 2 in this example. This indicates that two pods are created.
    -   `mountPath`: the path where the disk is mounted in the container.
    -   `accessModes`: the access mode of the StatefulSet.
    -   `storageClassName`: the parameter is set to `alicloud-disk-ssd` in this example. This indicates that an Alibaba Cloud standard SSD is used.
    -   `storage`: specifies the storage that is required by the application.
2.  Run the following command to deploy the StatefulSet application:

    ```
    kubectl create -f statefulset.yaml
    ```

3.  Run the following command to view the deployed pods:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          6m
    web-1                         1/1     Running   0          6m
    ```

4.  Run the following command to view the PVCs:

    ```
    kubectl get pvc
    ```

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   7m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   6m
    ```


## Verify that the PVCs are scaled out together with the StatefulSet application

1.  Run the following command to scale out the StatefulSet application to three pods:

    ```
    kubectl scale sts web --replicas=3
    ```

    Expected output:

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the StatefulSet application is scaled out:

    ```
    kubectl get pod
    ```

    Expected output:

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

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   35m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   34m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   27m
    ```

    The output indicates that three PVCs are provisioned for the StatefulSet application after the StatefulSet application is scaled to three pods.


## Verify that the PVCs remain unchanged after the StatefulSet application is scaled in.

1.  Run the following command to scale the StatefulSet application to two pods:

    ```
    kubectl scale sts web --replicas=2
    ```

    Expected output:

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the StatefulSet application is scaled in:

    ```
    kubectl get pod
    ```

    Expected output:

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

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   39m
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   31m
    ```

    After the StatefulSet application is scaled to two pods, the StatefulSet application still has three PVCs. This indicates that the PVCs are not scaled in together with the StatefulSet application.


## Verify that the PVCs remain unchanged when the StatefulSet application is scaled out again

When the StatefulSet is scaled out again, verify that the PVCs remain unchanged.

1.  Run the following command to scale out the StatefulSet application to three pods:

    ```
    kubectl scale sts web --replicas=3
    ```

    Expected output:

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to view the pods after the StatefulSet application is scaled out:

    ```
    kubectl get pod
    ```

    Expected output:

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

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    The newly created pod uses an existing PVC.


## Verify that the PVCs remain unchanged when the pod of the StatefulSet application is deleted

1.  Run the following command to view the PVC that is used by the pod named web-1:

    ```
    kubectl describe pod web-1 | grep ClaimName
    ```

    Expected output:

    ```
    ClaimName:  disk-ssd-web-1
    ```

2.  Run the following command to delete the pod named web-1:

    ```
    kubectl delete pod web-1
    ```

    Expected output:

    ```
    pod "web-1" deleted
    ```

3.  Run the following command to view the pod:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    web-0                         1/1     Running   0          1h
    web-1                         1/1     Running   0          25s
    web-2                         1/1     Running   0          9m
    ```

    The recreated pod uses the same name as the deleted pod.

4.  Run the following command to view the PVCs:

    ```
    kubectl get pvc
    ```

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    disk-ssd-web-0   Bound    d-2zegw7et6xc96nbojuoo   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-1   Bound    d-2zefbrqggvkd10xb523h   20Gi       RWO            alicloud-disk-ssd   1h
    disk-ssd-web-2   Bound    d-2ze4jx1zymn4n9j3pic2   20Gi       RWO            alicloud-disk-ssd   1h
    ```

    The recreated pod uses an existing PVC.


## Verify that the StatefulSet application supports persistent storage

1.  Run the following command to view the file in the /data path:

    ```
    kubectl exec web-1 ls /data
    ```

    Expected output:

    ```
    lost+found
    ```

2.  Run the following command to create a file named statefulset in the /data path:

    ```
    kubectl exec web-1 touch /data/statefulset
    ```

3.  Run the following command to view the file in the /data path:

    ```
    kubectl exec web-1 ls /data
    ```

    Expected output:

    ```
    lost+found
    statefulset
    ```

4.  Run the following command to delete the pod named web-1:

    ```
    kubectl delete pod web-1
    ```

    Expected output:

    ```
    pod "web-1" deleted
    ```

5.  Run the following command to view the file in the /data path:

    ```
    kubectl exec web-1 ls /data
    ```

    Expected output:

    ```
    lost+found
    statefulset
    ```

    The statefulset file still exists in the /data path. This indicates that data is persisted to the disk.


