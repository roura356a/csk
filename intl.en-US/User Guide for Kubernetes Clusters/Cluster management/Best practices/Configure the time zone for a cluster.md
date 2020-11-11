# Configure the time zone for a cluster

By default, when you create a managed cluster, the system configures the system time zone and the time zone of worker nodes based on the location where the cluster is deployed. Container Service for Kubernetes \(ACK\) allows you to change the system time zone of a cluster and the time zone of worker nodes. This topic describes how to query and configure the time zone for a cluster.

## Considerations

-   You can change the system time zone of a managed cluster only during cluster creation.
-   After you create a cluster, you can change the time zone of worker nodes only.

    **Note:** To ensure the smooth running of a cluster, we recommend that you configure the time zone of worker nodes the same as that of the system time zone.


## Query time zones

-   Run the following command to query the system time zone of a managed cluster:

    ```
    kubectl get configmap -n kube-system ack-cluster-profile -o yaml | grep timezone
    ```

    Expected output:

    ```
    timezone: Asia/Shanghai
    ```

-   Run the following command to query the time zone of a worker node:

    ```
    ls -l /etc/localtime
    ```

    Expected output:

    ```
    lrwxrwxrwx 1 root root 30 Sep 30 18:44 /etc/localtime -> /usr/share/zoneinfo/Asia/Tokyo
    ```


## Change the time zone

After you create a cluster, you can change the time zone of worker nodes only. Perform the following steps to change the time zone of a worker node.

1.  Log on to a worker node by using Secure Shell \(SSH\).

2.  Run the following sample command to change the current time zone to Japan Standard Time:

    ```
    ln -sf  /usr/share/zoneinfo/Asia/Tokyo  /etc/localtime
    ```


