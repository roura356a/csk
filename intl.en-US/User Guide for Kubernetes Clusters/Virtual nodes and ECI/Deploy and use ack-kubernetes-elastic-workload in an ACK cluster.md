# Deploy and use ack-kubernetes-elastic-workload in an ACK cluster

You can use ack-kubernetes-elastic-workload to create elastic workloads. An elastic workload listens to a source workload and can clone elastic units for extra pod replicas based on the predefined scheduling policy. If the number of pod replicas in an elastic workload reaches the threshold, the numbers of pod replicas scheduled on the source workload and elastic units are adjusted. This topic describes how to deploy and use ack-kubernetes-elastic-workload in a cluster of Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   ack-virtual-node is deployed in the ACK cluster. For more information, see [Use ECI in Container Service for Kubernetes]().

## Deploy ack-kubernetes-elastic-workload

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-kubernetes-elastic-workload**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-kubernetes-elastic-workload** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the **App Catalog - ack-kubernetes-elastic-workload** tab, select a cluster to deploy the application and click **Create** in the **Deploy** section.

    ![Deploy ack-kubernetes-elastic-workload](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3420793061/p132495.png)

    Go to the details page of the cluster. In the left-side navigation pane, click **Releases** and click the **Helm** tab. On the Helm tab, you can find the newly deployed ack-kubernetes-elastic-workload.


## Create and use an elastic workload

You must deal with pod scheduling and pod lifecycle management for workloads in a Kubernetes cluster. Pod scheduling and pod lifecycle management are mainly based on the following operations:

-   Change the pod scheduling policy when the number of pod replicas reaches a threshold.
-   Change the lifecycle to prioritize certain pods.

This section describes how to use ack-kubernetes-elastic-workload to perform the preceding operations for a workload.

For example, create an application by using a Deployment based on the following template:

```
apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment-basic
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
    #  nodeSelector:
    #    env: test-team
      containers:
      - name: nginx
        image: nginx:1.7.9 
        ports:
        - containerPort: 80
```

Create an elastic workload for the application.

```
# Define an elastic workload.
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: ElasticWorkload
metadata:
  name: elasticworkload-sample
spec:
  sourceTarget:
    name: nginx-deployment-basic
    kind: Deployment
    apiVersion: apps/v1
    min: 2
    max: 4
  replicas: 6
  elasticUnit:
  - name: virtual-kubelet
    labels:
      virtual-kubelet: "true"
    annotations:
      virtual-kubelet: "true"
    nodeSelector:
      type: "virtual-kubelet"
    tolerations:
    - key: "virtual-kubelet.io/provider"
      operator: "Exists"
    # min: 0 You can specify the maximum and minimum numbers of pod replicas in each elastic unit.
    # max: 10
```

The preceding template shows how to create an elastic workload for the application. Similar to the use of Horizontal Pod Autoscaler \(HPA\), ack-kubernetes-elastic-workload is deployed as an external add-on and does not interfere with your workload.

Typically, an elastic workload is configured with two parts:

1.  SourceTarget: defines the type of the source workload and the range for the number of pod replicas.
2.  elasticUnit: an array that defines the scheduling policies for the elastic unit. If you need to define multiple elastic units, specify related parameters in the same order as shown in the template.

In this example, the number of pod replicas for the source workload ranges from 2 to 4. This indicates that if the elastic workload has 2 to 4 pod replicas, pod replicas are scheduled to the source workload. If the number is greater than 4, extra pod replicas are scheduled to the elastic unit. Pod replicas are scheduled based on the scheduling policies specified in the preceding template, such as the label, annotation, nodeSelector, affinity, and toleration parameters.

An elastic workload listens to a source workload and can clone elastic units for extra pod replicas to be scheduled. If the number of pod replicas in an elastic workload reaches the threshold, the numbers of pod replicas scheduled on the source workload and elastic units are adjusted.

