---
keyword: [ACK Windows nodes, disks, storage resources, Kubernetes]
---

# Mount Alibaba Cloud disks to Windows containers

You can use Alibaba Cloud disks as the storage resources for Windows containers that run in a Container Service for Kubernetes \(ACK\) cluster. This topic describes how to mount Alibaba Cloud disks to Windows containers.

-   [Create a node pool that runs Windows](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a node pool that runs Windows.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Procedure

1.  Deploy the FlexVolume plug-in in the ACK cluster.

    1.  Create a YAML file.

        Use the following template to create a YAML file, and deploy the FlexVolume plug-in in the ACK cluster. Then, create a StorageClass in the Windows environment, and update the default component alicloud-disk-controller in the ACK cluster. This allows you to mount Alibaba Cloud disks as Windows persistent volumes \(PVs\).

        FlexVolume is a storage plug-in that is supported by Kubernetes. For more information, see [Volume plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md).



    2.  In the command-line interface \(CLI\), enter `kubectl apply -f <YAML file name>.yaml` to deploy the plug-in.

2.  Create an application.

    1.  Use the following YAML template to create a persistent volume claim \(PVC\) and a StatefulSet. This allows you to check whether the storage plug-in works as expected.

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
        apiVersion: apps/v1beta2
        kind: StatefulSet
        metadata:
          name: disk-windows
        spec:
          selector:
            matchLabels:
              app: nginx
          serviceName: "nginx"
          replicas: 1
          template:
            metadata:
              labels:
                app: nginx
            spec:
              tolerations:
              - effect: NoSchedule
                key: os
                operator: Equal
                value: windows
              containers:
              - name: nginx
                image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.16.9.7be0fa0-windows1809
                command: ["pwsh.exe"]
                args: ["-Command", "start-sleep 10000"]
                volumeMounts:
                  - name: disk-essd
                    mountPath: "C:\\data"
          volumeClaimTemplates:
          - metadata:
              name: disk-essd
            spec:
              accessModes: [ "ReadWriteOnce" ]
              storageClassName: alicloud-disk-essd-windows
              resources:
                requests:
                  storage: 20Gi
        ```

    If the storage plug-in is deployed, after you run the `kubectl get pv` command, the PV is automatically created.

    The following output is returned:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM              STORAGECLASS        REASON   AGE
    d-2zeh2yew2t48lu75joy1   20Gi       RWO            Delete           Bound    default/pvc-disk   alicloud-disk-ssd            2m46s
    ```


