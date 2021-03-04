---
keyword: [CoreDNS, ACK, DNS]
---

# Optimize DNS resolution for an ACK cluster

CoreDNS is a Domain Name System \(DNS\) resolver for Kubernetes clusters. CoreDNS resolves custom domain names from inside or outside Kubernetes clusters. CoreDNS contains a variety of add-ons. These add-ons allow you to configure custom DNS settings, and customize hosts, Canonical Name \(CNAME\) records, and rewrite rules for Kubernetes clusters. Clusters of Container Service for Kubernetes \(ACK\) require high queries per second \(QPS\) for DNS resolution. DNS queries may fail if the QPS cannot match the heavy load. This topic describes how to optimize DNS resolution for an ACK cluster.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

For more information about CoreDNS, see [CoreDNS](https://coredns.io/).

## Adjust the number of CoreDNS pods properly

Setting a proper ratio of CoreDNS pods to nodes in a Kubernetes cluster improves the performance of service discovery for the cluster. We recommend that you set the ratio to 1:8.

-   If you do not want to expand your cluster on a large scale, you can run the following command to change the number of pods:

    ```
    kubectl scale --replicas={target} deployment/coredns -n kube-system
    ```

    **Note:** Replace `{target}` with the required value.

-   If you want to expand your cluster on a large scale, you can use the following YAML template to create a Deployment. Then, you can use [cluster-proportional-autoscaler](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler) to dynamically scale the number of pods. We recommend that you do not use Horizontal Pod Autoscaler \(HPA\) to scale the number of pods when the QPS, CPU usage, or memory usage of pods reaches the threshold. HPA cannot meet the expected requirements.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: dns-autoscaler
      namespace: kube-system
      labels:
        k8s-app: dns-autoscaler
    spec:
      selector:
        matchLabels:
           k8s-app: dns-autoscaler
      template:
        metadata:
          labels:
            k8s-app: dns-autoscaler
    
        spec:
          serviceAccountName: admin
          containers:
          - name: autoscaler
            image: registry.cn-hangzhou.aliyuncs.com/ringtail/cluster-proportional-autoscaler-amd64:v1.3.0
            resources:
                requests:
                    cpu: "200m"
                    memory: "150Mi"
            command:
              - /cluster-proportional-autoscaler
              - --namespace=kube-system
              - --configmap=dns-autoscaler
              - --target=Deployment/coredns
              - --default-params={"linear":{"coresPerReplica":64,"nodesPerReplica":8,"min":2,"max":100,"preventSinglePointFailure":true}}
              - --logtostderr=true
              - --v=2
    ```

    **Note:** In the preceding linear scaling policy, the number of replicas for CoreDNS is calculated based on the following formula: Replicas = Max \(Ceil \(Cores × 1/coresPerReplica\), Ceil \(Nodes × 1/nodesPerReplica\) \). The number of CoreDNS pod replicas is subject to the values of `max` and `min` in the linear scaling policy.

    The following code block shows the parameters of the linear scaling policy:

    ```
    {
          "coresPerReplica": 64,
          "nodesPerReplica": 8,
          "min": 2,
          "max": 100,
          "preventSinglePointFailure": true
    }
    ```


## Use the autopath add-on

Before you use autopath, you must familiarize yourself with the four DNS policies for pods:

-   **Default**: This policy indicates that a pod inherits the DNS resolution settings from the node where the pod is deployed. In an ACK cluster, nodes are created based on Elastic Compute Service \(ECS\) instances. Therefore, a pod directly uses the /etc/resolv.conf file of the ECS instance-based node where the pod is deployed. This file contains the address of a DNS server that is provided by Alibaba Cloud DNS.
-   **ClusterFirst**: This policy indicates that a pod uses CoreDNS to resolve domain names from inside or outside of the cluster. The /etc/resolv.conf file contains the address of the DNS server that is provided by CoreDNS, which is kube-dns.

    In ClusterFirst mode, an external domain name is resolved twice: one for an IPv4 address and one for an IPv6 address. Eight queries \(four for an IPv4 address and four for an IPv6 address\) are generated in total. For example, the cluster base domain www.aliyun.com has three suffixes configured. When this domain is resolved, six queries are generated in total for the three suffixes. Invalid results are returned for all these queries. This increases the QPS of DNS resolution by three times and adversely affects the resolution performance.

    ```
    2020-06-04T20:46:35.643+08:00 [INFO] 172.20.0.100:54153 - 63230 "AAAA IN www.aliyun.com.kube-system.svc.cluster.local. udp 62 false 512" NXDOMAIN qr,aa,rd 155 0.000282418s
    2020-06-04T20:46:35.644+08:00 [INFO] 172.20.0.100:54153 - 62219 "A IN www.aliyun.com.kube-system.svc.cluster.local. udp 62 false 512" NXDOMAIN qr,aa,rd 155 0.000387552s
    2020-06-04T20:46:35.644+08:00 [INFO] 172.20.0.100:33409 - 11389 "AAAA IN www.aliyun.com.svc.cluster.local. udp 50 false 512" NXDOMAIN qr,aa,rd 143 0.000264026s
    2020-06-04T20:46:35.644+08:00 [INFO] 172.20.0.100:33409 - 10963 "A IN www.aliyun.com.svc.cluster.local. udp 50 false 512" NXDOMAIN qr,aa,rd 143 0.000334383s
    2020-06-04T20:46:35.645+08:00 [INFO] 172.20.0.100:57153 - 2741 "AAAA IN www.aliyun.com.cluster.local. udp 46 false 512" NXDOMAIN qr,aa,rd 139 0.000200375s
    2020-06-04T20:46:35.645+08:00 [INFO] 172.20.0.100:57153 - 2435 "A IN www.aliyun.com.cluster.local. udp 46 false 512" NXDOMAIN qr,aa,rd 139 0.000329507s
    2020-06-04T20:46:35.646+08:00 [INFO] 172.20.0.100:48284 - 31225 "A IN www.aliyun.com. udp 32 false 512" NOERROR qr,aa,rd,ra 476 0.00070823s
    2020-06-04T20:46:35.646+08:00 [INFO] 172.20.0.100:48284 - 31851 "AAAA IN www.aliyun.com. udp 32 false 512" NOERROR qr,aa,rd,ra 498 0.000925332s
    ```

-   **ClusterFirstWithHostNetwork**: This policy indicates that a pod in the host network uses the ClusterFirst policy. If you do not specify a policy for a pod, the pod uses the Default policy.
-   **None**: This policy indicates that a pod ignores the DNS settings of the cluster. You must customize the DNS setting by setting the dnsConfig field.

If the first resolution attempt fails, autopath splits the suffixes to make another attempt. The resolution is complete within two attempts \(one for an IPv4 address and one for an IPv6 address\).

**Note:**

-   For Kubernetes 1.16 and later, autopath is automatically enabled when you create a Kubernetes cluster. Earlier Kubernetes versions do not support this feature.
-   The autopath add-on reduces the CPU usage of the CoreDNS pods, but increases the memory usage of the pods. In a 1000-node and 10000-pod cluster, the CoreDNS pods consume less than 100 MiB of memory.

**Use autopath**

Reference the following example and modify the kube-system/configmap coredns file by changing the content in the red boxes.

![autopath](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6545359951/p129419.png)

## Use NodeLocal DNSCache in a Kubernetes cluster

NodeLocal DNSCache is deployed as a DaemonSet and runs a pod on each node of a Kubernetes cluster to process DNS queries. Queries of internal domain names are sent to CoreDNS and queries of external domain names are sent to external DNS servers. NodeLocal DNSCache also caches all DNS queries. NodeLocal DNSCache is an efficient DNS cache based on nodes, which can significantly improve the performance of domain name resolution for the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  In the upper-right corner of the App Catalog page, enter ack-node-local-dns in the **Name** search bar and click the search icon to search for NodeLocal DNSCache.

4.  Click **ack-node-local-dns** and specify the parameters on the **Parameters** tab.

    |Parameter|Description|Default|
    |---------|-----------|-------|
    |upStreamIP|The IP address of a Service that uses the CoreDNS pod as the backend. NodeLocal DNSCache requests DNS resolution services from CoreDNS for domain names inside the cluster by sending DNS queries to this IP address. The value of this parameter must be an unused Service IP address. This parameter is required.|NULL|
    |kubeDnsIP|This parameter is required.The IP address of the virtual interface. NodeLocal DNSCache listens to DNS queries sent to the virtual interface IP address. This IP address determines how the CoreDNS pod is accessed. For more information, see [README](https://cs.console.aliyun.com/?spm=5176.181001.1396228.btn1cs.344a60dfTM90ou#/k8s/catalog/detail/incubator_ack-node-local-dns).|"169.254.20.10"|
    |clusterDomain|The base domain of the cluster.|"cluster.local"|
    |force\_tcp|Specifies whether the upstream DNS server must use TCP for communication.|"false"|

5.  In the Deploy section, select a cluster to deploy the application and click **Create**.

    The namespace is automatically specified as **kube-system**, and the release name is automatically specified as **ack-node-local-dns**.

6.  Run the following command to view the deployment status of **node-local-dns**:

    ```
    kubectl get daemonSet node-local-dns  -n kube-system
    ```

    ```
    NAME             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    node-local-dns   1         1         1       1            1           beta.Kubernetes.io/os=linux   43d
    ```

    ```
    kubectl get pods  -n kube-system -l k8s-app=node-local-dns
    ```

    ```
    NAME                   READY   STATUS    RESTARTS   AGE
    node-local-dns-b8x4l   1/1     Running   1          43d
    node-local-dns-sf85d   1/1     Running   0          43d
    node-local-dns-wf547   1/1     Running   4          43d
    ```

    If the node-local-dns parameter appears in the result and the pods are in the Running state, **node-local-dns** is deployed.


