---
keyword: [migrate-controller, across clusters, migrate, backup]
---

# Use migrate-controller to migrate applications across clusters

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Kubernetes applications. You can use the Velero tool of migrate-controller to migrate applications across clusters. This topic describes how to migrate applications across clusters.

[Install and use migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install and use migrate-controller.md)

## Procedure

Assume that you have two clusters: cluster01 and cluster02. You want to migrate applications in the nginx-examples namespace from cluster01 to cluster02. To migrate the applications, perform the following steps:

1.  Install the Velero client and migrate-controller in cluster01. Then, configure and connect to the Object Storage Service \(OSS\) bucket named mybackups. For more information, see [Install and use migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install and use migrate-controller.md).

2.  Install the Velero client and migrate-controller in cluster02. Then, configure and connect to the OSS bucket named mybackups. For more information, see [Install and use migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install and use migrate-controller.md).

3.  Back up applications in the nginx-examples namespace in cluster01 and specify the backup name as nginx-backup.

4.  Open nginx-backup in cluster02 to restore the applications. This migrates the applications from cluster01 to cluster02.


## Migration example

In this example, an application in cluster01 is backed up and then restored in cluster02. This way, the application is migrated from cluster01 to cluster02. Before you back up and restore the application, perform Step 1 and Step 2 in [Migration process](#migrate). To back up and restore the application, perform the following steps:

1.  Deploy the application named nginx-app-with-pv.

    1.  Create the nginx-app-with-pv.yaml file and copy the following content to the file:

        ```
        ---
        apiVersion: v1
        kind: Namespace
        metadata:
          name: nginx-example
          labels:
            app: nginx
        
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: nginx-logs
          namespace: nginx-example
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 20Gi
          storageClassName: alicloud-disk-ssd
        
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nginx-deployment
          namespace: nginx-example
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                app: nginx
              annotations:
                pre.hook.backup.velero.io/container: fsfreeze
                pre.hook.backup.velero.io/command: '["/sbin/fsfreeze", "--freeze", "/var/log/nginx"]'
                post.hook.backup.velero.io/container: fsfreeze
                post.hook.backup.velero.io/command: '["/sbin/fsfreeze", "--unfreeze", "/var/log/nginx"]'
            spec:
              volumes:
                - name: nginx-logs
                  persistentVolumeClaim:
                   claimName: nginx-logs
              containers:
              - image: nginx:1.7.9
                name: nginx
                ports:
                - containerPort: 80
                volumeMounts:
                  - mountPath: "/var/log/nginx"
                    name: nginx-logs
                    readOnly: false
              # sync from gcr.io/heptio-images/fsfreeze-pause:latest
              - image: registry.cn-hangzhou.aliyuncs.com/acs/fsfreeze-pause:latest
                name: fsfreeze
                securityContext:
                  privileged: true
                volumeMounts:
                  - mountPath: "/var/log/nginx"
                    name: nginx-logs
                    readOnly: false
        ```

    2.  Run the following command to deploy the application to cluster01:

        ```
        kubectl apply -f nginx-app-with-pv.yam
        ```

2.  Run the following commands to view the application information.

    Run the following command to view Deployment:

    ```
    kubectl -n nginx-example get deployment
    ```

    Output:

    ```
    NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.extensions/nginx-deployment   1/1     1            1           108s
    ```

    Run the following command to view pods:

    ```
    kubectl -n nginx-example get pod
    ```

    Output:

    ```
    NAME                                    READY   STATUS    RESTARTS   AGE
    pod/nginx-deployment-7477779c4f-rz95l   2/2     Running   0          108s
    ```

    Run the following command to view the persistent volume claim \(PVC\):

    ```
    kubectl -n nginx-example get pvc
    ```

    Output:

    ```
    NAME                               STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    persistentvolumeclaim/nginx-logs   Bound    d-bp1eutshfe774exa5os2   20Gi       RWO            alicloud-disk-ssd   108s
    ```

3.  Back up the namespace to which the application belongs.

    -   To back up the namespace excluding persistent volume \(PV\) data, run the following command:

        ```
        velero backup create nginx-backup-without-pv --include-namespaces nginx-example
        ```

    -   To back up the namespace and PV data, perform the following steps:
        1.  You must add an annotation to the pod to which the PV is mounted. Command:

            ```
            kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
            ```

        2.  Back up the PV. Command:

            ```
            velero backup create nginx-backup-with-pv --include-namespaces nginx-example
            ```

4.  Run the following command to view information about the backup:

    ```
    velero backup get
    ```

    Output \(without PV data\):

    ```
    NAME                      STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    nginx-backup-without-pv   Completed   0        0          2020-09-18 14:43:13 +0800 CST   29d       default            <none>
    ```

    The output indicates that you have backed up the application.

5.  To migrate the application to cluster02, run the following command to restore the application \(without PV data\) in cluster02:

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


**Related topics**  


[t1993430.md\#]()

