---
keyword: [OpenKruise, CloneSet, Advanced StatefulSet]
---

# Use OpenKruise to deploy cloud-native applications

OpenKruise is a set of standard extensions for Kubernetes. It can be used with native Kubernetes to efficiently manage application pods, sidecar containers, and image distribution. This topic describes how to use OpenKruise to deploy cloud-native applications.

A Container Service for Kubernetes \(ACK\) cluster is created and the Kubernetes version is 1.13 or later. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

OpenKruise is an open source automation engine provided by Alibaba Cloud for cloud-native applications. It is used as a deployment base to migrate the business of Alibaba Group to the cloud. OpenKruise has joined the Cloud Native Computing Foundation \(CNCF\) Sandbox project.

OpenKruise contains a variety of custom workloads. You can use the workloads to deploy and manage stateless applications, stateful applications, sidecar containers, and daemon applications. OpenKruise also supports advanced strategies such as in-place upgrades, canary releases, stream upgrades, and priority configuration.

## Component architecture

![OpenKruise](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5935748261/p296863.png)

OpenKruise is a set of standard extensions for Kubernetes and can be deployed as Kubernetes-native components in clusters. OpenKruise mainly includes the following two components.

|Component|Description|
|---------|-----------|
|Kruise-manager|Kruise-manager is a control plane component that runs controllers and webhooks. It is deployed by a Deployment in the kruise-system namespace. It uses controllers and webhooks to implement key features such as in-place upgrades and sidecar management.|
|Kruise-daemon|Kruise-daemon is deployed by a DaemonSet on every node, and manages features such as image pre-download and container restart.|

## Usage notes

OpenKruise provides controllers such as CloneSet, Advanced StatefulSet, and Advanced DaemonSet. The following section describes the features of commonly used controllers.

