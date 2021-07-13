---
keyword: [CoreDNS, ACK, DNS]
---

# Optimize DNS resolution for an ACK cluster

CoreDNS is a Domain Name System \(DNS\) resolver for Kubernetes clusters. CoreDNS resolves custom domain names from inside or outside Kubernetes clusters. CoreDNS contains a variety of add-ons. These add-ons allow you to configure custom DNS settings and customize hosts, Canonical Name \(CNAME\) records, and rewrite rules for Kubernetes clusters. Container Service for Kubernetes \(ACK\) clusters require high queries per second \(QPS\) for DNS resolution. DNS queries may fail if the QPS cannot match the heavy load. This topic describes how to optimize DNS resolution for an ACK cluster.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md)

For more information about CoreDNS, see [CoreDNS](https://coredns.io/).

## Adjust the number of CoreDNS pods

Setting a proper ratio of CoreDNS pods to nodes in a Kubernetes cluster improves the performance of service discovery for the cluster. We recommend that you set the ratio to 1:8.

-   If you do not want to expand your cluster on a large scale, you can run the following command to change the number of pods:

    ```
    kubectl scale --replicas={target} deployment/coredns -n kube-system
    ```

    **Note:** Replace `{target}` with the required value.

-   If you want to expand your cluster on a large scale, you can use the following YAML template to create a Deployment. Then, you can use [cluster-proportional-autoscaler](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler) to dynamically scale the number of pods. We recommend that you do not use Horizontal Pod Autoscaler \(HPA\) to scale the number of pods when the QPS, CPU utilization, or memory usage of pods reaches the threshold. HPA cannot meet the expected requirements.

    ```
    ---
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
            image: registry.cn-hangzhou.aliyuncs.com/acs/cluster-proportional-autoscaler:1.8.4
            resources:
              requests:
                cpu: "200m"
                memory: "150Mi"
            command:
            - /cluster-proportional-autoscaler
            - --namespace=kube-system
            - --configmap=dns-autoscaler
            - --nodelabels=type!=virtual-kubelet
            - --target=Deployment/coredns
            - --default-params={"linear":{"coresPerReplica":64,"nodesPerReplica":8,"min":2,"max":100,"preventSinglePointFailure":true}}
            - --logtostderr=true
            - --v=9
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


## Use NodeLocal DNSCache in a Kubernetes cluster

ACK allows you to deploy NodeLocal DNSCache to improve the stability and performance of service discovery. NodeLocal DNSCache is implemented as a DaemonSet and runs a DNS caching agent on cluster nodes to improve the DNS performance.

For more information about NodeLocal DNSCache and how to deploy NodeLocal DNSCache in ACK clusters, see [Use NodeLocal DNSCache in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Use NodeLocal DNSCache in an ACK cluster.md)

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)

[Use NodeLocal DNSCache in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Use NodeLocal DNSCache in an ACK cluster.md)

