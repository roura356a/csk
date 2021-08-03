---
keyword: [node-local-dns, NodeLocal DNSCache, DaemonSet]
---

# Use NodeLocal DNSCache in an ACK cluster

Container Service for Kubernetes \(ACK\) allows you to deploy NodeLocal DNSCache to improve the stability and performance of service discovery. NodeLocal DNSCache is implemented as a DaemonSet and runs a DNS caching agent on cluster nodes to improve the efficiency of DNS resolution for ACK clusters. This topic describes how to deploy and configure NodeLocal DNSCache for an application in an ACK cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Limits

-   NodeLocal DNSCache does not support pods that run in serverless Kubernetes \(ASK\) clusters or on elastic container instances in managed or dedicated Kubernetes clusters.
-   The Terway version must be v1.0.10.301 or later. If your cluster runs Terway in inclusive ENI mode based on IPVLAN, you must modify the configurations of Terway. For more information, see [Modify the configurations of Terway](#section_wcp_x4c_oyc).
-   To install NodeLocal DNSCache, you can go to the Add-ons page or install ack-node-local-dns on the App Catalog page.
-   NodeLocal DNSCache serves as a transparent cache proxy for CoreDNS and does not provide features such as Hosts or Rewrite. If you want to enable these features, modify CoreDNS configurations.
-   If PrivateZone is used in the cluster, you must modify CoreDNS configurations before you enable PrivateZone. For more information, see [Compatibility with PrivateZone](#section_ajk_4vf_oqk).

## Introduction

ACK NodeLocal DNSCache is a local DNS cache solution developed based on the open source NodeLocal DNSCache project. The Helm chart name of ACK NodeLocal DNSCache is ack-node-local-dns. This solution consists of a DNS cache that runs as a DaemonSet and an admission controller that runs as a Deployment to dynamically inject DNSConfig.

-   The admission controller intercepts pod creation requests based on admission webhooks and dynamically injects cached DNSConfig to pod configurations.

    **Note:** If you do not enable the admission controller to automatically inject DNSConfig, you must manually add DNS settings to pod configurations. For more information, see Method 2: Manually specify DNSConfig in [Configure NodeLocal DNSCache for an application](#section_kf0_5uj_ay2).

-   The DaemonSet that runs a DNS cache on each node can create a virtual network interface. By default, the virtual network interface listens for DNS queries that are sent to the IP address 169.254.20.10. To change the IP address, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). DNS queries that are generated in pods are proxied by the DaemonSet based on pod DNSConfig and node network settings.

    **Note:** The DaemonSets that run DNS caches are built on CoreDNS and provide only proxy and cache services. Do not enable other features such as Hosts and Rewrite on the DaemonSets.


![How it works](../images/p266014.png "How it works")

|No.|Description|
|---|-----------|
|1|By default, a pod with the local DNSConfig injected uses NodeLocal DNSCache to listen for DNS queries that are sent to the IP address 169.254.20.10 on the node.|
|2|If NodeLocal DNSCache does not find a cache hit for the DNS query, the kube-dns Service is used to request CoreDNS to handle the query.|
|3|CoreDNS uses the DNS server deployed in the virtual private cloud \(VPC\) to resolve domain names that are not cluster-local.|
|4|When the pod with the local DNSConfig injected fails to connect to NodeLocal DNSCache, the pod uses the kube-dns Service to connect to CoreDNS for DNS resolution.|
|5|A pod without the local DNSConfig injected uses the kube-dns Service to connect to CoreDNS for DNS resolution.|

## Install NodeLocal DNSCache

You can install NodeLocal DNSCache on the Add-ons page or the App Catalog page. We recommend that you install NodeLocal DNSCache on the Add-ons page.

**Install NodeLocal DNSCache on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  On the Add-ons page, click the **Networking** tab and find ACK NodeLocal DNSCache.

5.  Click **Install**. In the message that appears, click **OK**.


**Install NodeLocal DNSCache on the App Catalog page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, search for ack-node-local-dns. In the search result, click **ack-node-local-dns**.

4.  On the **Description** tab, read the introduction and parameter settings about **ack-node-local-dns**. Then, click the **Parameters** tab to set the parameters.

    The following table describes the parameters.

    |Parameter|Description|How to obtain the value|
    |---------|-----------|-----------------------|
    |upstream\_ip|The cluster IP of the kube-dns Service in the kube-system namespace. NodeLocal DNSCache uses the kube-dns Service to communicate with CoreDNS and resolve cluster-local domain names.|You can modify the parameter value to specify other upstream DNS servers.|
    |clusterDomain|The domain name of the cluster.|Query the --cluster-domain parameter of the kubelet process on a cluster node. The default value of the parameter is cluster.local.|

5.  In the **Deploy** section on the right side of the page, select the cluster and namespace, specify a release name, and then click **Create**.


## Configure NodeLocal DNSCache for an application

**Note:** By default, NodeLocal DNSCache is not installed on master nodes. If you want to install NodeLocal DNSCache on master nodes and taints are added to master nodes, you must modify taints and tolerations for the node-local-dns DaemonSet in the kube-system namespace.

To forward DNS requests that are previously directed to CoreDNS to the DaemonSet that runs a DNS cache, you must set the nameservers parameter of pod configurations to 169.254.20.10 and the IP address of kube-dns. To do this, use one of the following methods:

-   Method 1: Use the admission controller to automatically inject DNSConfig when pods are created. We recommend that you use this method.
-   Method 2: Manually specify DNSConfig when pods are created.
-   Method 3: Modify kubelet parameters and restart kubelet. We recommend that you do not use this method because your business may be interrupted.

**Method 1: Automatically inject DNSConfig**

You can use an admission controller to automatically inject DNSConfig to newly created pods. This way, you do not need to manually configure the pod configuration file. By default, the application listens for requests to create pods from namespaces that have the `node-local-dns-injection=enabled` label. You can use the following command to add this label to a namespace.

```
kubectl label namespace default node-local-dns-injection=enabled
```

**Note:**

-   The preceding command enables automatic DNSConfig injection only for the default namespace. To enable automatic DNSConfig injection for other namespaces, replace `default` with the namespace based on your requirements.
-   If automatic DNSConfig injection is enabled for a namespace but you do not want to automatically inject DNSConfig to a specified pod, you can add the `node-local-dns-injection=disabled` label to the pod template.

After automatic DNSConfig injection is enabled, the following parameters are added to new pods. To ensure the high availability of DNS services, the cluster IP address of kube-dns is added to the nameservers parameter as a backup DNS server.

```
dnsConfig:
    nameservers:
    - 169.254.20.10
    - 172.21.0.10
    options:
    - name: ndots
      value: "3"
    - name: attempts
      value: "2"
    - name: timeout
      value: "1"
    searches:
    - default.svc.cluster.local
    - svc.cluster.local
    - cluster.local
  dnsPolicy: None
```

To enable automatic DNSConfig injection, the following conditions must be met. If DNSConfig is not automatically injected, check whether the conditions are met:

-   New pods do not belong to the kube-system or kube-public namespace.
-   The namespace to which new pods belong has the `node-local-dns-injection=enabled` label.
-   The namespace to which new pods belong does not have labels that are related to Elastic Container Instance-based pods, such as `virtual-node-affinity-injection`, `eci`, and `alibabacloud.com/eci`.
-   New pods do not have labels that are related to elastic container instances, such as `eci` and `alibabacloud.com/eci`, or the `node-local-dns-injection=disabled` label.
-   New pods use the `hostNetwork` network and the `ClusterFirstWithHostNet` DNSPolicy, or the pods do not use the `hostNetwork` network but use the `ClusterFirst` DNSPolicy.

**Method 2: Manually specify DNSConfig**

If you do not want to use admission webhooks to automatically inject DNSConfig, you can modify pod configurations to manually specify DNSConfig.

```
apiVersion: v1
kind: Pod
metadata:
  name: alpine
  namespace: default
spec:
  containers:
  - image: alpine
    command:
      - sleep
      - "10000"
    imagePullPolicy: Always
    name: alpine
  dnsPolicy: None
  dnsConfig:
    nameservers: ["169.254.20.10","172.21.0.10"]
    searches:
    - default.svc.cluster.local
    - svc.cluster.local
    - cluster.local
    options:
    - name: ndots
      value: "3"
    - name: attempts
      value: "2"
    - name: timeout 
      value: "1"
```

-   dnsPolicy: Set the value to `None`.
-   nameservers: Set the value to 169.254.20.10 and the cluster IP address of kube-dns.
-   searches: Set the DNS search domains. Make sure that internal domains can be resolved.
-   ndots: You can improve resolution efficiency by setting this parameter to a smaller value. Default value: 5. For more information, see [resolv.conf](https://linux.die.net/man/5/resolv.conf).

**Method 3: Configure kubelet startup parameters**

The kubelet uses the --cluster-dns and --cluster-domain parameters to control pod DNSConfig. In the /etc/systemd/system/kubelet.service.d/10-kubeadm.conf file, add the --cluster-dns parameter and set the value to the local IP address 169.254.20.10. Then, run the `systemctl daemon-reload` and `systemctl restart kubelet` commands for the changes to take effect.

```
--cluster-dns=169.254.20.10 --cluster-dns=<kube-dns ip> --cluster-domain=<search domain>
```

-   cluster-dns: specifies the DNS servers that are used in the pod configuration. By default, only the IP address of ``kube-dns`` is specified. You must add the local IP address 169.254.20.10.
-   cluster-domain: specifies the DNS search domains that are used in the pod configuration. You can use the existing domain. In most cases, the existing domain is ``cluster.local``.

**Example on how to enable NodeLocal DNSCache for an application**

The following example shows how to enable NodeLocal DNSCache for a Deployment that is created in the default namespace.

1.  Run the following command to add a label to the namespace to which the Deployment belongs. In this example, the Deployment is created in the default namespace.

    ```
    kubectl label namespace default node-local-dns-injection=enabled
    ```

    **Note:** The admission controller ignores applications in the kube-system and kube-public namespaces. Do not configure to automatically inject dnsConfig to applications in the two namespaces.

2.  Deploy a sample application in the default namespace.

    1.  Use the following YAML template to create a sample application named ubuntu-deployment:

        ```
        apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
        kind: Deployment
        metadata:
          name: ubuntu
          labels:
            app: ubuntu
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: ubuntu
          template:
            metadata:
              labels:
                app: ubuntu
            spec:
              containers:
              - name: ubuntu
                image: ubuntu
                command: ["sh", "-c"]
                args: ["sleep 100000"]
        ```

    2.  Run the following command to deploy the application in the cluster:

        ```
        kubectl apply -f ubuntu-deployment.yaml
        ```

        Expected output:

        ```
        deployment.apps/ubuntu created
        ```

    3.  Run the following command to view the application information:

        ```
        kubectl get deployment ubuntu
        ```

        Expected output:

        ```
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        ubuntu   2/2     2            2           7s
        ```

3.  Check whether dnsConfig is injected.

    1.  Run the following command to view the pods that are provisioned for the application:

        ```
        kubectl get pods
        ```

        Expected output:

        ```
        NAME                      READY   STATUS    RESTARTS   AGE
        ubuntu-766448f68c-mj8qk   1/1     Running   0          4m39s
        ubuntu-766448f68c-wf5hw   1/1     Running   0          4m39s
        ```

    2.  Run the following command to check whether NodeLocal DNSCache is enabled in dnsConfig of a pod:

        ```
        kubectl get pod ubuntu-766448f68c-mj8qk -o=jsonpath='{.spec.dnsConfig}'
        ```

        Expected output:

        ```
        map[nameservers:[169.254.20.10 172.21.0.10] options:[map[name:ndots value:5]] searches:[default.svc.cluster.local svc.cluster.local cluster.local]]
        ```

        The preceding output indicates that NodeLocal DNSCache is enabled for the application.

        After NodeLocal DNSCache is enabled for an application, the following parameters are added to the pods that are provisioned for the application. To ensure high availability of DNS services, the cluster IP address 172.21.0.10 of the kube-dns Service is specified as the IP address of the backup DNS server in the nameservers parameter.

        ```
          dnsConfig:
            nameservers:
            - 169.254.20.10
            - 172.21.0.10
            options:
            - name: ndots
              value: "3"
            - name: attempts
              value: "2"
            - name: timeout
              value: "1"
            searches:
            - default.svc.cluster.local
            - svc.cluster.local
            - cluster.local
          dnsPolicy: None
        ```


## Upgrade NodeLocal DNSCache

If you have installed NodeLocal DNSCache on the Add-ons page, you must upgrade NodeLocal DNSCache on the same page. If you have installed NodeLocal DNSCache on the App Catalog page, you must uninstall the current version of NodeLocal DNSCache on the Helm chart page and install the latest version.

**Upgrade NodeLocal DNSCache on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, find NodeLocal DNSCache and click **Upgrade**. In the message that appears, click **OK**.

    **Note:** If you modified taints and tolerations for the node-local-dns DaemonSet, the modifications are overwritten during the upgrade process. You must configure taints and tolerations again after the upgrade is completed.


**Upgrade NodeLocal DNSCache on the App Catalog page**

If you have installed NodeLocal DNSCache on the App Catalog page, you must uninstall the current version of NodeLocal DNSCache and install the latest version. For more information, see [Uninstall NodeLocal DNSCache](#step_9sz_4y8_vju) and [Install NodeLocal DNSCache](#section_rzr_wqx_ss3).

## Uninstall NodeLocal DNSCache

If you have installed NodeLocal DNSCache on the Add-ons page, you must uninstall NodeLocal DNSCache on the Add-ons page. If you have installed NodeLocal DNSCache on the App Catalog page, you must uninstall NodeLocal DNSCache on the Helm page.

**Uninstall NodeLocal DNSCache on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, find NodeLocal DNSCache and click **Uninstall**. In the message that appears, click **OK**.

    **Note:** After NodeLocal DNSCache is uninstalled, all DNS queries are sent to CoreDNS. We recommend that you add replicas for CoreDNS before you uninstall NodeLocal DNSCache.


**Uninstall NodeLocal DNSCache on the App Catalog page**

1.  Check the parameter settings.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**.

    5.  On the Helm page, find and click **ack-node-local-dns-default**.

    6.  Click the **Parameters** tab and check the parameter settings.

        -   If the local\_dns\_ip parameter is set to the cluster IP address of kube-dns, proceed to [Step 2](#step_9sz_4y8_vju) to uninstall NodeLocal DNSCache.
        -   If the high\_availability parameter is set to enabled and the local\_dns\_ip parameter is set to 169.254.20.10, proceed to [Step 2](#step_9sz_4y8_vju) to uninstall NodeLocal DNSCache.
        -   If the parameters do not meet the preceding conditions, perform the following steps to uninstall NodeLocal DNSCache:
            1.  Run the `kubectl get ns -o yaml` command to find the namespaces that have the node-local-dns-injection=enabled label. Remove the label from the namespaces.
            2.  Run the `kubectl get pod -o yaml` command to find the pods to which DNSConfig is injected. Delete the pods and create new pods.
            3.  Perform [Step 2](#step_9sz_4y8_vju) to uninstall NodeLocal DNSCache.
2.  Uninstall NodeLocal DNSCache.

    1.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**.

    5.  On the Helm page, select ack-node-local-dns-default and click **Delete** in the **Actions** column. In the message that appears, click **OK**.


## Compatibility with PrivateZone

By default, NodeLocal DNSCache uses the TCP protocol to communicate with CoreDNS. CoreDNS communicates with the upstream servers based on the protocol used by the source of DNS queries. If PrivateZone is used in your cluster, DNS queries processed by NodeLocal DNSCache are sent to PrivateZone over TCP. PrivateZone does not support TCP in some regions. Therefore, errors may occur when DNS queries are processed by using PrivateZone.

## Modify the ConfigMap of Terway

1.  Run the following command to view the ConfigMap of Terway:

    ```
    kubectl -n kube-system edit cm eni-config -o yaml
    ```

2.  Check the ConfigMap of Terway.

    -   Check whether IPVLAN is enabled in the `eniip_virtual_type` field. If this field does not exist in the ConfigMap or the value is not set to IPVLAN, you do not need to perform the following steps before you can install NodeLocal DNSCache. For more information, see [Install NodeLocal DNSCache](#section_rzr_wqx_ss3).
    -   Check whether the `host_stack_cidrs` field of the ConfigMap is specified. If the `host_stack_cidrs` field is specified, you do not need to perform the following steps before you can install NodeLocal DNSCache. For more information, see [Install NodeLocal DNSCache](#section_rzr_wqx_ss3).
3.  Add the `host_stack_cidrs` field to the ConfigMap of Terway and set the value to 169.254.20.10/32. Then, save the ConfigMap and exist.

    ```
     10-terway.conf: |
      {
        "cniVersion": "0.3.0",
        "name": "terway",
        "eniip_virtual_type": "IPVlan",
        "host_stack_cidrs": ["169.254.20.10/32"], 
        "type": "terway"
      }
    ```

4.  Run the following command to query all of the DaemonSet pods for Terway:

    ```
    kubectl -n kube-system get pod | grep terway-eniip
    ```

    Expected output:

    ```
    terway-eniip-7d56l         2/2     Running   0          30m
    terway-eniip-s7m2t         2/2     Running   0          30m
    ```

5.  Run the following command to recreate the Terway pods:

    ```
     kubectl -n kube-system delete pod terway-eniip-7d56l terway-eniip-s7m2t
    ```

6.  Log on to a node of the cluster and run the following command to query the ConfigMap of Terway.

    If the ConfigMap contains the field that you have added, it indicates that the ConfigMap of Terway is modified.

    ```
    cat /etc/cni/net.d/*
    ```

    Expected output:

    ```
     {
       "cniVersion": "0.3.0",
       "name": "terway-chainer",
       "plugins": [
         {
           "eniip_virtual_type": "IPVlan",
           "host_stack_cidrs": [ 
             "169.254.20.10/32",
           ],
           "type": "terway"
         },
         {
           "type": "cilium-cni"
         }
       ]
     }
    ```

    After all the Terway pods are recreated and run as normal, you can install NodeLocal DNSCache.


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)

[Optimize DNS resolution for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Optimize DNS resolution for an ACK cluster.md)

