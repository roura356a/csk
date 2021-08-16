---
keyword: [Horizontal Pod Autoscaling \(HPA\), FAQ, diagnosis]
---

# FAQ about auto scaling

This topic provides answers to some frequently asked questions about auto scaling in Container Service for Kubernetes \(ACK\).

|Type|Issue|
|----|-----|
|FAQ about HPA|-   [What do I do if the current field shows unknown in the HPA metrics?](#section_7l1_a7b_adu)
-   [What do I do if excess pods are added by HPA during a rolling update?](#section_oxl_0ov_og8)
-   [What do I do if HPA does not scale pods when the scaling threshold is reached?](#section_1tg_5jx_p4h)
-   [How do I configure the metric collection interval of HPA?](#section_i3r_lgt_w1n)
-   [Can CronHPA and HPA interact without conflicts?](#section_ilk_54c_4f6)
-   [How do I fix the issue that excess pods are added by HPA when CPU or memory usage rapidly increases?](#section_2cl_008_8x0) |
|FAQ about HPA based on Alibaba Cloud metrics|-   [What do I do if the TARGETS column shows &lt;unknown&gt; after I run the `kubectl get hpa` command?](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement horizontal auto scaling based on Alibaba Cloud metrics.md)
-   [Where can I find the metrics that are supported by HPA?](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement horizontal auto scaling based on Alibaba Cloud metrics.md) |
|FAQ about node auto scaling|-   [Why does the auto scaling component fail to add nodes after a scale-out activity is triggered?](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)
-   [Why does the auto scaling component fail to remove nodes after a scale-in activity is triggered?](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)
-   [How does the system choose a scaling group for a scaling activity?](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md) |

## What do I do if the current field shows unknown in the HPA metrics?

If the current field of HPA metrics shows unknown, it indicates that kube-controller-manager cannot access monitored data sources to fetch resource metrics.

```
Name:                                                  kubernetes-tutorial-deployment
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Mon, 10 Jun 2019 11:46:48  0530
Reference:                                             Deployment/kubernetes-tutorial-deployment
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 2%
Min replicas:                                          1
Max replicas:                                          4
Deployment pods:                                       1 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server is currently unable to handle the request (get pods.metrics.k8s.io)
Events:
  Type     Reason                   Age                      From                       Message
  ----     ------                   ----                     ----                       -------
  Warning  FailedGetResourceMetric  3m3s (x1009 over 4h18m)  horizontal-pod-autoscaler  unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server is currently unable to handle the request (get pods.metrics.k8s.io)
```

Possible causes:

-   Cause 1: The data sources from which resource metrics are fetched are unavailable. Run the `kubectl top pod` command to check whether metric data of monitored pods is returned. If no metric data is returned, run the `kubectl get apiservice` command to check whether the metrics-server component is available. The following output shows an example of the returned data:

    ```
    NAME                                   SERVICE                      AVAILABLE   AGE
    v1.                                    Local                        True        29h
    v1.admissionregistration.k8s.io        Local                        True        29h
    v1.apiextensions.k8s.io                Local                        True        29h
    v1.apps                                Local                        True        29h
    v1.authentication.k8s.io               Local                        True        29h
    v1.authorization.k8s.io                Local                        True        29h
    v1.autoscaling                         Local                        True        29h
    v1.batch                               Local                        True        29h
    v1.coordination.k8s.io                 Local                        True        29h
    v1.monitoring.coreos.com               Local                        True        29h
    v1.networking.k8s.io                   Local                        True        29h
    v1.rbac.authorization.k8s.io           Local                        True        29h
    v1.scheduling.k8s.io                   Local                        True        29h
    v1.storage.k8s.io                      Local                        True        29h
    v1alpha1.argoproj.io                   Local                        True        29h
    v1alpha1.fedlearner.k8s.io             Local                        True        5h11m
    v1beta1.admissionregistration.k8s.io   Local                        True        29h
    v1beta1.alicloud.com                   Local                        True        29h
    v1beta1.apiextensions.k8s.io           Local                        True        29h
    v1beta1.apps                           Local                        True        29h
    v1beta1.authentication.k8s.io          Local                        True        29h
    v1beta1.authorization.k8s.io           Local                        True        29h
    v1beta1.batch                          Local                        True        29h
    v1beta1.certificates.k8s.io            Local                        True        29h
    v1beta1.coordination.k8s.io            Local                        True        29h
    v1beta1.events.k8s.io                  Local                        True        29h
    v1beta1.extensions                     Local                        True        29h
    ...
    [v1beta1.metrics.k8s.io                 kube-system/metrics-server   True        29h]
    ...
    v1beta1.networking.k8s.io              Local                        True        29h
    v1beta1.node.k8s.io                    Local                        True        29h
    v1beta1.policy                         Local                        True        29h
    v1beta1.rbac.authorization.k8s.io      Local                        True        29h
    v1beta1.scheduling.k8s.io              Local                        True        29h
    v1beta1.storage.k8s.io                 Local                        True        29h
    v1beta2.apps                           Local                        True        29h
    v2beta1.autoscaling                    Local                        True        29h
    v2beta2.autoscaling                    Local                        True        29h
    ```

    If the apiservice for v1beta1.metrics.k8s.io is not kube-system/metrics-server, check whether metrics-server is overwritten by Prometheus Operator. If metrics-server is overwritten by Prometheus Operator, use the following YAML template to redeploy metrics-server:

    ```
    apiVersion: apiregistration.k8s.io/v1beta1
    kind: APIService
    metadata:
      name: v1beta1.metrics.k8s.io
    spec:
      service:
        name: metrics-server
        namespace: kube-system
      group: metrics.k8s.io
      version: v1beta1
      insecureSkipTLSVerify: true
      groupPriorityMinimum: 100
      versionPriority: 100
    ```

    If the issue still exists, see [Troubleshooting](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/metrics-server.md).

-   Cause 2: Metrics cannot be fetched during a rolling update or scale-out activity.

    By default, metrics-server fetches metrics at intervals of 1 second. However, metrics-server must wait a few seconds before it can collect metrics after a rolling update or scale-out activity. We recommend that you query metrics 2 seconds after a rolling update or scale-out activity.

-   Cause 3: The request field is not specified for the pod.

    By default, HPA obtains the CPU or memory usage of the pod by calculating the value of used resources/requested resources. If the requested resources are not specified in the pod configurations, HPA cannot calculate the resource usage. Therefore, you must ensure that the request field is specified in the resource parameter of the pod configurations.


## What do I do if excess pods are added by HPA during a rolling update?

During a rolling update, kube-controller-manager performs zero filling on pods whose monitoring data cannot be collected. This may cause HPA to add excess pods. To fix this issue, we recommend that you upgrade metrics-server to the latest version and add the following parameter to the startup settings of metrics-server:

```
## Add the following parameter to the startup settings of metrics-server. 
--enable-hpa-rolling-update-skipped=true
```

## What do I do if HPA does not scale pods when the scaling threshold is reached?

HPA may not scale pods even if the CPU or memory usage drops below the scale-in threshold or exceeds the scale-out threshold. HPA also takes other factors into consideration when it scales pods. For example, HPA checks whether the current scale-out activity triggers a scale-in activity or the current scale-in activity triggers a scale-out activity. This avoids repetitive scaling and prevents unnecessary resource consumption.

## How do I configure the metric collection interval of HPA?

For metrics-server versions later than 0.2.1-b46d98c-aliyun, specify the `--metric_resolution` parameter in the startup settings. Example: `--metric_resolution=15s`.

## Can CronHPA and HPA interact without conflicts?

CronHPA and HPA can interact without conflicts. ACK modifies the CronHPA configurations by setting scaleTargetRef to the scaling object of HPA. This way, only HPA scales the application that is specified by scaleTargetRef. This also enables CronHPA to detect the state of HPA. CronHPA does not directly change the number of pods for the Deployment. CronHPA triggers HPA to scale the pods. This way, conflicts between CronHPA and HPA are resolved. For more information about how to enable CronHPA and HPA to interact without conflicts, see [CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).

## How do I fix the issue that excess pods are added by HPA when CPU or memory usage rapidly increases?

When the pods of Java applications or applications powered by Java frameworks start to run, the CPU and memory usage may be high for a few minutes during the warm-up period. This may trigger HPA to scale out the pods. To fix this issue, upgrade metrics-server to the latest version and add annotations to the pod configurations to prevent HPA from triggering scaling activities. For more information about how to upgrade metrics-server, see [Install the metrics-server component](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Manage clusters/Install the metrics-server component.md).

The following YAML template provides the sample pod configurations that prevent HPA from triggering scaling activities in this scenario.

```
## A Deployment is used in this example.
apiVersion: apps/v1
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
      annotations:
        HPAScaleUpDelay: 3m # This setting indicates that HPA takes effect 3 minutes after the pods are created. Valid units: s and m. s indicates seconds and m indicates minutes. 
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9 # Replace it with your exactly <image_name:tags>.
        ports:
        - containerPort: 80 
```

## FAQ about node auto scaling

-   Why does the auto scaling component fail to add nodes after a scale-out activity is triggered?

    Check whether the following situations exist:

    -   The instance types in the scaling group cannot fulfill the resource request from pods. By default, system components are installed for each node. Therefore, the requested pod resources must be less than the resource capacity of the instance type.
    -   The RAM role does not have the permissions to manage the Kubernetes cluster. You must complete the authorization for each Kubernetes cluster that is involved in the scale-out activity.
    -   The Kubernetes cluster cannot connect to the Internet. The auto scaling component must call Alibaba Cloud API operations. Therefore, the nodes must have access to the Internet.
-   Why does the auto scaling component fail to remove nodes after a scale-in activity is triggered?

    Check whether the following situations exist:

    -   The requested resource threshold of each pod is higher than the configured scale-in threshold.
    -   Pods that belong tothe kube-system namespace are running on the node.
    -   A scheduling policy forces the pods to run on the current node. Therefore, the pods cannot be scheduled to other nodes.
    -   PodDisruptionBudget is set[for the](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work) pods on the node and the minimum value of PodDisruptionBudget is reached.
    For more information[about FAQ,](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) see open source component.

-   How does the system choose a scaling group for a scaling activity?

    When pods cannot be scheduled to nodes, the auto scaling component simulates the scheduling of the pods based on the configuration of scaling groups. The configuration includes labels, taints, and instance specifications. If a scaling group can simulate the scheduling of the pods, this scaling group is selected for the scale-out activity. If more than one scaling groups meet the requirements, the system selects the scaling group that has the fewest idle resources after simulation.


## FAQ about HPA based on Alibaba Cloud metrics

-   What do I do if the TARGETS column shows <unknow\> after I run the `kubectl get hpa` command?

    Perform the following operations to troubleshoot the issue:

    1.  Run the `kubectl describe hpa <hpa_name>` command to check why HPA does not function as normal.
        -   If the value of AbleToScale is False in the Conditions field, check whether the Deployment is created as normal.
        -   If the value of ScalingActive is False in the Conditions field, proceed to the next step.
    2.  Run the `kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/"` command. If Error from server \(NotFound\): the server could not find the requested resource is returned, verify the status of alibaba-cloud-metrics-adapter.

        If the status of alibaba-cloud-metrics-adapter is normal, check whether the HPA metrics are relevant to the Ingress. If the metrics are relevant to the Ingress, make sure that you deploy the Log Service component before ack-alibaba-cloud-metrics-adapter is deployed. For more information, see [Monitor nginx-ingress and analyze the access log of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor nginx-ingress and analyze the access log of nginx-ingress.md).

    3.  Make sure that the values of the HPA metrics are valid. The value of sls.ingress.route must be in the `<namespace>-<svc>-<port>` format.
        -   `<namespace>` specifies the namespace to which the Ingress belongs.
        -   `<svc>` specifies the name of the Service that you selected when you created the Ingress.
        -   `<port>` specifies the port of the Service.
-   Where can I find the metrics that are supported by HPA?

    For more information about the metrics that are supported by HPA, see [Alibaba Cloud metrics adapter](https://github.com/AliyunContainerService/alibaba-cloud-metrics-adapter). The following table describes the commonly used metrics.

    |Metric|Description|Additional parameter|
    |------|-----------|--------------------|
    |sls\_ingress\_qps|The number of requests that the Ingress can process per second based on a specific routing rule.|sls.ingress.route|
    |sls\_ingress\_latency\_avg|The average latency of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p50|The maximum latency for the fastest 50% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p95|The maximum latency for the fastest 95% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p99|The maximum latency for the fastest 99% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p9999|The maximum latency for the fastest 99.99% of all requests.|sls.ingress.route|
    |sls\_ingress\_inflow|The inbound bandwidth of the Ingress.|sls.ingress.route|


**Related topics**  


[metrics-server](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/metrics-server.md)

[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

[CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md)

[Install the metrics-server component](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Manage clusters/Install the metrics-server component.md)

