---
keyword: [CoreDNS upgrading, DNS resolution failure, timeout]
---

# Precautions for upgrading CoreDNS

When you upgrade CoreDNS, Container Service for Kubernetes \(ACK\) migrates the configuration file named Corefile and recreates the pods for CoreDNS. During the upgrade process, you may encounter DNS resolution failures or timeouts. This topic describes the inspections and optimizations that you must perform before you upgrade CoreDNS. This reduces the chances of DNS resolution failures and timeouts.

-   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md)
-   [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md)

## Precautions for upgrading CoreDNS

During the upgrade process, ACK overwrites the CoreDNS YAML template with the configurations of the new CoreDNS version, excluding the following settings:

-   The number of replicated pods
-   The **coredns** ConfigMap

In CoreDNS 1.7.0 and later, the default memory limit is 2 GB. This ensures sufficient resources for CoreDNS and avoids out of memory \(OOM\) errors. Under normal circumstances, you do not need to modify the limit.

Modifications performed before the upgrade, such as toleration changes and changes on CPU and memory requests and limits, will be overwritten. To keep these modifications, manually upgrade CoreDNS or perform these modifications again after CoreDNS is automatically upgraded. For more information, see [\[Component Upgrades\] Upgrade CoreDNS to V1.6.2 or later](t1964489.md#).

Before you upgrade CoreDNS, perform the following operations to reduce the chances of DNS resolution failures and timeouts:

-   [Set the timeout period of UDP sessions if the kube-proxy mode of your cluster is set to IPVS](#section_m9z_3lo_iv4)
-   [Configure the kube-dns Service](#section_66f_5fj_cl0)
-   [Configure graceful shutdown for CoreDNS](#section_btw_3tp_tte)

## Set the timeout period of UDP sessions if the kube-proxy mode of your cluster is set to IPVS

If the kube-proxy mode of your ACK cluster is set to IPVS, you must set the timeout period of UDP sessions to 10 seconds. If your ACK cluster contains UDP-based workloads, check the impacts on these workloads before you perform this operation.

**Note:** If the kube-proxy mode of your cluster is not set to IPVS, you do not need to set the timeout period of UDP sessions. For more information about how to check the kube-proxy mode of your cluster, see [View basic information](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Manage clusters/View cluster information.md).

**ACK clusters that run Kubernetes 1.18 or later**

**Use the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the ConfigMap page, select the **kube-system** namespace, find the kube-proxy-worker ConfigMap, and then click **Edit YAML** in the Actions column.

6.  In the ipvs field of the **View in YAML** panel, add `udpTimeout: 10s` and click **OK**.

    ```
    apiVersion: v1
    data:
      config.conf: |
        apiVersion: kubeproxy.config.k8s.io/v1alpha1
        kind: KubeProxyConfiguration
        // Irrelevant fields are not displayed. 
        mode: ipvs
        // If the ipvs key does not exist, add the key in this field. 
        ipvs:
          udpTimeout: 10s
    ```

7.  Recreate all of the pods named kube-proxy-worker.

    1.  In the left-side navigation pane of the details page, choose **Workloads** \> **DaemonSets**.

    2.  On the DaemonSets page, find and click **kube-proxy-worker**.

    3.  On the **Pods** tab of the **kube-proxy-worker** page, choose **More** \> **Delete** in the Actions column. In the message that appears, click **Confirm**.

        Repeat the preceding steps to delete all of the pods. After you delete the pods, the system automatically recreates the pods.

8.  Check whether the timeout period of UDP sessions is modified.

    1.  Run the following command to install ipvsadm.

        ipvsadm is a tool that is used to manage IPVS. For more information, see [ipvsadm](https://software.opensuse.org/package/ipvsadm).

        ```
        yum install -y ipvsadm
        ```

    2.  Log on to an Elastic Compute Service \(ECS\) instance in your cluster and run the following command to check the third value in the output:

        ```
        ipvsadm -L --timeout
        ```

        If the third value in the output is 10, it indicates that the timeout period of UDP sessions is modified for your cluster.

        **Note:** After the timeout period of UDP sessions is modified, wait at least 5 minutes before you proceed.


**Use the CLI**

1.  Run the following command to modify the kube-proxy-worker ConfigMap:

    ```
    kubectl -n kube-system edit configmap kube-proxy-worker
    ```

2.  In the ipvs field of the kube-proxy-worker ConfigMap, add `udpTimeout: 10s`. Then, save the ConfigMap and exit.

    ```
    apiVersion: v1
    data:
      config.conf: |
        apiVersion: kubeproxy.config.k8s.io/v1alpha1
        kind: KubeProxyConfiguration
        // Irrelevant fields are not displayed. 
        mode: ipvs
        // If the ipvs key does not exist, add the key in this field. 
        ipvs:
          udpTimeout: 10s
    ```

3.  Run the following command to recreate all of the pods named kube-proxy-worker.

    1.  Run the following command to query the pods:

        ```
        kubectl -n kube-system get pod -o wide | grep kube-proxy-worker
        ```

    2.  Run the following command to delete all of the pods that are returned in the preceding step. Then, the system recreates the pods named kube-proxy-worker.

        ```
        kubectl -n kube-system delete pod <kube-proxy-worker-xxxx>
        ```

        **Note:** Replace <kube-proxy-worker-xxxx\> with the names of the pods that are returned in the preceding step.

4.  Check whether the timeout period of UDP sessions is modified.

    1.  Run the following command to install ipvsadm.

        ipvsadm is a tool that is used to manage IPVS. For more information, see [ipvsadm](https://software.opensuse.org/package/ipvsadm).

        ```
        yum install -y ipvsadm
        ```

    2.  Log on to an Elastic Compute Service \(ECS\) instance in your cluster and run the following command to check the third value in the output:

        ```
        ipvsadm -L --timeout
        ```

        If the third value in the output is 10, it indicates that the timeout period of UDP sessions is modified for your cluster.

        **Note:** After the timeout period of UDP sessions is modified, wait at least 5 minutes before you proceed.


**ACK clusters that run Kubernetes 1.16 or earlier**

kube-proxy of an ACK cluster that runs Kubernetes 1.16 or earlier does not support the udpTimeout parameter. We recommend that you use Operation Orchestration Service \(OOS\) and run the `ipvsadm` command on all ECS instances of your cluster at a time to modify the timeout period of UDP sessions. Run the following commands:

```
yum install -y ipvsadm
ipvsadm -L --timeout > /tmp/ipvsadm_timeout_old
ipvsadm --set 900 120 10
ipvsadm -L --timeout > /tmp/ipvsadm_timeout_new
diff /tmp/ipvsadm_timeout_old /tmp/ipvsadm_timeout_new
```

For more information about how to use OOS to manage multiple ECS instances at a time, see [Manage multiple instances]().

## Configure the kube-dns Service

**Use the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

5.  In the kube-system namespace, find the **kube-dns** Service and click **View in YAML** in the Actions column.

6.  -   If the value of the sessionAffinity field is `None`, skip the following steps.
-   If the value of the sessionAffinity field is `ClientIP`, perform the following steps.
7.  Delete sessionAffinity, sessionAffinityConfig, and all of the subfields. Then, click **Update**.

    ```
    // Delete the following content. 
    sessionAffinity: ClientIP
      sessionAffinityConfig:
        clientIP:
          timeoutSeconds: 10800
    ```

8.  Find the **kube-dns** Service and click **View in YAML** in the Actions column again to check whether the value of the sessionAffinity field is `None`. If the value is `None`, it indicates that the kube-dns Service is modified.


**Use the CLI**

1.  Run the following command to query the configurations of the kube-dns Service:

    ```
    kubectl -n kube-system get svc kube-dns -o yaml
    ```

2.  -   If the value of the sessionAffinity field is `None`, skip the following steps.
-   If the value of the sessionAffinity field is `ClientIP`, perform the following steps.
3.  Run the following command to modify the kube-dns Service:

    ```
    kubectl -n kube-system edit service kube-dns
    ```

4.  Delete all of the fields related to sessionAffinity, including sessionAffinity, sessionAffinityConfig, and all of the subfields. Then, save the modifications and exit.

    ```
    // Delete the following content. 
    sessionAffinity: ClientIP
      sessionAffinityConfig:
        clientIP:
          timeoutSeconds: 10800
    ```

5.  After you modify the kube-dns Service, run the following command again to check whether the value of the sessionAffinity field is `None`. If the value is `None`, it indicates that the kube-dns Service is modified.

    ```
    kubectl -n kube-system get svc kube-dns -o yaml
    ```


## Configure graceful shutdown for CoreDNS

**Note:** When CoreDNS updates its configurations, additional memory resources may be consumed. After the coredns ConfigMap is modified, check the status of the CoreDNS pods. If the memory resources of the pods are exhausted, modify the memory limit of pods in the CoreDNS Deployment. We recommend that you change the memory limit to 2 GB.

**Use the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  Select the kube-system namespace. Find the coredns ConfigMap and click **Edit YAML** in the Actions column.

6.  Refer to the following YAML file and make sure that the health plug-in is enabled. Then, set lameduck to `15s` and click **OK**.

    ```
    .:53 {
            errors
            
            // The health plug-in may use different settings for different CoreDNS versions. 
            // Scenario 1: The health plug-in is disabled by default. 
            
            // Scenario 2: The health plug-in is enabled by default but lameduck is not set. 
            health
            
            // Scenario 3: The health plug-in is enabled by default and lameduck is set to 5s.    
            health {
                lameduck 5s
            }
            
            // In the preceding scenarios, change the value of lameduck to 15s. 
            health {
                lameduck 15s
            }
            
            // You do not need to modify other plug-ins. 
        }
    ```

    If the CoreDNS pods run as normal, it indicates that CoreDNS can be shut down gracefully. If the CoreDNS pods do not run as normal, you can check the pod events and log to locate the issues.


**Use the CLI**

1.  Run the following command to open the coredns ConfigMap:

    ```
    kubectl -n kube-system edit configmap coredns
    ```

2.  Refer to the following YAML file and make sure that the health plug-in is enabled. Then, set lameduck to `15s`.

    ```
    .:53 {
            errors
            
            // The health plug-in may use different settings for different CoreDNS versions. 
            // Scenario 1: The health plug-in is disabled by default. 
            
            // Scenario 2: The health plug-in is enabled by default but lameduck is not set. 
            health
            
            // Scenario 3: The health plug-in is enabled by default and lameduck is set to 5s.    
            health {
                lameduck 5s
            }
            
            // In the preceding scenarios, change the value of lameduck to 15s. 
            health {
                lameduck 15s
            }
            
            // You do not need to modify other plug-ins. 
        }
    ```

3.  After you modify the coredns ConfigMap, save the modifications and exit.

    If the CoreDNS pods run as normal, it indicates that CoreDNS can be shut down gracefully. If the CoreDNS pods do not run as normal, you can check the pod events and log to locate the issues.


**Related topics**  


[In IPVS mode, kube-proxy doesn't handle graceful termination of UDP flows](https://github.com/kubernetes/kubernetes/issues/71514)

[CoreDNS](/intl.en-US/Release notes/System Component change Records/Networking/CoreDNS.md)

