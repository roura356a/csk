---
keyword: [kubernetes, vertical pod autoscaling, VPA \(vertical-pod-autoscaler\)]
---

# Vertical pod autoscaling

You can deploy the vertical-pod-autoscaler component in a Container Service for Kubernetes \(ACK\) cluster. vertical-pod-autoscaler is a Vertical Pod Autoscaler \(VPA\). The VPA enables vertical autoscaling of pods. The VPA automatically sets limits on the resource usage of a cluster based on the pod resource usage. This way, ACK can schedule the pods to nodes that have sufficient resources. The VPA also maintains the ratio of requests to limits that you specify in the initial container configurations. This topic describes how to use a YAML file to enable vertical pod autoscaling.

Make sure that the following operations are completed:

-   An ACK cluster is created and its Kubernetes version is later than 1.12. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the cluster by using a command-line tool. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).
-   VPA is uninstalled from the cluster. This avoids conflicts when you deploy a new version of the VPA.

**Note:** Vertical pod autoscaling is in testing. Use this feature with caution.

-   You can use the VPA to update the resource configurations of the running pods. This feature is in testing. The configuration updates will lead to pod restart and recreation, and the pods may be scheduled to other nodes.
-   The VPA does not evict the pods that are not managed by a replication controller. For these pods, the Auto mode is equivalent to the Initial mode.
-   The VPA and the Horizontal Pod Autoscaler \(HPA\) cannot run at the same time. The HPA monitors the CPU and memory metrics. If the HPA monitors only other custom or external resource metrics, you can use the VPA in conjunction with the HPA.
-   The VPA uses an admission webhook as its admission controller. If other admission webhooks exist in the cluster, make sure that the admission webhooks do not conflict with the admission webhook of the VPA. The execution sequence of admission controllers is defined in the configuration parameters of the API server.
-   The VPA can react to most out of memory \(OOM\) events, but may fail to handle some events in some scenarios.
-   The VPA performance is not tested in large-scale clusters.
-   The pod resource requests set by the VPA may exceed the upper limit of the actual resources, including node resources, idle resources, and resource quotas. In this case, a pod may enter the Pending state and cannot be scheduled. You can use the cluster autoscaler to mitigate this issue.
-   If multiple VPAs monitor the resource usage of a pod at the same time, some undefined behavior may occur.

## Install vertical-pod-autoscaler

1.  Run the following command to create a CustomResourceDefinition \(CRD\) for vertical-pod-autoscaler.

    The CRD improves the scalability of ACK clusters. For more information, see [Extend the Kubernetes API with CustomResourceDefinitions](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/).

    ```
    kubectl apply -f crd.yaml
    ```

    Use the following content to create a crd.yaml file:

    ```
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: verticalpodautoscalers.autoscaling.k8s.io
      annotations:
        "api-approved.kubernetes.io": "https://github.com/kubernetes/kubernetes/pull/63797"
    spec:
      group: autoscaling.k8s.io
      scope: Namespaced
      names:
        plural: verticalpodautoscalers
        singular: verticalpodautoscaler
        kind: VerticalPodAutoscaler
        shortNames:
          - vpa
      version: v1beta1
      versions:
        - name: v1beta1
          served: false
          storage: false
        - name: v1beta2
          served: true
          storage: true
        - name: v1
          served: true
          storage: false
      validation:
        # openAPIV3Schema is the schema for validating custom objects.
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              required: []
              properties:
                targetRef:
                  type: object
                updatePolicy:
                  type: object
                  properties:
                    updateMode:
                      type: string
                resourcePolicy:
                  type: object
                  properties:
                    containerPolicies:
                      type: array
                      items:
                        type: object
                        properties:
                          containerName:
                            type: string
                          controlledValues:
                            type: string
                            enum: ["RequestsAndLimits", "RequestsOnly"]
                          mode:
                            type: string
                            enum: ["Auto", "Off"]
                          minAllowed:
                            type: object
                          maxAllowed:
                            type: object
                          controlledResources:
                            type: array
                            items:
                              type: string
                              enum: ["cpu", "memory"]
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: verticalpodautoscalercheckpoints.autoscaling.k8s.io
      annotations:
        "api-approved.kubernetes.io": "https://github.com/kubernetes/kubernetes/pull/63797"
    spec:
      group: autoscaling.k8s.io
      scope: Namespaced
      names:
        plural: verticalpodautoscalercheckpoints
        singular: verticalpodautoscalercheckpoint
        kind: VerticalPodAutoscalerCheckpoint
        shortNames:
          - vpacheckpoint
      version: v1beta1
      versions:
        - name: v1beta1
          served: false
          storage: false
        - name: v1beta2
          served: true
          storage: true
        - name: v1
          served: true
          storage: false
    ```

2.  Install the components of vertical-pod-autoscaler.

    vertical-pod-autoscaler contains the following components: admission-controller, recommender, and updater.




## Verify that the VPA is installed

1.  Use the following YAML file to define and create a Deployment named nginx-deployment-basic and a VPA resource named nginx-deployment-basic-vpa.

    ```
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
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
    ---
    apiVersion: autoscaling.k8s.io/v1
    kind: VerticalPodAutoscaler
    metadata:
      name: nginx-deployment-basic-vpa
    spec:
      targetRef:
        apiVersion: "apps/v1"
        kind:       Deployment
        name:       nginx-deployment-basic
      updatePolicy:
        updateMode: "Off"
    ```

    **Note:** Set updateMode to Off, and leave the requests and limits fields in the Deployment unspecified.

2.  Run the following command to query the CPU and memory resource requests recommended by the VPA for the Deployment:

    ```
    kubectl describe vpa nginx-deployment-basic-vpa
    ```

    The following command output shows the recommended resource requests:

    ```
    recommendation:
        containerRecommendations:
        - containerName: nginx
          lowerBound:
            cpu: 50m
            memory: 300144k
          target:
            cpu: 50m
            memory: 300144k
          upperBound:
            cpu: 8031m
            memory: 800000k
    ```

    You can set the resource requests for the Deployment based on the recommendation. The VPA performs continuous monitoring of the resource usage of the Deployment and provides optimization suggestions.


**Related topics**  


[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

