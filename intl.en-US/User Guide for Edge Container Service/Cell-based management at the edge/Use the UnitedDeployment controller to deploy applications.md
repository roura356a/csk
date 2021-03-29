---
keyword: [use the UnitedDeployment controller to deploy applications, manage edge node pools]
---

# Use the UnitedDeployment controller to deploy applications

In edge computing scenarios, you can use the UnitedDeployment controller to deploy applications to different node pools. This way, you can centrally manage the number of pods and the image version of containers by node pool. This topic describes how to use the UnitedDeployment controller to deploy applications.

In edge computing scenarios, computing nodes may be deployed across regions, and same applications may run on nodes in different regions. Deployment is used as an example in this topic. Traditionally, you add the same label to the nodes that are deployed in the same region and create multiple Deployments. These Deployments match different labels through node selectors and are deployed in different regions to meet your business requirements.

![G-6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6527896161/p224323.png)

Application management and maintenance become more complex with the increasing number of regions and differentiated requirements for applications in different regions. The following lists the main challenges:

-   When a new image version is released, you must modify the image version for each Deployment.
-   You must customize naming conventions to identify Deployments that belong to the same application.
-   Deployments that belong to the same application are configured in the same way, except for the name, node selector, and replicated pods.

The UnitedDeployment controller is a feature provided by ACK@Edge. This feature allows you to centrally manage Deployments from a different dimension. For example, you can create, update, and delete multiple Deployments at a time.

![G-7](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6527896161/p224329.png)

The UnitedDeployment controller provides a template to define applications. This template allows you to deploy workloads in different regions and define each region as a node pool. The UnitedDeployment controller supports two types of workloads: StatefulSet and Deployment. The UnitedDeployment controller creates Deployments or StatefulSets based on the configurations of node pools. You can specify the number of replicated pods for each type of workload. UnitedDeployment enables automatic management and maintenance of multiple Deployments or StatefulSets within individual node pools. In addition, you can create differentiated configurations for these Deployments or StatefulSets, such as the name, node selector, and replicated pods.

## Create a UnitedDeployment

Create a UnitedDeployment to deploy Deployments.

The following YAML template is an example:

```
apiVersion: apps.openyurt.io/v1alpha1
kind: UnitedDeployment
metadata:
  name: example 
  namespace: default
spec:
  revisionHistoryLimit: 5
  selector:
    matchLabels:
      app: example 
  workloadTemplate:
    deploymentTemplate:
      metadata:
        creationTimestamp: null
        labels:
          app: example 
      spec:
        selector:
          matchLabels:
            app: example 
        template:
          metadata:
            creationTimestamp: null
            labels:
              app: example 
          spec:
            containers:
            - image: nginx:1.19.3
              imagePullPolicy: Always
              name: nginx
            dnsPolicy: ClusterFirst
            restartPolicy: Always
  topology:
    pools:
    - name: cloud
      nodeSelectorTerm:
        matchExpressions:
        - key: apps.openyurt.io/uniteddeployment
          operator: In
          values:
          - cloud
      replicas: 2
    - name: edge
      nodeSelectorTerm:
        matchExpressions:
        - key: apps.openyurt.io/uniteddeployment
          operator: In
          values:
          - edge
      replicas: 2
      tolerations:
      - effect: NoSchedule
        key: apps.openyurt.io/taints
        operator: Exists            
```

The following table describes the fields in the YAML template.

|Field|Description|
|-----|-----------|
|spec.workloadTemplate|Indicates the workload template. Only the `deploymentTemplate and statefulSetTemplate` templates are supported.|
|spec.topology.pools|Specifies multiple node pools.|
|spec.topology.pools\[\*\].name|The name of the node pool.|
|spec.topology.pools\[\*\].nodeSelectorTerm|Specifies node affinity for the node pool. Set the key to `apps.openyurt.io/nodepool` and set the value to the name of the node pool.|
|spec.topology.pools\[\*\].tolerations|Sets tolerance rules for the node pool.|
|spec.topology.pools\[\*\].replicas|The number of pods in each node pool.|

## Use the UnitedDeployment controller to manage pods

-   Upgrade pods: You can modify the `spec.template.workloadTemplate.deploymentTemplate` field to trigger pod upgrades. The UnitedDeployment controller updates the workload template for all node pools. Then, the node pool controller upgrades the pods in the node pools.
-   Scale replicated pods for multiple node pools: You can modify the `spec.topology.pools` field to change the number of replicated pods for multiple node pools. Then, the replicated pods in the node pools are scaled based on the configuration.

