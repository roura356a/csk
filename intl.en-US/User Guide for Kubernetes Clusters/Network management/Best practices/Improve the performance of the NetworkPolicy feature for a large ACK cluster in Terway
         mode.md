---
keyword: [Terway, NetworkPolicy, large-scale]
---

# Improve the performance of the NetworkPolicy feature for a large ACK cluster in Terway mode

In a Container Service for Kubernetes \(ACK\) cluster that has the network plug-in Terway installed, you can use the NetworkPolicy feature to control communication among pods. When an ACK cluster that has Terway installed contains more than 100 nodes, the NetworkPolicy proxies cause a heavy load on the management of the cluster. To resolve this issue, you must optimize the NetworkPolicy feature for the cluster. This topic describes how to optimize the performance of the NetworkPolicy feature for a large ACK cluster.

-   An ACK cluster that has Terway installed and contains more than 100 nodes is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

Terway implements the NetworkPolicy feature by using the Felix agent of Calico. In an ACK cluster that contains more than 100 nodes, the Felix agent on each node retrieves proxy rules from the API server. This increases the load of the API server. To reduce the load of the API server, you can disable the NetworkPolicy feature or deploy the Typha component as a repeater.

You can improve the performance of the NetworkPolicy feature for a large ACK cluster in the following ways:

-   Deploy Typha as a repeater.
-   Disable the NetworkPolicy feature.

    **Note:** After you disable the NetworkPolicy feature, you cannot use network policies to control communication among pods.


## Deploy Typha as a repeater

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Upgrade Terway to the latest version. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md).

3.  Use the following template and run the `kubectl apply -f` command to deploy Typha as a repeater.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: calico-typha
      namespace: kube-system
      labels:
        k8s-app: calico-typha
    spec:
      ports:
        - port: 5473
          protocol: TCP
          targetPort: calico-typha
          name: calico-typha
      selector:
        k8s-app: calico-typha
    
    ---
    
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: calico-typha
      namespace: kube-system
      labels:
        k8s-app: calico-typha
    spec:
      replicas: 3
      revisionHistoryLimit: 2
      selector:
        matchLabels:
          k8s-app: calico-typha
      template:
        metadata:
          labels:
            k8s-app: calico-typha
          annotations:
            cluster-autoscaler.kubernetes.io/safe-to-evict: 'true'
        spec:
          nodeSelector:
            kubernetes.io/os: linux
          hostNetwork: true
          tolerations:
            - operator: Exists
          serviceAccountName: terway
          priorityClassName: system-cluster-critical
          containers:
          - image: registry-vpc.{ REGION-ID}.aliyuncs.com/acs/typha:v3.5.8
            name: calico-typha
            ports:
            - containerPort: 5473
              name: calico-typha
              protocol: TCP
            env:
              - name: TYPHA_LOGSEVERITYSCREEN
                value: "info"
              - name: TYPHA_LOGFILEPATH
                value: "none"
              - name: TYPHA_LOGSEVERITYSYS
                value: "none"
              - name: TYPHA_CONNECTIONREBALANCINGMODE
                value: "kubernetes"
              - name: TYPHA_DATASTORETYPE
                value: "kubernetes"
              - name: TYPHA_HEALTHENABLED
                value: "true"
            livenessProbe:
              httpGet:
                path: /liveness
                port: 9098
                host: localhost
              periodSeconds: 30
              initialDelaySeconds: 30
            readinessProbe:
              httpGet:
                path: /readiness
                port: 9098
                host: localhost
              periodSeconds: 10
    
    ---
    
    apiVersion: policy/v1beta1
    kind: PodDisruptionBudget
    metadata:
      name: calico-typha
      namespace: kube-system
      labels:
        k8s-app: calico-typha
    spec:
      maxUnavailable: 1
      selector:
        matchLabels:
          k8s-app: calico-typha
    
    ---
    
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: bgppeers.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: BGPPeer
        plural: bgppeers
        singular: bgppeer
    ```

    **Note:**

    -   Replace the value of the \{REGION-ID\} field with the specified region ID.
    -   Modify the value of the replicas field based on the cluster size. Create 1 replica for every 200 nodes. You must create at least 3 replicas.
4.  Run the following command to add the felix\_relay\_service: calico-typha setting to the ConfigMap of the Terway plug-in:

    ```
    kubectl edit cm eni-config -n kube-system
    #Add the following trunk configuration. The configuration must be aligned with the eni_conf parameter.
    felix_relay_service: calico-typha
    ```

5.  Run the following command to restart Terway:

    ```
    kubectl get pod -n kube-system  | grep terway | awk '{print $1}' | xargs kubectl delete -n kube-system pod
    ```

    ```
    #Output:
    pod "terway-eniip-8hmz7" deleted
    pod "terway-eniip-dclfn" deleted
    pod "terway-eniip-rmctm" deleted
    ```


## Disable the NetworkPolicy feature

If you no longer need to use network policies, you can disable the NetworkPolicy feature to reduce the heavy load on the API server. The heavy load is caused by the NetworkPolicy proxies.

1.  Add disable\_network\_policy: "true" to ConfigMap of the Terway plug-in to disable the NetworkPolicy feature.

    ```
    kubectl edit cm -n kube-system eni-config 
    #Add or modify (if this key exists) the following setting:
    disable_network_policy: "true"
    ```

2.  Run the following command to restart Terway:

    ```
    kubectl get pod -n kube-system  | grep terway | awk '{print $1}' | xargs kubectl delete -n kube-system pod
    ```

    ```
    #Output:
    pod "terway-eniip-8hmz7" deleted
    pod "terway-eniip-dclfn" deleted
    pod "terway-eniip-rmctm" deleted
    ```


After the preceding operations are complete, the NetworkPolicy proxies start to use the Typha component. This reduces the load on the API server. You can monitor the traffic that is distributed to the Server Load Balancer \(SLB\) instances to check whether the load on the API server is reduced.

