---
keyword: [OpenKruise, CloneSet, Advanced StatefulSet]
---

# Use OpenKruise to deploy cloud-native applications

OpenKruise is a standard extension of Kubernetes and can work with Kubernetes-native clusters. Automation is the core feature of OpenKruise that allows you to automate the deployment of applications in Kubernetes, including pod deployment, upgrades, and scaling. This topic describes how to use OpenKruise to deploy cloud-native applications.

OpenKruise is an open source automation engine provided by Alibaba Cloud for cloud-native applications. It is used as a deployment base to migrate the business of Alibaba Group to the cloud. OpenKruise has joined the Cloud Native Computing Foundation \(CNCF\) Sandbox project.

OpenKruise contains a variety of custom workloads. You can use the workloads to deploy and manage stateless applications, stateful applications, sidecar containers, and daemon applications. OpenKruise also supports advanced strategies such as in-place upgrades, canary release, stream upgrades, and priority configuration.

## Instruction

OpenKruise provides controllers such as CloneSet, Advanced StatefulSet, and Advanced DaemonSet. The following section describes the features of commonly used controllers.

|Controller|Feature|Start rating|
|----------|-------|------------|
|CloneSet|CloneSets are equivalent to Kubernetes-native Deployments. CloneSets are used to manage stateless applications. For more information, see [CloneSet](https://openkruise.io/zh-cn/docs/cloneset.html).

The fields in a CloneSet YAML file do not completely match those in a Deployment YAML file. However, CloneSets support all features of Deployments and provide more strategies.

|✩✩✩✩✩|
|Advanced StatefulSet|Advanced StatefulSets are equivalent to Kubernetes-native StatefulSets. Advanced StatefulSets are used to manage stateful applications. For more information, see [Advanced StatefulSet](https://openkruise.io/zh-cn/docs/advanced_statefulset.html).

The fields in an Advanced StatefulSet YAML file completely match those in a StatefulSet YAML file. You only need to change the value of `apiVersion` to `apps.kruise.io/v1alpha1`. In addition, you can set the `optional` field to use more release strategies, such as in-place upgrades and parallel release.

|✩✩✩✩|
|Advanced DaemonSet|Advanced DaemonSets are equivalent to Kubernetes-native DaemonSets. Advanced DaemonSets are used to manage daemon applications. For more information, see [Advanced DaemonSet](https://openkruise.io/zh-cn/docs/advanced_daemonset.html).

The fields in an Advanced DaemonSet YAML file completely match those in a DaemonSet YAML file. You only need to change the value of `apiVersion` to `apps.kruise.io/v1alpha1`. In addition, you can set the `optional` field to use more release strategies, such as hot upgrades, canary release, and canary release by node label.

|✩✩✩✩|
|SidecarSet|The SidecarSet controller independently manages sidecar containers and injects sidecar containers to pods. For more information, see [SidecarSet](https://openkruise.io/zh-cn/docs/sidecarset.html).

After you define a sidecar container and a label selector in an independent custom resource \(CR\), OpenKruise injects the defined sidecar container to the pod that matches the conditions when the pod is created. You can also perform in-place upgrades for the injected sidecar container by using a SidecarSet.

|✩✩✩✩|
|UnitedDeployment|The UnitedDeployment controller manages multiple sub-workloads in different regions. For more information, see [UnitedDeployment](https://openkruise.io/zh-cn/docs/uniteddeployment.html).

The UnitedDeployment controller supports the following sub-workloads: CloneSets, StatefulSets, and Advanced StatefulSets. You can use one UnitedDeployment to manage sub-workloads in different regions and pod replicas of these sub-workloads.

|✩✩✩|

The following section compares the CloneSet, Advanced StatefulSet, and Advanced DaemonSet controllers of OpenKruise with the corresponding controllers provided by the Kubernetes community.

|Feature|CloneSet VS Deployment|Advanced StatefulSet VS StatefulSet|Advanced DaemonSet VS DaemonSet|
|CloneSet|Deployment|Advanced StatefulSet|StatefulSet|Advanced DaemonSet|DaemonSet|
|-------|----------------------|-----------------------------------|-------------------------------|
|--------|----------|--------------------|-----------|------------------|---------|
|Stream scaling|Not supported \(coming soon\)|Not supported|Not supported|Not supported|Supported|Not supported|
|Specified pod deletion|Supported|Not supported|Supported|Not supported|Not supported|Not supported|
|Upgrade pods upon recreation|Supported|Supported|Supported|Supported|Supported|Supported|
|In-place pod upgrades|Supported|Not supported|Supported|Not supported|Not supported \(coming soon\)|Not supported|
|Canary release|Supported|Not supported|Supported|Supported|Supported|Not supported|
|MaxUnavailable|Supported|Supported|Supported|Not supported|Supported|Supported|
|MaxSurge|Supported|Supported|Not supported|Not supported|Supported|Not supported|
|Customizing release sequence by using the priority or scattering strategy|Supported|Not supported|Supported|Not supported|Supported|Not supported|
|Use the lifecycle hook to manage the lifecycle of pods|Supported|Not supported|Not supported|Not supported|Not supported|Not supported|

## Install OpenKruise

You can install OpenKruise by using Container Service for Kubernetes \(ACK\) App Catalog or Helm charts. We recommend that you use Helm charts to install OpenKruise. The following is a comparison of the two installation methods:

**Note:** Before you install OpenKruise, make sure that the Kubernetes version is 1.13 or later. If you use Kubernetes 1.13 or 1.14, you must enable `CustomResourceWebhookConversion` feature-gate in kube-apiserver before you install OpenKruise.

-   Install OpenKruise with ACK App Catalog: You can install OpenKruise with one click. You do not need to use the Helm command-line interface \(CLI\). However, after you install OpenKruise, you can upgrade it only by running the `helm upgrade` command.
-   Install OpenKruise with Helm charts: This method is applicable to all Kubernetes-native clusters. If you choose this method, you can manage versions and configure parameters as needed. However, to install OpenKruise with Helm charts, you must use the CLI.

**Install OpenKruise with ACK App Catalog**

1.  On the **Alibaba Cloud Apps** tab, click **Application Management** and click **ack-kruise**.

2.  On the App Catalog - ack-kruise page, set the **Cluster**, **Namespace**, and **Release Name** parameters in the **Deploy** section, and then click **Create**.


**Install OpenKruise with Helm charts**

1.  Install the CLI of Helm. For more information about how to download the CLI, see [Helm Release](https://github.com/helm/helm/releases).

    **Note:** Make sure that the version of the Helm CLI that you install is 3.1.0 or later.

2.  Install OpenKruise.

    **Note:** If you want to configure OpenKruise, see [Install OpenKruise](https://openkruise.io/zh-cn/docs/quick_start.html).

    -   If you use Kubernetes 1.13 or 1.14, run the following command to install OpenKruise:

        ```
        Kubernetes 1.13 or 1.14
        helm install kruise https://github.com/openkruise/kruise/releases/download/v0.7.0/kruise-chart.tgz --disable-openapi-validation
        ```

    -   If you use Kubernetes 1.15 or later, run the following command to install OpenKruise:

        ```
        Kubernetes 1.15 or later
        helm install kruise https://github.com/openkruise/kruise/releases/download/v0.7.0/kruise-chart.tgz
        ```


## Use a CloneSet to deploy a stateless application

1.  Create a CloneSet.

    1.  Create a cloneset.yaml file.

        ```
        apiVersion: apps.kruise.io/v1alpha1
        kind: CloneSet
        metadata:
          name: demo-clone
        spec:
          replicas: 5
          selector:
            matchLabels:
              app: guestbook
          template: #The schema of the pod template is the same as that of a Deployment.
            metadata:
              labels:
                app: guestbook
            spec:
              affinity:
                podAntiAffinity:
                  preferredDuringSchedulingIgnoredDuringExecution:
                  - podAffinityTerm:
                      labelSelector:
                        matchExpressions:
                        - key: app
                          operator: In
                          values:
                          - guestbook
                      topologyKey: kubernetes.io/hostname
                    weight: 100
              containers:
              - name: guestbook
                image: registry.cn-hangzhou.aliyuncs.com/kruise-test/guestbook:v1
                env:
                - name: test
                  value: foo
          updateStrategy:
            type: InPlaceIfPossible     #We recommend that you perform an in-place upgrade instead of upgrading pods upon recreation.
            maxUnavailable: 20%        #A maximum of 20% pods can be unavailable during the release.
            inPlaceUpdateStrategy:
              gracePeriodSeconds: 3    #The graceful period specifies how long a pod stays in the Not-ready state before the controller upgrades the pod in place.
        ```

        -   type: specifies the upgrade strategy. The following three strategies are supported:
            -   ReCreate: The controller deletes the current pods and persistent volume claims \(PVCs\), and then creates new pods and PVCs.
            -   InPlaceIfPossible: The controller attempts to perform an in-place upgrade. If the attempt fails, the controller upgrades the pods by recreating them.
            -   InPlaceOnly: The controller is allowed to perform only in-place upgrades.
        -   maxUnavailable: The maximum number of pods that can be unavailable during the upgrade process. You can specify an absolute value or a percentage value.
        -   gracePeriodSeconds: The graceful period specifies how long a pod stays in the Not-ready state before the controller upgrades the pod in place.
    2.  Make the cloneset.yaml file effective in the ACK cluster.

        ```
        kubectl create -f ./cloneset.yaml
        ```

        Expected output:

        ```
        cloneset.apps.kruise.io/demo-clone created
        ```

2.  Query the states of the pods.

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME               READY   STATUS    RESTARTS   AGE
    demo-clone-5b9kl   1/1     Running   0          3s
    demo-clone-6xjdg   1/1     Running   0          3s
    demo-clone-bvmdj   1/1     Running   0          3s
    demo-clone-dm22s   1/1     Running   0          3s
    demo-clone-rbpg9   1/1     Running   0          3s
    ```

3.  View the CloneSet.

    ```
    kubectl get clone
    ```

    Expected output:

    ```
    NAME         DESIRED   UPDATED   UPDATED_READY   READY   TOTAL   AGE
    demo-clone   5         5         5               5       5       46s
    ```

    -   DESIRED: the expected number of pods \(spec.replicas\).
    -   UPDATED: the number of pods that are upgraded \(status.updatedReplicas\).
    -   UPDATED\_READY: the number of pods that are available after they are upgraded \(status.updatedReadyReplicas\).
    -   READY: the total number of available pods \(status.readyReplicas\).
    -   TOTAL: the total number of pods \(status.replicas\).

## Use an Advanced StatefulSet to deploy a stateful application

1.  Create an Advanced StatefulSet.

    1.  Create a statefulset.yaml file.

        ```
        apiVersion: apps.kruise.io/v1alpha1
        kind: StatefulSet
        metadata:
          name: demo-asts
        spec:
          replicas: 3
          selector:
            matchLabels:
              app: guestbook-sts
          podManagementPolicy: Parallel
          template: #The schema of the pod template is the same as that of a StatefulSet YAML.
            metadata:
              labels:
                app: guestbook-sts
            spec:
              affinity:
                podAntiAffinity:
                  preferredDuringSchedulingIgnoredDuringExecution:
                  - podAffinityTerm:
                      labelSelector:
                        matchExpressions:
                        - key: app
                          operator: In
                          values:
                          - guestbook-sts
                      topologyKey: kubernetes.io/hostname
                    weight: 100
              containers:
              - name: guestbook
                image: registry.cn-hangzhou.aliyuncs.com/kruise-test/guestbook:v1
                env:
                - name: test
                  value: foo
                volumeMounts:
                - name: log-volume
                  mountPath: /var/log
              readinessGates:
              - conditionType: InPlaceUpdateReady
              volumes:
              - name: log-volume
                emptyDir: {}
          updateStrategy:
            type: RollingUpdate
            rollingUpdate:
              podUpdatePolicy: InPlaceIfPossible #We recommend that you perform an in-place upgrade instead of upgrading pods upon recreation.
              maxUnavailable: 20% #A maximum of 20% pods can be unavailable during the release.
              inPlaceUpdateStrategy:
                gracePeriodSeconds: 3 #The graceful period specifies how long a pod stays in the Not-ready state before the controller upgrades the pod in place.
        ```

        -   type: specifies the upgrade strategy. The following three strategies are supported:
            -   ReCreate: The controller deletes the current pods and PVCs, and then creates new pods and PVCs.
            -   InPlaceIfPossible: The controller attempts to perform an in-place upgrade. If the attempt fails, the controller upgrades the pods by recreating them.
            -   InPlaceOnly: The controller is allowed to perform only in-place upgrades.
        -   maxUnavailable: The maximum number of pods that can be unavailable during the upgrade process. You can specify an absolute value or a percentage value.
        -   gracePeriodSeconds: The graceful period specifies how long a pod stays in the Not-ready state before the controller upgrades the pod in place.
    2.  Make the statefulset.yaml file effective in the ACK cluster.

        ```
         kubectl create -f ./statefulset.yaml
        ```

        Expected output:

        ```
        statefulset.apps.kruise.io/demo-asts created
        ```

2.  Query the states of the pods.

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME          READY   STATUS    RESTARTS   AGE
    demo-asts-0   1/1     Running   0          3h29m
    demo-asts-1   1/1     Running   0          3h29m
    demo-asts-2   1/1     Running   0          3h29m
    ```

3.  View the Advanced StatefulSet.

    ```
    kubectl get asts
    ```

    Expected output:

    ```
    NAME        DESIRED   CURRENT   UPDATED   READY   AGE
    demo-asts   3         3         3         3       3h30m
    ```

    -   DESIRED: the expected number of pods \(spec.replicas\).
    -   UPDATED: the number of pods that are upgraded \(status.updatedReplicas\).
    -   READY: the total number of available pods \(status.readyReplicas\).
    -   TOTAL: the total number of pods \(status.replicas\).

**Related topics**  


[OpenKruise official documentation](https://openkruise.io/zh-cn/docs/what_is_openkruise.html)

[OpenKruise Github](https://github.com/openkruise/kruise)

