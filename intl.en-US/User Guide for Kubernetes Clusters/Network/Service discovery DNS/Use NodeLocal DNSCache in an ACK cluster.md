---
keyword: [node-local-dns, NodeLocal DNSCache, DaemonSet]
---

# Use NodeLocal DNSCache in an ACK cluster

Container Service for Kubernetes \(ACK\) allows you to deploy NodeLocal DNSCache to improve the stability and performance of service discovery. NodeLocal DNSCache is implemented as a DaemonSet and runs a DNS caching agent on cluster nodes to improve the DNS performance. This topic describes how to deploy and configure NodeLocal DNSCache for an application in an ACK cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Limits

-   NodeLocal DNSCache does not support serverless Kubernetes \(ASK\) clusters or pods that run on elastic container instances in managed or dedicated ACK clusters.
-   The Terway version must be v1.0.10.301 or later.
-   To install NodeLocal DNSCache, you can go to the Add-ons page or install ack-node-local-dns on the App Catalog page.
-   NodeLocal DNSCache serves as a transparent cache proxy for CoreDNS and does not provide features such as Hosts or Rewrite. If you want to enable the features, modify CoreDNS configurations.
-   If PrivateZone is used in the cluster, you must modify CoreDNS configurations before you enable PrivateZone. For more information, see [Compatibility with PrivateZone](#section_ajk_4vf_oqk).

## Introduction

ACK NodeLocal DNSCache is a local DNS cache solution developed based on the open source NodeLocal DNSCache project. ACK NodeLocal DNSCache is the ack-node-local-dns chart in Helm. This solution consists of a DNS cache that runs as a DaemonSet and an admission controller that runs as a Deployment to dynamically inject DNSConfig.

-   The admission controller intercepts pod creation requests based on admission webhooks and dynamically injects cached DNSConfig to pod configurations.

    **Note:** If you do not enable the admission controller to automatically inject DNSConfig, you must manually add DNS settings to pod configurations. For more information, see Method 2: Manually specify DNSConfig in [Configure NodeLocal DNSCache for an application](#section_kf0_5uj_ay2).

-   The DaemonSets that run DNS caches can create a virtual network interface on each node. By default, the virtual network interface uses the IP address 169.254.20.10 to listen on requests. To change the IP address, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). DNS requests that are generated in pods are proxied by using the DaemonSets that run DNS caches. The requests are proxied based on pod DNSConfig and node network settings.

    **Note:** The DaemonSets that run DNS caches are built on CoreDNS and provide only proxy and cache services. Do not enable other features such as Hosts and Rewrite on the DaemonSets.


![How it works](../images/p266014.png "How it works")

|No.|Description|
|---|-----------|
|1|By default, the pods to which the DNS local cache is injected use NodeLocal DNSCache to listen on query requests by using the IP address 169.254.20.10 on the node.|
|2|If NodeLocal DNSCache has no cache that responds to a query request, the kube-dns service is used to request CoreDNS for resolution.|
|3|CoreDNS uses the DNS server deployed in the virtual private cloud \(VPC\) to resolve non-cluster domain names.|
|4|When the pods to which the DNS local cache is injected fail to connect to NodeLocal DNSCache, these pods use the kube-dns service to connect to CoreDNS for resolution as a backup solution.|
|5|Pods to which the DNS local cache is not injected use the kube-dns service to connect to CoreDNS for resolution.|

## Install NodeLocal DNSCache

You can install NodeLocal DNSCache on the Add-ons page or the App Catalog page. We recommend that you install NodeLocal DNSCache on the Add-ons page.

**Install NodeLocal DNSCache on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  On the Add-ons page, click the **Networking** tab and find ACK NodeLocal DNSCache.

5.  Click **Install**. In the dialog box that appears, click **OK**.


**Install NodeLocal DNSCache on the App Catalog page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, search for ack-node-local-dns. In the search result, click **ack-node-local-dns**.

4.  On the **Description** tab, read the introduction and parameter settings about **ack-node-local-dns**. Then, click the **Parameters** tab to set the parameters.

    The following table describes the parameters.

    |Parameter|Description|How to obtain the value|
    |---------|-----------|-----------------------|
    |upstream\_ip|The cluster IP of the kube-dns service in the kube-system namespace. NodeLocal DNSCache uses the kube-dns service to communicate with CoreDNS and resolve cluster-local domain names.|You can modify the parameter value to specify other upstream DNS servers.|
    |clusterDomain|The domain name of the cluster.|Query the --cluster-domain parameter of the kubelet process on a cluster node. The default value of the parameter is cluster.local.|

5.  In the **Deploy** panel on the right side of the page, select the cluster and namespace, specify a release name, and then click **Create**.


## Configure NodeLocal DNSCache for an application

**Note:** By default, NodeLocal DNSCache is not installed on master nodes. If you want to install NodeLocal DNSCache on master nodes and taints are configured on master nodes, you must modify taints and tolerations for the node-local-dns DaemonSet in the kube-system namespace.

To forward DNS requests that are previously directed to CoreDNS to the DaemonSet that runs DNS cache, you must set the nameservers parameter of pod configurations to 169.254.20.10 and the IP address of kube-dns. To do this, use one of the following methods:

-   Method 1: Use the admission controller to automatically inject DNSConfig when pods are created. We recommend that you use this method.
-   Method 2: Manually specify DNSConfig when pods are created.
-   Method 3: Modify kubelet parameters and restart kubelet. We recommend that you do not use this method because your application may be interrupted.

**Method 1: Automatically inject DNSConfig**

You can use an admission controller to automatically inject DNSConfig to newly created pods. This way, you do not need to manually configure the pod configuration file. By default, the application listens on requests to create pods from namespaces that have the `node-local-dns-injection=enabled` label. You can use the following command to add this label to a namespace.

**Note:** If DNSConfig auto injection is enabled on a namespace but you do not want to automatically inject DNSConfig to a pod, you can add the `node-local-dns-injection=disabled` label to the pod template.

```
kubectl label namespace default node-local-dns-injection=enabled
```

After DNSConfig auto injection is enabled, the following parameters are added to new pods. To ensure the high availability of DNS services, the cluster IP address of kube-dns is added to the nameservers parameter as a backup server.

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

To enable DNSConfig auto injection, the following conditions must be met. If DNSConfig is not automatically injected, check whether the conditions are met.

-   New pods do not belong to the kube-system or kube-public namespace.
-   The namespace to which new pods belong has the `node-local-dns-injection=enabled` label.
-   The namespace to which new pods belong does not have labels that are related to Elastic Container Instance \(ECI\), such as `virtual-node-affinity-injection`, `eci`, and `alibabacloud.com/eci`.
-   New pods do not have labels that are related to ECI, such as `eci` and `alibabacloud.com/eci`, or the `node-local-dns-injection=disabled` label.
-   New pods have the `hostNetwork` network and the `ClusterFirstWithHostNet` DNSPolicy, or the pods do not have the `hostNetwork` network but have the `ClusterFirst` DNSPolicy.

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
-   ndots: Default value: 5. You can moderately reduce the value to improve resolution efficiency. For more information, see [resolv.conf](https://linux.die.net/man/5/resolv.conf).

**Method 3: Configure kubelet startup parameters**

The kubelet uses the --cluster-dns and --cluster-domain parameters to control pod DNSConfig. In the /etc/systemd/system/kubelet.service.d/10-kubeadm.conf file, add the --cluster-dns parameter and set the value to the local IP address 169.254.20.10. Then, run the `systemctl daemon-reload` and `systemctl restart kubelet` commands for the changes to take effect.

```
--cluster-dns=169.254.20.10 --cluster-dns=<kube-dns ip> --cluster-domain=<search domain>
```

-   cluster-dns: Specifies the DNS servers that are used in the pod configuration. By default, only the IP address of ``kube-dns`` is specified. You must add the local IP address 169.254.20.10.
-   cluster-domain: Specifies the DNS search domains that are used in the pod configuration. You can use the existing domain. In most cases, is ``cluster.local``.

**Example on how to enable NodeLocal DNSCache for an application**

The following example shows how to enable NodeLocal DNSCache for a Deployment that is created in the default namespace.

1.  Run the following command to add a label to the namespace to which the Deployment belongs. In this example, the Deployment is created in the default namespace.

    ```
    kubectl label namespace default node-local-dns-injection=enabled
    ```

    **Note:** The admission controller ignores applications in the kube-system and kube-public namespaces. Do not configure to automatically inject dnsConfig to applications in the two namespaces.

2.  Deploy a sample application in the default namespace.

    1.  Use the following YAML template to create a sample application named ubuntu-deployment.

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

    2.  Run the following command to check whether NodeLocal DNSCache is enabled in dnsConfig of a pod.

        ```
        kubectl get pod ubuntu-766448f68c-mj8qk -o=jsonpath='{.spec.dnsConfig}'
        ```

        Expected output:

        ```
        map[nameservers:[169.254.20.10 172.21.0.10] options:[map[name:ndots value:5]] searches:[default.svc.cluster.local svc.cluster.local cluster.local]]
        ```

        The preceding output indicates that NodeLocal DNSCache is enabled for the application.

        After NodeLocal DNSCache is enabled for an application, the following parameters are added to the pods that are provisioned for the application. To ensure high availability of DNS services, the cluster IP address 172.21.0.10 of the kube-dns service is added to the nameservers parameter. This way, the kube-dns service is configured as a backup DNS service.

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

If you installed NodeLocal DNSCache on the Add-ons page, you must upgrade NodeLocal DNSCache on the Add-ons page. If you installed NodeLocal DNSCache on the App Catalog page, you must uninstall the old version of NodeLocal DNSCache on the Helm chart page and install the new version.

**Upgrade NodeLocal DNSCache on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, find NodeLocal DNSCache and click **Upgrade**. In the dialog box that appears, click **OK**.

    **Note:** If you modified taints and tolerations for the node-local-dns DaemonSet, the modifications are overwritten during the upgrade. You must configure taints and tolerations again after the upgrade.


**Upgrade NodeLocal DNSCache on the App Catalog page**

If you installed NodeLocal DNSCache on the App Catalog page, you must uninstall the old version of NodeLocal DNSCache and install the new version. For more information, see [Uninstall NodeLocal DNSCache](#step_9sz_4y8_vju) and [Install NodeLocal DNSCache](#section_rzr_wqx_ss3).

## Uninstall NodeLocal DNSCache

If you installed NodeLocal DNSCache on the Add-ons page, you must uninstall NodeLocal DNSCache on the Add-ons page. If you installed NodeLocal DNSCache on the App Catalog page, you must uninstall NodeLocal DNSCache on the Helm chart page.

**Uninstall NodeLocal DNSCache on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, find NodeLocal DNSCache and click **Uninstall**. In the dialog box that appears, click **OK**.

    **Note:** After NodeLocal DNSCache is uninstalled, all DNS query requests are sent to CoreDNS. We recommend that you add replicas for CoreDNS before you uninstall NodeLocal DNSCache.


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

    5.  On the Helm page, select ack-node-local-dns-default and click **Delete** in the **Actions** column. In the dialog box that appears, click **OK**.


## Compatibility with PrivateZone

By default, NodeLocal DNSCache uses the TCP protocol to communicate with CoreDNS. CoreDNS communicates with the upstream servers by using the protocol that is used by the query source. If PrivateZone is used in your cluster, query requests processed by NodeLocal DNSCache are sent to PrivateZone by using the TCP protocol. PrivateZone does not support the TCP protocol in some regions. Therefore, errors may occur when query requests are processed by using PrivateZone.

We recommend that you modify the configuration file of CoreDNS based on the following modifications. The configuration file is named as **coredns** in the kube-system namespace. For more information, see [t1994954.md\#](). Modify the setting of the forward plug-in and set the protocol that is used to request upstream servers to ``perfer_udp``. This way, CoreDNS preferably uses the UDP protocol to communicate with upstream servers. You can modify the setting based on the following modifications:

```
# The original setting
forward . /etc/resolv.conf
# The modified setting
forward . /etc/resolv.conf {
  prefer_udp
}
```

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)

[Optimize DNS resolution for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Optimize DNS resolution for an ACK cluster.md)