|Controller|Description|Star rating|
|----------|-----------|-----------|
|CloneSet|CloneSets are equivalent to Kubernetes-native Deployments. CloneSets are used to manage stateless applications. For more information, see [CloneSet](https://openkruise.io/zh-cn/docs/cloneset.html).

The fields in a CloneSet YAML file do not completely match those in a Deployment YAML file. However, CloneSets support all features of Deployments and provide more strategies.

|✩✩✩✩✩|
|Advanced StatefulSet|Advanced StatefulSets are equivalent to Kubernetes-native StatefulSets. Advanced StatefulSets are used to manage stateful applications. For more information, see [Advanced StatefulSet](https://openkruise.io/zh-cn/docs/advanced_statefulset.html).

The fields in an Advanced StatefulSet YAML file completely match those in a StatefulSet YAML file. You need only to change the value of `apiVersion` to `apps.kruise.io/v1alpha1`. In addition, you can set the `optional` field to use more release strategies, such as in-place upgrades and parallel releases.

|✩✩✩✩|
|Advanced DaemonSet|Advanced DaemonSets are equivalent to Kubernetes-native DaemonSets. Advanced DaemonSets are used to manage daemon applications. For more information, see [Advanced DaemonSet](https://openkruise.io/zh-cn/docs/advanced_daemonset.html).

The fields in an Advanced DaemonSet YAML file completely match those in a DaemonSet YAML file. You need only to change the value of `apiVersion` to `apps.kruise.io/v1alpha1`. In addition, you can set the `optional` field to use more release strategies, such as hot upgrades, canary releases, and canary releases by node label.

|✩✩✩✩|
|SidecarSet|The SidecarSet controller independently manages sidecar containers and injects sidecar containers to pods. For more information, see [SidecarSet](https://openkruise.io/zh-cn/docs/sidecarset.html).

After you define a sidecar container and a label selector in an independent custom resource \(CR\), OpenKruise injects the defined sidecar container to the pod that matches the conditions when the pod is created. You can also perform in-place upgrades for the injected sidecar container by using a SidecarSet.

|✩✩✩✩|
|UnitedDeployment|The UnitedDeployment controller manages multiple sub-workloads in different regions. For more information, see [UnitedDeployment](https://openkruise.io/zh-cn/docs/uniteddeployment.html).

The UnitedDeployment controller supports the following sub-workloads: CloneSets, StatefulSets, and Advanced StatefulSets. You can use one UnitedDeployment to manage sub-workloads in different regions and replicated pods of these sub-workloads.

|✩✩✩|

The following section compares the CloneSet, Advanced StatefulSet, and Advanced DaemonSet controllers of OpenKruise with the equivalent controllers provided by the Kubernetes community.

|Description|CloneSet VS Deployment|Advanced StatefulSet VS StatefulSet|Advanced DaemonSet VS DaemonSet|
|CloneSet|Deployment|Advanced StatefulSet|StatefulSet|Advanced DaemonSet|DaemonSet|
|-----------|----------------------|-----------------------------------|-------------------------------|
|--------|----------|--------------------|-----------|------------------|---------|
|Stream scaling|Not supported \(coming soon\)|Not supported|Not supported|Not supported|Supported|Not supported|
|Selective pod deletion|Supported|Not supported|Supported|Not supported|Not supported|Not supported|
|Upgrade pods upon recreation|Supported|Supported|Supported|Supported|Supported|Supported|
|In-place pod upgrade|Supported|Not supported|Supported|Not supported|Not supported \(coming soon\)|Not supported|
|Canary release|Supported|Not supported|Supported|Supported|Supported|Not supported|
|Maximum available pods|Supported|Supported|Supported|Not supported|Supported|Supported|
|MaxSurge|Supported|Supported|Not supported|Not supported|Supported|Not supported|
|Custom release sequence by using the priority or scatter strategy|Supported|Not supported|Supported|Not supported|Supported|Not supported|
|Pod lifecycle management by using the lifecycle hook|Supported|Not supported|Not supported|Not supported|Not supported|Not supported|

## Install OpenKruise

**Note:** Before you install OpenKruise, make sure that the Kubernetes version is 1.13 or later. If you use Kubernetes 1.13 or 1.14, you must enable the `CustomResourceWebhookConversion` feature gate in kube-apiserver before you install OpenKruise. For more information, see [Feature gates](https://kubernetes.io/zh/docs/reference/command-line-tools-reference/feature-gates/).

You can install OpenKruise through the App Catalog or Add-ons page in the ACK console. We recommend that you install OpenKruise through the Add-ons page. The following list provides a comparison of the two installation methods:

-   Install OpenKruise through the Add-ons page: You can click one button to install OpenKruise. You can also upgrade or uninstall OpenKruise based on your requirements.
-   Install OpenKruise through the App Catalog page: You can click one button to install OpenKruise. However, after OpenKruise is installed, you can upgrade it only by running the `helm upgrade` command.

**Install OpenKruise through the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, click the Manage Applications tab. In the **ack-kruise** section, click **Install**.

    In the **Note** dialog box, confirm the component information and click **OK**.


**Install OpenKruise through the App Catalog page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **Application Management** and click **ack-kruise**.

4.  On the App Catalog - ack-kruise page, set the **Cluster**, **Namespace**, and **Release Name** parameters in the **Deploy** section, and then click **Create**.


## Use a CloneSet to deploy a stateless application

1.  Create a CloneSet.

    1.  Create a file named cloneset.yaml and copy the following code to the file.

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
          template: #The schema of the pod template is the same as that in the definition of a Deployment. 
            metadata:
              labels:
                app: guestbook
            spec:
              containers:
              - name: guestbook
                image: registry.cn-hangzhou.aliyuncs.com/kruise-test/guestbook:v1
                env:
                - name: test
                  value: foo
          updateStrategy:
            type: InPlaceIfPossible     #We recommend that you perform in-place upgrades instead of upgrading pods upon recreation. 
            maxUnavailable: 20%        #A maximum of 20% pods can be unavailable during the release. 
            inPlaceUpdateStrategy:
              gracePeriodSeconds: 3  #The grace period specifies how long a pod stays in the Not-ready state before the controller performs an in-place upgrade on the pod. 
        ```

        -   type: specifies the upgrade strategy. The following three strategies are supported:
            -   ReCreate: The controller deletes the current pods and persistent volume claims \(PVCs\), and then creates new pods and PVCs.
            -   InPlaceIfPossible: The controller attempts to perform an in-place upgrade. If the attempt fails, the controller upgrades the pods by recreating them.
            -   InPlaceOnly: The controller is allowed to perform only in-place upgrades.
        -   maxUnavailable: The maximum number of pods that can be unavailable during the upgrade process. You can specify an absolute value or a percentage value.
        -   gracePeriodSeconds: The grace period specifies how long a pod stays in the Not-ready state before the controller performs an in-place upgrade on the pod.
    2.  Make the cloneset.yaml file effective in the ACK cluster.

        ```
        kubectl create -f ./cloneset.yaml
        ```

        Expected output:

        ```
        cloneset.apps.kruise.io/demo-clone created
        ```

2.  Query the status of the pods.

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

3.  Query the CloneSet.

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
    -   UPDATED\_READY: the number of pods that are available after the pods are upgraded \(status.updatedReadyReplicas\).
    -   READY: the total number of available pods \(status.readyReplicas\).
    -   TOTAL: the total number of pods \(status.replicas\).

## Use an Advanced StatefulSet to deploy a stateful application

1.  Create an Advanced StatefulSet.

    1.  Create a file named statefulset.yaml and copy the following code to the file:

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
          template: #The schema of the pod template is the same as that in the definition of a Deployment. 
            metadata:
              labels:
                app: guestbook-sts
            spec:
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
              podUpdatePolicy: InPlaceIfPossible     #We recommend that you perform in-place upgrades instead of upgrading pods upon recreation. 
              maxUnavailable: 20%        #A maximum of 20% pods can be unavailable during the release. 
              inPlaceUpdateStrategy:
                gracePeriodSeconds: 3  #The grace period specifies how long a pod stays in the Not-ready state before the controller performs an in-place upgrade on the pod. 
        ```

        -   type: specifies the upgrade strategy. The following three strategies are supported:
            -   ReCreate: The controller deletes the current pods and PVCs, and then creates new pods and PVCs.
            -   InPlaceIfPossible: The controller attempts to perform an in-place upgrade. If the attempt fails, the controller upgrades the pods by recreating them.
            -   InPlaceOnly: The controller is allowed to perform only in-place upgrades.
        -   maxUnavailable: The maximum number of pods that can be unavailable during the upgrade process. You can specify an absolute value or a percentage value.
        -   gracePeriodSeconds: The grace period specifies how long a pod stays in the Not-ready state before the controller performs an in-place upgrade on the pod.
    2.  Make the statefulset.yaml file effective in the ACK cluster.

        ```
         kubectl create -f ./statefulset.yaml
        ```

        Expected output:

        ```
        statefulset.apps.kruise.io/demo-asts created
        ```

2.  Query the status of the pods.

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

3.  Query the Advanced StatefulSet.

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

