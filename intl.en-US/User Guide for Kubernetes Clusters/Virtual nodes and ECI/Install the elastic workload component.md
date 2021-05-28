# Install the elastic workload component

You can use ack-kubernetes-elastic-workload to create elastic workloads. An elastic workload listens on a source workload and can create elastic units based on the scheduling policies of the elastic units. If the number of pod replicas in an elastic workload exceeds a threshold, the numbers of pod replicas scheduled to the source workload and elastic units are adjusted. This topic describes how to deploy and use ack-kubernetes-elastic-workload in a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   ack-virtual-node is deployed in the ACK cluster. For more information, see [Use ECI in Container Service for Kubernetes]().

## Install ack-kubernetes-elastic-workload

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-kubernetes-elastic-workload**.

    You can search for **ack-kubernetes-elastic-workload** on the **Alibaba Cloud Apps** tab. Enter ack-virtual-kubelet-autoscaler into the Name search box and click the search icon.

4.  On the **App Catalog - ack-kubernetes-elastic-workload** tab, select the created cluster in the **Deploy** section and click **Create**.

    ![Install ack-kubernetes-elastic-workload](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3420793061/p132495.png)

5.  In the left-side navigation pane of the ACK console, click **Clusters**.

6.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

7.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**. You can verify that ack-kubernetes-elastic-workload is deployed.


## Use elastic workloads

In a Kubernetes cluster, you must deal with pod scheduling and pod lifecycle management for workloads. Pod scheduling and pod lifecycle management are based on the following operations:

-   Change the pod scheduling policy when the number of pod replicas reaches a threshold.
-   Prioritize specific pods for lifecycle management.

This section describes how to use elastic workloads to perform the preceding operations.

Create an application by using a Deployment based on the following template:

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
    # min: 0 You can specify different values for different elastic units. 
    # max: 10
```

The preceding template shows how to create an elastic workload for the application. Similar to the use of Horizontal Pod Autoscaler \(HPA\), ack-kubernetes-elastic-workload is deployed as an external plug-in and does not interfere with your workloads.

Typically, the configurations of an elastic workload include the following parts:

1.  SourceTarget: defines the type of the source workload and the range of the number of pod replicas.
2.  elasticUnit: an array of fields that define the scheduling policies of an elastic unit. If you want to define multiple elastic units, set the fields in the same order as shown in the template for each elastic unit.

In this example, the number of pod replicas provisioned for the source workload ranges from 2 to 4. This indicates that pod replicas are scheduled to the source workload if the elastic workload has 2 to 4 pod replicas. If the number of pod replicas is greater than 4, extra pod replicas are scheduled to the virtual-kubelet elastic unit. Pod replicas are scheduled based on the scheduling policies of virtual-kubelet, such as the label, annotation, nodeSelector, affinity, and toleration parameters.

![Pod scheduling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8912712261/p132512.png)

The elastic workload listens on the source workload and can create elastic units based on the scheduling policy of the elastic unit. If the number of pod replicas in the elastic workload exceeds the threshold, the numbers of pod replicas scheduled to the source workload and the elastic unit are adjusted.

After you deploy the elastic workload with the preceding template, you can run the following command to query the state of the elastic workload. The value of the Desired Replicas field in the Status section of the output indicates the number of pod replicas that are scheduled to the elastic unit.

Run the following command to query the state of the elastic workload:

```
kubectl describe ew elasticworkload-sample   # same as kubectl get elasticworkload
```

Expected output:

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

After you deploy the elastic workload with the preceding template, you can run the following command to query the states of the pods: The output shows that new Deployments and pods are created by cloning the source workload. You can also find that the number of pod replicas in these Deployments complies with the scheduling policy.

Run the following command to query the states of the pods:

```
kubectl get pod -o wide
```

Expected output:

```
NAME                                               READY   STATUS    RESTARTS   AGE    IP             NODE                     NOMINATED NODE   READINESS GATES
nginx-deployment-basic-7ff9955f89-djxwv            1/1     Running   0          138m   172.20.*.***   cn-hangzhou.10.0.*.***   &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-7ff9955f89-hrw2z            1/1     Running   0          138m   172.2*.*.**    cn-hangzhou.10.0.*.***   &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-4f8xt   1/1     Running   0          138m   10.1.**.**     virtual-node-eci-1       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-bl5pd   1/1     Running   0          138m   10.1.**.**     virtual-node-eci-0       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-ndbp8   1/1     Running   0          138m   10.1.**.**     virtual-node-eci-0       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
nginx-deployment-basic-unit-demo-8bb586568-vx9jx   1/1     Running   0          138m   10.1.**.**     virtual-node-eci-2       &amp;amp;lt;none&amp;amp;gt;           &amp;amp;lt;none&amp;amp;gt;
```

You can also use HPA to scale an elastic workload, as shown in the preceding figure. If an elastic workload is scaled by HPA, the elastic workload adjusts the number of pod replicas scheduled to each elastic unit. For example, if HPA reduces the number of pod replicas in the elastic workload from 6 to 4, elastic units will scale in and the pod replicas of elastic units are reduced in priority.

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

**Note:** Elastic workloads create Deployments by cloning source Deployments and overwriting scheduling policies. This allows you to manage scheduling policies. Elastic workloads also adjust the pod replicas scheduled to source workloads and elastic units. This allows you to prioritize specific pods.

