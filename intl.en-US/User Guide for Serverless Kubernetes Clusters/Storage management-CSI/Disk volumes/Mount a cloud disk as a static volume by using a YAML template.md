# Mount a cloud disk as a static volume by using a YAML template

This topic describes how to use a YAML template with the Alibaba Cloud Container Storage Interface \(CSI\) plug-in to mount a cloud disk as a static volume.

-   A Container Service for Kubernetes \(ACK\) cluster is created and the CSI plug-in is deployed in the cluster.
-   A cloud disk that is charged on a pay-as-you-go basis is created. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

## Create a static PV and a PVC

After you create a cloud disk in the Elastic Compute Service \(ECS\) console, record the disk ID. The ID of the cloud disk that is used in this topic is `d-wz92s6d95go6ki9xge6b`.

You can only use a YAML template to create a CSI persistent volume \(PV\) object in the ACK console.

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Use the following template to create a static PV and a persistent volume claim \(PVC\):

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

    -   driver: the type of the driver.

        This parameter is set to `diskplugin.csi.alibabacloud.com`. The value indicates that the Alibaba Cloud CSI plug-in is used.

    -   volumeHandle: the ID of the cloud disk.
    -   nodeAffinity: the zone to which the PV and PVC belong.

        You can set this parameter to specify the zone to which the pod of the PV and PVC are scheduled.


## Create an application

1.  Create the nginx-disk-dept.yaml file and copy the following content to the file:

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


You can also mount a cloud disk as a static volume in the ACK console. For more information, see [Use a disk in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use a disk in the ACK console.md).