After you run the template for the elastic workload, you can run the following command to view the status of the elastic workload. The value of the Desired Replicas field in the Status section of the result indicates the number of pod replicas that are scheduled to the elastic unit.

Run the following command to query the elastic workload.

```
kubectl describe ew elasticworkload-sample   # same as kubectl get elasticworkload
```

The following output is returned:

```
Name:         elasticworkload-sample
Namespace:    default
Labels:       &amp;amp;lt;none&amp;amp;gt;
Annotations:  &amp;amp;lt;none&amp;amp;gt;
API Version:  autoscaling.alibabacloud.com/v1beta1
Kind:         ElasticWorkload
Metadata:
  Creation Timestamp:  2020-05-06T03:43:41Z
  Generation:          27
  Resource Version:    20635284
  Self Link:           /apis/autoscaling.alibabacloud.com/v1beta1/namespaces/default/elasticworkloads/elasticworkload-sample
  UID:                 0e9205ff-38b8-43b7-9076-ffa130f26ef4
Spec:
  Elastic Unit:
    Annotations:
      Virtual - Kubelet:  true
    Labels:
      Virtual - Kubelet:  true
    Name:                 demo
    Node Selector:
      Type:  virtual-kubelet
    Tolerations:
      Key:       virtual-kubelet.io/provider
      Operator:  Exists
  Replicas:      6
  Source Target:
    API Version:  apps/v1
    Kind:         Deployment
    Max:          2
    Min:          0
    Name:         nginx-deployment-basic
Status:
  Elastic Units Status:
    Desired Replicas:  4
    Name:              nginx-deployment-basic-unit-virtual-kubelet
    Update Timestamp:  2020-05-07T12:38:27Z
  Replicas:            6
  Selector:            app=nginx
  Source Target:
    API Version:       apps/v1
    Desired Replicas:  2
    Kind:              Deployment
    Name:              nginx-deployment-basic
    Update Timestamp:  2020-05-07T12:38:27Z
Events:                &amp;amp;lt;none&amp;amp;gt;
```

After you run the template for the elastic workload, you can run the following command to view the status of pods: The following result shows that new Deployments and pods are cloned based on the source workload. You can also find that the number of pod replicas in these Deployments complies with scheduling policy.

Run the following command to query the pods:

```
kubectl get pod -o wide
```

The following output is returned:

```
NAME                                               READY   STATUS    RESTARTS   AGE    IP             NODE                     NOMINATED NODE   READINESS GATES
nginx-deployment-basic-7ff9955f89-djxwv            1/1     Running   0          138m   172.20. *.***   cn-hangzhou.10.0. *.***   &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-7ff9955f89-hrw2z            1/1     Running   0          138m   172.2*. *.**    cn-hangzhou.10.0. *.***   &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-4f8xt   1/1     Running   0          138m   10.1. **.**     virtual-node-eci-1       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-bl5pd   1/1     Running   0          138m   10.1. **.**     virtual-node-eci-0       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-ndbp8   1/1     Running   0          138m   10.1. **.**     virtual-node-eci-0       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-vx9jx   1/1     Running   0          138m   10.1. **.**     virtual-node-eci-2       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
```

You can also use HPA in an elastic workload as shown in the following example. If the number of pod replicas of an elastic workload changes, HPA can dynamically adjust the number of pod replicas scheduled to each elastic unit. For example, if the number of the pod replicas changes from 6 to 4, elastic units will scale in and the pod replicas of elastic units are reduced in priority.

```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: elastic-workload-demo
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: autoscaling.alibabacloud.com/v1beta1
    kind: ElasticWorkload
    name: elasticworkload-sample
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

**Note:** In conclusion, an elastic workload can clone Deployments and pod replicas, and overwrite pod scheduling policies for elastic units. This allows you to manage the scheduling of pods for your workload. An elastic workload can also adjust the number of pod replicas that are scheduled to the source workload and the number of elastic units by setting a range for the number of pod replicas. This allows you to prioritize certain pods.

