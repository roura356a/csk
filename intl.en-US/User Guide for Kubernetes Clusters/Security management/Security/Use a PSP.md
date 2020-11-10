# Use a PSP

A pod security policy \(PSP\) is a Kubernetes resource that is used to validate pod creation requests against a set of rules. This feature can limit the container runtime behavior to enhance security. This topic describes how to create, manage, and associate a PSP to service accounts.

-   A managed or dedicated Kubernetes cluster is created. The cluster version is 1.14.8-aliyun.1 or later. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   An Alibaba Cloud account or a RAM user that has administrator permissions is used to log on to the Container Service for Kubernetes \(ACK\) console. For information about how to grant a RAM user administrator permissions, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

ACK allows you to create custom PSPs by specifying parameters in policy templates. This feature can limit the container runtime behavior to enhance security. To ensure security, you may consider using security engines such as AppArmor and SELinux to validate pod creation requests. To meet your security requirements, ACK enables the PSP feature. The following list describes this feature:

-   A PSP is a cluster-level Kubernetes resource model that is used to validate pod creation requests against a set of rules. To use a policy, you must install and enable the PSP admission controller for kube-apiserver. If a pod fails to meet the conditions defined in a specified policy, kube-apiserver rejects the pod creation request.
-   To use a policy, enable the PSP admission controller. For more information, see [Enable an admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#how-do-i-turn-on-an-admission-controller). You must also use RBAC to create a role that has the permission to use the policy and then bind the role to service accounts.
-   By default, ACK enables the PSP feature for new managed or dedicated clusters. In this case, you do not have to configure related parameters for kube-apiserver. This reduces the O&M workloads.
-   Assume that you have enabled the PSP feature for a cluster. To ensure the smooth running of the cluster, ACK creates a default policy named ack.privileged. This policy does not limit pod creation and is applied to all authenticated users of the cluster. However, we recommend that you create custom policies that grant users the minimum permissions. For example, you can create custom policies to prevent some users from creating privileged pods. You can also set the root file system to read-only or specify a list of host paths that can be used by hostPath volumes.

## Create a custom policy

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  On the page that appears, choose **Security** \> **PSPs**.

4.  On the PSPs tab, click **Create**. In the dialog box that appears, select a policy template and click **Use**.

    The default policy ack.privileged does not limit pod creation and is applied to all authenticated users. You can use the following templates to create custom policies:

    -   privileged-restricted: limits the creation of privileged pods. For example, you can specify whether to allow the creation of privileged pods that use the host network, host port, or host namespace.
    -   privileged-root-volumes-restricted: limits the creation of privileged pods. For example, you can specify whether to allow the creation of privileged pods that use the host network, host port, or host namespace. You can also specify the supported volume types and limit the root privileges of pods.
5.  On the Create YAML page, create a custom policy by specifying parameter values in the YAML file based on your needs. For more information about the parameters, see [Pod security policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/).

    |Parameter|Description|
    |---------|-----------|
    |privileged|Specifies whether a privileged pod can be created.|
    |hostPID and hostIPC|Specifies whether a pod can use the host namespace.|
    |hostNetwork and hostPorts|Specifies whether a pod can use the host network and host ports.|
    |volumes|The supported volume types.|
    |allowedHostPaths|A list of host paths that can be used by hostPath volumes. After you specify pathPrefix, hostPath volumes can be stored in all paths that use the specified prefix.|
    |allowedFlexVolumes|The FlexVolume drivers supported by the pod.|
    |fsGroup|The ID of the FSGroup that owns the volumes mounted to the pod.|
    |readOnlyRootFilesystem|Specifies whether the pod must run with a read-only root file system.|
    |runAsUser, runAsGroup, and supplementalGroups|The IDs of the user, primary user group, and supplemental user group of the pod containers.|
    |allowPrivilegeEscalation and defaultAllowPrivilegeEscalation|Specifies whether to allow privilege escalation for containers. The two parameters determine whether setuid binaries can be used.|
    |defaultAddCapabilities, requiredDropCapabilities, and allowedCapabilities|The Linux capabilities of pod containers.|
    |seLinux|The SELinux context of pod containers.|
    |allowedProcMountTypes|The ProcMountTypes of pod containers.|
    |annotations|The AppArmor or seccomp profile of pod containers.|
    |forbiddenSysctls and allowedUnsafeSysctls|The sysctl profile of pod containers.|

6.  Click **OK**.

    On the PSPs tab, you can view the details of the policy that you create.

    **Note:**

    -   To modify a policy, click **YAML** in the **Actions** column.
    -   To delete a policy, click **Delete** in the **Actions** column.

## Associate a custom policy with service accounts

Assume that you have enabled the PSP feature for a cluster and disassociated the default policy. To use a custom policy, associate the policy with service accounts. If no custom policy is associated, the following error occurs when the system uses the service accounts to create a pod.

```
Error from server (Forbidden): error when creating xxx: pods "xxx" is forbidden: unable to validate against any pod security policy: []
```

You can associate a custom policy with specified or all service accounts in a specified namespace.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the page that appears, choose **Security** \> **PSPs**.

5.  Click the **Association Rules** tab, and click **Add Association Rule**.

6.  In the Add Association Rule dialog box, specify **Namespace**, **Account**, and **PSP**.

7.  Click **OK**.


## Disassociate the default policy from service accounts

To enable service accounts in a namespace to use a custom policy, you must first disassociate the default policy from the service accounts.

-   After you disassociate the default policy, all service accounts in the kube-system namespace and the system:nodes user group are authorized to use the default policy. This ensures the smooth running of system components and containers.
-   All service accounts in a namespace are authorized to use the default policy if no custom policy is associated with the service accounts. This ensures that the containers in the namespace run smoothly.

**Note:**

-   For a namespace in which service accounts are associated with a custom policy:

    To ensure that the custom policy is effective, after you disassociate the default policy, the services accounts are not authorized to use the default policy. Therefore, before you disassociate the default policy, make sure that the service accounts in the namespace are associated with the desired custom policy.

-   For a namespace that is newly created:

    The system does not automatically associate the default policy with service accounts in a new namespace. After you create a namespace, we recommend that you create a custom policy and associate it with service accounts in the namespace at the earliest opportunity.


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the page that appears, choose **Security** \> **PSPs**.

5.  In the **PSP Management Guide** section, click **Disassociate Default PSP**.

6.  In the Disassociate dialog box, click **OK**.


## Enable the PSP admission controller

By default, ACK enables the PSP admission controller for new clusters. To ensure that PSPs take effect in an existing cluster, enable the admission controller for kube-apiserver.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the page that appears, choose **Security** \> **PSPs**.

5.  Enable the admission controller.

    -   In the upper-right corner of the PSPs page, ![chang](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1040105061/p180547.png) indicates that the admission controller is disabled. You can click **Change PSP State** and then click **OK** to enable it.
    -   In the upper-right corner of the PSPs page, ![admission](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1040105061/p180548.png) indicates that the admission controller is enabled.
    **Note:** If you enable the admission controller, kube-apiserver is restarted. To minimize the impact on services, we recommend that you change the status of the admission controller during off-peak hours.


## Examples

The following examples show how to create custom policies and associate them with service accounts.

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Create custom policies based on the privileged-restricted and privileged-root-volumes-restricted templates. For more information, see [Create a custom policy](#section_70j_m0v_87s).

3.  Run the following commands to create two namespaces and service accounts in the namespaces, and use RBAC to bind required roles to service accounts.

    ```
    kubectl create ns ack-all-restrictive
    kubectl create ns ack-restrictive
    
    kubectl create sa ack-dev -n ack-restrictive
    kubectl create sa ack-tester -n ack-all-restrictive
    
    kubectl -n ack-restrictive create rolebinding ack-dev-editor \
               --clusterrole=edit \
               --serviceaccount=ack-restrictive:ack-dev
    kubectl -n ack-all-restrictive create rolebinding ack-tester-editor \
               --clusterrole=edit \
               --serviceaccount=ack-all-restrictive:ack-tester          
    ```

4.  On the Association Rules tab, associate custom policies with service account ack-dev.

    1.  Click **Add Association Rule**. In the Add Association Rule dialog box, set **Namespace** to **ack-restrictive**, **Account** to **ack-dev**, and **PSP** to **privileged-restricted**. Click **OK**.

    2.  Click **Add Association Rule**. In the Add Association Rule dialog box, set **Namespace** to **ack-restrictive**, **Account** to **ack-dev**, and **PSP** to **privileged-root-volumes-restricted**. Click **OK**.

5.  Run the following commands to generate aliases for service account ack-dev in namespace ack-restrictive and service account ack-tester in namespace ack-all-restrictive.

    ```
    alias kubectl-dev='kubectl --as=system:serviceaccount:ack-restrictive:ack-dev -n ack-restrictive'
    alias kubectl-tester='kubectl --as=system:serviceaccount:ack-all-restrictive:ack-tester -n ack-all-restrictive'
    
    kubectl-dev auth can-i use  podsecuritypolicy/privileged-restricted
    Warning: resource 'podsecuritypolicies' is not namespace scoped in group 'policy'
    yes
                            
    ```

6.  Run the following command to create a privileged pod that uses the host network.

    ```
    kubectl-dev apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: privileged
    spec:
      hostNetwork: true
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
    EOF
    Error from server (Forbidden): error when retrieving current configuration of:
    Resource: "/v1, Resource=pods", GroupVersionKind: "/v1, Kind=Pod"
    Name: "privileged", Namespace: "ack-restrictive"
    Object: &{map["apiVersion":"v1" "kind":"Pod" "metadata":map["annotations":map["kubectl.kubernetes.io/last-applied-configuration":""] "name":"privileged" "namespace":"ack-restrictive"] "spec":map["containers":[map["command":["sh" "-c" "sleep 1h"] "image":"busybox" "name":"busybox"]] "hostNetwork":%! q(bool=true)]]}
    from server for: "STDIN": pods "privileged" is forbidden: User "system:serviceaccount:ack-restrictive:ack-dev" cannot get resource "pods" in API group "" in the namespace "ack-restrictive"
    ```

    An error occurs when you create the privileged pod.

7.  Run the following command to create an unprivileged pod.

    ```
    kubectl-dev apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: non-privileged
    spec:
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
    EOF
    pod/non-privileged created
    ```

    The command output indicates that the unprivileged pod is created.

8.  Run the following commands to create a pod that has root privileges and a pod that does not have root privileges.

    ```
    kubectl-tester apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: root-pod
    spec:
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
    EOF
    
    kubectl-tester apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: non-root-pod
    spec:
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
          securityContext:
            runAsUser: 1000
            runAsGroup: 3000
    EOF
    ```

9.  Run the following command to query the statuses of pods.

    The pod that has root privileges cannot be created. The custom policy associated with service account ack-tester prevents the creation of pods that have root privileges.

    Command:

    ```
    kubectl-tester get po
    ```

    The following information is displayed:

    ```
    NAME           READY   STATUS                       RESTARTS   AGE
    non-root-pod   1/1     Running                      0          115s
    root-pod       0/1     CreateContainerConfigError   0          24s
    ```

10. Run the following command to query the error details.

    Command:

    ```
    kubectl describe pod root-pod
    ```

    The following information is displayed:

    ```
    Warning  Failed     28s (x5 over 2m1s)   kubelet, cn-shenzhen.192.168.1.52  Error: container has runAsNonRoot and image will run as root
    ```


