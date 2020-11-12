# Use static disk volumes

This topic describes how to attach a static disk volume by using the Container Storage Interface \(CSI\) plug-in.

-   A Kubernetes cluster is created, and the CSI plug-in is installed in the cluster.
-   A pay-as-you-go disk is created. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a pay-as-you-go disk.md).

## Create a PV and a PVC

After you create a disk in the Elastic Compute Service \(ECS\) console, record its ID, for example, `d-wz92s6d95go6ki9xge6b`.

Currently, you can only use a YAML file to create a CSI persistent volume \(PV\) object in the Container Service console.

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Use the following template to create a PV and a persistent volume claim \(PVC\) for using a static volume:

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      selector:
        matchLabels:
          alicloud-pvname: static-disk-pv
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: csi-pv
      labels:
        alicloud-pvname: static-disk-pv
    spec:
      capacity:
        storage: 25Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: diskplugin.csi.alibabacloud.com
        volumeHandle: d-wz92s6d95go6ki9xge6b
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.diskplugin.csi.alibabacloud.com/zone
              operator: In
              values:
              - cn-shenzhen-a
    ```

    **Note:**

    -   driver: the driver type.

        Set this parameter to `diskplugin.csi.alibabacloud.com`, indicating that the Alibaba Cloud CSI plug-in is used.

    -   volumeHandle: the disk ID.
    -   nodeAffinity: the zone of the PV and PVC.

        By setting this parameter, you can schedule the pod where the PV and PVC are located to the corresponding zone.


## Create an application

1.  Create the nginx-disk-dept.yaml file and copy the following code to the file:

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
            - name: pvc-disk
              mountPath: /data
          volumes:
            - name: pvc-disk
              persistentVolumeClaim:
                claimName: csi-pvc
    ```

2.  Run the following command to create an application:

    ```
    kubectl apply -f nginx-disk-dept.yaml
    ```


