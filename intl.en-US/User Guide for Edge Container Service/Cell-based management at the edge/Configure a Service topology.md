---
keyword: [Service topology, edge computing]
---

# Configure a Service topology

The backend endpoints of Kubernetes-native Services are arbitrarily distributed across nodes. Consequently, when Service requests are distributed to nodes across node groups, these requests may fail to reach the nodes or may not be answered promptly. You can configure a Service topology to expose an application on an edge node only to the current node or other nodes in the same edge node pool. This topic describes how a Service topology works and how to configure a Service topology.

In edge computing, edge nodes are classified into groups by zone, region, or other logical attribute, such as CPU architecture, Internet service provider \(ISP\), or cloud service provider. Nodes in different groups are isolated from each other in one way or another. For example, these nodes may be disconnected, may not share the same resources, may have heterogeneous resources, or may run applications that are independently deployed.

## How a Service topology works

To solve the preceding issues, ACK@Edge provides a feature to manage the topology of endpoints of Kubernetes-native Services. You can configure a Service topology to specify how endpoints of a Service are accessed. For example, you can configure a Service topology to expose an application on an edge node only to the current node or other nodes in the same edge node pool. The following figure shows how a Service topology works.

![G-8](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1987896161/p224391.png)

-   Service 1 is associated with Pod 2 and Pod 3. `annotation:"openyurt.io/topologyKeys: kubernetes.io/zone"` specifies the node pool that is allowed to access Service 1.
-   Pod 2 is deployed on Node 2 and Pod 3 is deployed on Node 3. Node 2 belongs to Node Pool A and Node 3 belongs to Node Pool B.
-   Pod 3 and Pod 1 do not belong to the same node pool. As a result, when Pod 1 accesses Service 1, the traffic is forwarded only to Pod 2. The traffic is not forwarded to Pod 3.

## Method 1: Configure a Service topology in the ACK console

To create a Service that can be accessed only by the node pool where the Service is deployed, you need only to add an annotation to the Service. For example, you can set **Name** to `openyurt.io/topologyKeys` and **Value** to `kubernetes.io/zone`. For more information about how to create a Service, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

![G-9](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2987896161/p224982.png)

## Method 2: Configure a Service topology by using the CLI

You can use the CLI to configure a Service topology in the following ways:

-   Create a Service that uses the topological domain of a specific node pool. The following code block is an example of the YAML template:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        openyurt.io/topologyKeys: kubernetes.io/zone
      name: my-service-nodepool
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 8080
      selector:
        app: nginx
      sessionAffinity: None
      type: ClusterIP
    ```

-   Run the following command to configure the Service topology. The Service uses the topological domain of the specified node pool.

    ```
    kubectl annotate service xxx openyurt.io/topologyKeys='kubernetes.io/zone'
    ```


## Annotations

You can add annotations to a Kubernetes-native Service to configure a Service topology. The annotations are described in the following table.

|annotation Key|annotation Value|Description|
|--------------|----------------|-----------|
|openyurt.io/topologyKeys|kubernetes.io/hostname|Specifies that the Service can be accessed only by the node where the Service is deployed.|
|openyurt.io/topologyKeys|kubernetes.io/zone or openyurt.io/nodepool|Specifies that the Service can be accessed only by the nodes in the node pool where the Service is deployed. If the version of the edge Kubernetes cluster is 1.18 or later, we recommend that you use openyurt.io/nodepool.|
|-|-|Specifies that access to the Service is unlimited.|

