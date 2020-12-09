---
keyword: [Velero, back up applications, restore applications]
---

# Use migrate-controller to back up and restore applications

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Kubernetes applications. This topic describes how to use migrate-controller to back up and restore applications in a Container Service for Kubernetes \(ACK\) cluster.

[Install and use migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install and use migrate-controller.md)

## Back up applications

You can use the Velero tool of migrate-controller to back up all resources in one or more specified namespaces of an ACK cluster. Command:

```
velero backup create <BACKUP NAME> --include-namespaces <INCLUDE NAMESPACES>
```

The following examples show how to back up all resources in one namespace and all resources in two namespaces by running commands:

-   Back up applications in the nginx-examples namespace:

    ```
    velero backup create backup-nginx --include-namespaces nginx-examples
    ```

-   Back up applications in the nginx-examples namespace and wordpress namespace:

    ```
    velero backup create backup-nginx-and-wordpress --include-namespaces nginx-examples,wordpress
    ```


**Note:** The preceding operations do not back up the data of persistent volumes \(PVs\) that are mounted to the applications. Instead, only orchestration files in the JSON format are backed up.

If you want to back up the PVs, perform the following steps:

1.  Add the backup.velero.io/backup-volumes annotation to pods to which the PVs are mounted. Command:

    ```
    kubectl -n <NAMESPACE> annotate pod/<POD NAME> backup.velero.io/backup-volumes=<POD VOLUME NAME>
    ```

2.  After you add the annotation, back up the PVs. Command:

    ```
    velero backup create <BACKUP NAME> --include-namespaces <INCLUDE NAMESPACES>
    ```

    For example, you want to back up an NGINX application in the nginx-examples namespace and the PV of the application. The PV is named nginx-logs and mounted to the nginx-deployment-7477779c4f-rz95l pod. To back up the PV, perform the following steps:

    1.  Add an annotation to the pod to which the PV is mounted. Command:

        ```
        kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
        ```

    2.  Back up the PV. Command:

        ```
        velero backup create nginx-backup-with-pv --include-namespaces nginx-example
        ```


## Back up applications at a scheduled time

To back up applications at a scheduled time, run the following command to create a scheduled backup task:

```
velero schedule create <NAME> --include-namespaces <NAMESPACE> --schedule "SCHEDULE"
```

The following examples show how to back up applications every 7 days and every 24 hours by running commands:

-   Create the nginx-app-with-pv-schedule-01 scheduled task to back up applications in the nginx-examples namespace every 7 days:

    ```
    velero schedule create nginx-app-with-pv-schedule-01 --include-namespaces nginx-examples --schedule "0 7 * * *"
    ```

-   Create the nginx-app-with-pv-schedule-02 scheduled task to back up applications in the nginx-examples namespace every 24 hours:

    ```
    velero schedule create nginx-app-with-pv-schedule-01 --include-namespaces nginx-examples --schedule "@every 24h"
    ```


## Restore applications

This example describes how to restore applications after the namespace to which the applications belong is deleted.

1.  Run the following command to delete the nginx-example namespace:

    ```
    kubectl delete ns nginx-example
    ```

2.  Run the following command to restore applications:

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


**Related topics**  


[Use migrate-controller to migrate applications across clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Use migrate-controller to migrate applications across clusters.md)

