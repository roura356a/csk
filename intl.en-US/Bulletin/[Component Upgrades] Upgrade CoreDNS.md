---
keyword: [outdated CoreDNS version, upgrade CoreDNS, manual upgrade]
---

# \[Component Upgrades\] Upgrade CoreDNS

To improve the stability of DNS resolution in Container Service for Kubernetes \(ACK\) clusters, we recommend that you upgrade CoreDNS to the latest version. This topic describes how to upgrade CoreDNS.

The following issues exist in CoreDNS versions earlier than 1.7.0 and may affect the stability of DNS resolution in ACK clusters:

-   CoreDNS must send watch requests to the API server to query Service data. Therefore, the health check plug-in is used to check the connectivity between CoreDNS and the API server. If connectivity exceptions occur, such as network jitters or API server restarts, the health check plug-in reports errors. CoreDNS restarts if the health check failed for three consecutive times. Services are unavailable during the restart process. For more information, see [coredns](https://github.com/coredns/coredns/issues/2629).
-   CoreDNS is affected by the bug of klog. After CoreDNS is disconnected from the API server, klog attempts to write an error log entry to a directory named /tmp. However, this directory does not exist. As a result, the logging operation fails and causes CoreDNS to stop running. For more information, see [Kubernetes: add mount of /tmp \#137](https://github.com/coredns/deployment/issues/137).
-   Out of memory \(OOM\) errors may occur in large-scale clusters that use the default memory limit.

## Automatically upgrade CoreDNS

You can upgrade CoreDNS on the Add-ons page of the ACK console. For more information about the precautions for upgrading CoreDNS, see [Precautions for upgrading CoreDNS](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Precautions for upgrading CoreDNS.md). For more information about how to automatically upgrade CoreDNS, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).

For ACK clusters of Kubernetes 1.14.8 and later, you can upgrade CoreDNS to the latest version. For ACK clusters whose Kubernetes versions are earlier than 1.14.8, you can upgrade CoreDNS to 1.6.2 or earlier.

If no Upgrade button is displayed on the Add-ons page of the ACK console and the current CoreDNS version is outdated, it means that you cannot automatically upgrade CoreDNS for your cluster. In this case, you can manually upgrade CoreDNS. For more information, see [Manually upgrade CoreDNS](#section_7wd_grs_c5s).

If you used to manually upgrade CoreDNS, you must check the CoreDNS configuration file and make sure that the ready plug-in is enabled before you can configure to automatically upgrade CoreDNS. If the ready plug-in is not specified in the configuration file, you must enable the ready plug-in before you configure to automatically upgrade CoreDNS. For more information about how to enable the ready plug-in, see [Enable the ready plug-in](#section_ksj_o3p_1n8).

## Manually upgrade CoreDNS

1.  Check the compatibility between the Kubernetes version and the CoreDNS version.

    Check the Kubernetes version of the ACK cluster. Make sure that the Kubernetes version is compatible with CoreDNS 1.6.2. The following table lists the Kubernetes versions that are compatible with CoreDNS 1.6.2. Kubernetes 1.11, 1.12, 1.14, and 1.16 are compatible with CoreDNS 1.6.2.

    |Item|Compatible version|
    |----|------------------|
    |Kubernetes version|1.11|1.12|1.14|1.16|
    |CoreDNS|1.6.2|1.6.2|1.6.2|1.6.2|

    Perform the following steps to check the Kubernetes version of an ACK cluster:

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and check the Kubernetes version in the Version column.

        ![version](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3555065061/p176529.png)

2.  Check the CoreDNS version.

    -   You can check the CoreDNS version in the ACK console.
        1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

        2.  In the left-side navigation pane of the ACK console, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.
        5.  On the **Deployments** page, set **Namespace** to kube-system and check the CoreDNS version in the Image column.

            ![dns](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7722866261/p176315.png)

    -   You can also run the following kubectl command to query the CoreDNS version:

        ```
        kubectl get deployment coredns -n kube-system -o jsonpath="{.spec.template.spec.containers[0].image}"
        ```

        Expected output:

        ```
        registry-vpc.cn-hangzhou.aliyuncs.com/acs/coredns:1.3.1
        ```

3.  Modify the coredns ConfigMap.

    In CoreDNS 1.6.2, the proxy plug-in is replaced by the forward plug-in. You must replace proxy with forward in the **coredns** ConfigMap in the kube-system namespace. In addition, you must add the `ready` field to enable the ready plug-in.

    -   You can modify the **coredns** ConfigMap in the ACK console.
        1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

        2.  In the left-side navigation pane of the ACK console, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.
        5.  On the top of the **ConfigMap** page, set **Namespace** to kube-system. Find the coredns ConfigMap and click **Edit YAML** in the **Actions** column.
        6.  In the **View in YAML** panel, replace **proxy** with **forward**.

            ![forward](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3555065061/p176548.png)

        7.  Add the `ready` field as shown in the following figure. Then, click **OK**.

            ```
            ready # Add this line and make sure that word "ready" is aligned with word "kubernetes". 
            kubernetes cluster.local in-addr.arpa ip6.arpa {
               pods verified
               fallthrough in-addr.arpa ip6.arpa
            }
            ```

    -   You can also run a kubectl command to modify the coredns ConfigMap.

        ```
        # Modify the coredns ConfigMap. 
        kubectl edit configmap/coredns -n kube-system
        
        # Replace proxy with forward. 
        # Add the ready field. Note that word "ready" must be aligned with word "kubernetes". 
        # Save the change and exit. 
        ```

4.  Print the log of a CoreDNS pod to check whether the new configuration is loaded. It requires about 30 seconds for hot loading to complete.

    1.  Run the following command to query the status of CoreDNS pods in the cluster:

        ```
        kubectl get pods -n kube-system | grep coredns
        ```

        Expected output:

        ```
        coredns-78d4b8bd88-6g62w                           1/1     Running   0          9d
        coredns-78d4b8bd88-n6wjm                           1/1     Running   0          9d
        ```

    2.  Run the following command to query the log of a CoreDNS pod:

        ```
        kubectl logs coredns-78d4b8bd88-n6wjm -n kube-system
        ```

        Expected output:

        ```
        .:53
        [INFO] plugin/reload: Running configuration MD5 = 71c5f1ff539d304c630521f315dc2ac2
        CoreDNS-1.6.7
        linux/amd64, go1.13.6, da7f65b
        [INFO] 127.0.0.1:48329 - 42313 "HINFO IN 1108347002237365533.4506541768939609094. udp 57 false 512" NXDOMAIN qr,rd,ra 132 0.008874794s
        ```

        The output shows `plugin/reload`. This indicates that the new CoreDNS configuration is loaded.

5.  Change the image version of the CoreDNS application to V1.6.2.

    -   Change the image version in the ACK console.
        1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

        2.  In the left-side navigation pane of the ACK console, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.
        5.  On the top of the **Deployments** page, set **Namespace** to kube-system. Find **coredns** and choose **More** \> **View in YAML** in the Actions column.
        6.  In the **Edit YAML** dialog box, change the image version to **1.6.2**. Click **Update**.

            ![image](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3555065061/p176558.png)

    -   Change the image version by using kubectl.

        ```
        # Modify the coredns Deployment. 
        kubectl edit deployment/coredns -n kube-system
        
        # Change the image version to 1.6.2. 
        # Save the change and exit. 
        ```

6.  Verify the result.

    Run the following command to check whether all CoreDNS pods in the cluster are in the Running state:

    ```
    kubectl get pods -n kube-system | grep coredns
    ```

    Expected output:

    ```
    coredns-78d4b8bd88-6g62w                           1/1     Running   0          9d
    coredns-78d4b8bd88-n6wjm                           1/1     Running   0          9d
    ```


## Enable the ready plug-in

If you used to manually upgrade CoreDNS by following the guide in this topic and the current CoreDNS version is later than 1.5.0, confirm that the ready plug-in is enabled in the CoreDNS configuration file. Otherwise, CoreDNS fails to start.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the top of the **ConfigMap** page, set **Namespace** to kube-system. Find the coredns ConfigMap and click **Edit YAML** in the **Actions** column.

6.  In the **View in YAML** panel, check whether the `ready` field exists. If not, add the `ready` field and click **OK**.

    ```
    apiVersion: v1
    data:
      Corefile: |
        .:53 {
            errors
            health {
                lameduck 15s
            }
            ready # Add this line and make sure that word "ready" is aligned with word "kubernetes". 
            kubernetes cluster.local in-addr.arpa ip6.arpa {
                pods verified
                fallthrough in-addr.arpa ip6.arpa
            }
            prometheus :9153
            forward . /etc/resolv.conf {
                max_concurrent 1000
            }
            cache 30
            loop
            log
            reload
            loadbalance
        }
    ```

7.  Run the following command to print the log of a CoreDNS pod to check whether the new configuration is loaded. It requires about 30 seconds for hot loading to complete.

    ```
    kubectl logs coredns-78d4b8bd88-n6wjm -n kube-system
    ```

    The output shows `plugin/reload`. This indicates that the new CoreDNS configuration is loaded.


**Related topics**  


[CoreDNS](/intl.en-US/Release notes/System Component change Records/Networking/CoreDNS.md)

