---
keyword: [HPA, common problems, diagnostics]
---

# Troubleshooting for HPA

Horizontal Pod Autoscaler \(HPA\) is a component that can automatically scale the number of pods in Kubernetes clusters. This topic describes the common issues that may occur when you use HPA and the solutions.

## Issue 1. HPA cannot fetch monitoring metrics

If a FailedGetResourceMetric warning is returned in the Events section, as shown in the following returned HPA conditions, it indicates that Cloud Controller Manager \(CCM\) cannot fetch monitoring metrics from monitored resources.

```
Name:                                                  kubernetes-tutorial-deployment
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Mon, 10 Jun 2019 11:46:48 +0530
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

-   Cause 1: The Ddata source from which metrics are fetched is unavailable.

    Run the `kubectl top pod` command to check whether metric data of monitored pods is returned. If no metric data is returned, run the `kubectl get apiservice` command to check whether the metrics-server component is available.

    The following is an example of the returned data:

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

    If the apiservice for v1beta1.metrics.k8s.io is not metrics-server deployed in the kube-system namespace, check whether metrics-server is overwritten by Prometheus Operator. If metrics-server is overwritten by Prometheus Operator, use the following YAML template to redeploy metrics-server:

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

    If no error is found after you have performed the preceding checks, see [Troubleshooting for metrics-server](/intl.en-US/Release notes/System Component change Records/Introduction and release notes for metrics-server.md).

-   Cause 2: Metrics cannot be fetched during a rolling update or scale-out activity.

    By default, metrics-server fetches metrics at intervals of one second However, metric-server cannot fetch metrics within a few seconds after a rolling update or scale-out activity. We recommend that you query metrics two seconds after a rolling update or scale-out activity.

-   Reason 3: The requests field is not specified for the pod.

    HPA automatically obtains the CPU or memory usage by calculating the value of `used resource/requested resource` of the pod. If the requested resource is not specified in the pod configurations, HPA cannot calculate the resource usage. Therefore, you must make sure that the requests field is specified in the pod configurations.


## Issue 2: Excessive pods are added by HPA during a rolling update

During a rolling update, CCM performs zero filling on pods that lack of monitoring data. This may add excessive pods. Check whether metrics-server is upgraded to the latest version and configure the following startup settings for the pod where metrics-server is deployed:

```
Add the following configuration to the startup settings.
--enable-hpa-rolling-update-skipped=true
```

## Issue 3: HPA does not scale the number of pods when the scaling threshold is exceeded.

HPA may not scale the number of pods even if the CPU or memory usage drops below the scale-in threshold or exceeds the scale-out threshold. HPA also takes other factors into consideration when it scales pods. For example, it checks whether the current scale-out event triggers a scale-in activity or the scale-in event triggers a scale-out activity. This avoids repetitive scaling and prevents unnecessary resource occupation.

**Related topics**  


[Horizontal pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Horizontal pod autoscaling.md)

