---
keyword: pod security policy
---

# Configure and enforce pod security policies

Pod security policy is a significant method to verify the security of pod configurations before pods are deployed. This ensures that applications run in secure pods. This topic describes how to create, manage, and associate pod security policies.

-   A managed or dedicated Kubernetes cluster is created. The cluster version is 1.14.8-aliyun.1 or later. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Log on to the Container Service for Kubernetes \(ACK\) with your Alibaba Cloud account. If you want to log on as a Resource Access Management \(RAM\) user, you must first grant the administrator permissions to the RAM user. For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).
-   If you want to manage pod security policies as a RAM user, make sure that the RAM user is granted the following permissions:

    -   `cs:DescribeClusterPSPState`: queries the status of the pod security policy feature for the cluster.
    -   `cs:DescribeServiceAccountBindingPSP`: queries the pod security policy that is associated with a specified `service account`.
    -   `cs:UpdateClusterPodSecurityPolicy`: updates a pod security policy.
    -   `cs:CreatePSPBinding`: associates a pod security policy with a specified `service account`.
    -   `cs:UnbindingServiceAccountPSP`: disassociates a pod security policy from a specified `service account`.
    For more information about custom RAM policies, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).


Kubernetes allows you to configure pod context settings to ensure that your applications run in secure pods. Security engines such as AppArmor and SELinux can verify the security of pod configurations before pods are deployed. Kubernetes allows you to verify the security of pods with pod security policies in the same way as these security engines.

-   Forced security verification: A pod security policy is a cluster-level resource of Kubernetes. Pod security policy control is implemented as an admission controller of the API server to verify the security of pod configurations before pods are deployed. If the pods do not meet the conditions defined by the pod security policy, the API server rejects the pod creation request.
-   Role-based access control \(RBAC\) bindings: To create pod security policies, you must enable the [admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#how-do-i-turn-on-an-admission-controller) for pod security policies. Then, you must create RBAC bindings to authorize service accounts to use the pod security policies that you have created.
-   Pod security policy setting: By default, the pod security policy feature is automatically enabled for managed and dedicated Kubernetes clusters.
-   Principle of least privilege: To ensure that the cluster can run as normal, the system associates the default pod security policy \(ack.privileged\) with all authenticated service accounts by default. When you enforce pod security policies, you may want to follow the principle of least privilege. For example, you may forbid specific users to deploy privileged containers, require the use of a read-only root file system, or limit the host paths that hostPath volumes are allowed to use.

## Create a pod security policy

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, choose **Security** \> **PSPs**.

4.  On the PSPs page, click **Create**. In the Select PSP Template dialog box, click **Use** for the policy template that you want to use.

    In addition to the default pod security policy that enables all pods to run in privileged mode, ACK also provides the following policy templates:

    -   privileged-restricted: limits the `privileged` mode. For example, this policy template can limit the host networks, ports, and namespaces used by privileged containers.
    -   privileged-root-volumes-restricted: limits the `privileged` mode. For example, this policy template can limit the host networks, ports, namespaces, and volume types used by privileged containers and also limit escalation to root privileges.
5.  On the Create YAML page, modify the parameters of the policy template based on the desired security level. For more information, see [Pod security policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/).

    |Parameter|Description|
    |---------|-----------|
    |privileged|Specify whether the containers in a pod can run in privileged mode.|
    |hostPID and hostIPC|Specify whether the containers can use the host namespace.|
    |hostNetwork and hostPorts|Specify whether the containers can use the host network and ports.|
    |volumes|Specify a list of volume types that are allowed.|
    |allowedHostPaths|Specify a list of host paths that can be used by hostPath volumes. The allowed host paths start with an allowed prefix specified by the pathPrefix field.|
    |allowedFlexVolumes|Specify a list of FlexVolume drivers that are allowed.|
    |fsGroup|Specify the ID of an FSGroup that owns the volumes of the pod.|
    |readOnlyRootFilesystem|Require the use of a read-only root file system.|
    |runAsUser, runAsGroup, and supplementalGroups|Specify the IDs of the user, primary user group, and supplemental user group of the containers.|
    |allowPrivilegeEscalation and defaultAllowPrivilegeEscalation|Limit escalation to root privileges \(allowPrivilegeEscalation=true\). These options limit the use of setuid binaries and prevent applications from enabling extra capabilities.|
    |defaultAddCapabilities, requiredDropCapabilities, and allowedCapabilities|Specify the Linux capabilities that are allowed.|
    |seLinux|Specify the SELinux context that is allowed.|
    |allowedProcMountTypes|Specify a list of ProcMountTypes that are allowed.|
    |annotations|Specify the AppArmor or seccomp profile that is allowed.|
    |forbiddenSysctls and allowedUnsafeSysctls|Specify the sysctl profile that is allowed.|

6.  Click **OK**.

    After the pod security policy is created, you can find it on the PSPs tab.

    **Note:**

    -   To modify a pod security policy on the PSPs tab, click **YAML** in the **Actions** column of the pod security policy.
    -   To delete a pod security policy on the PSPs tab, click **Delete** in the **Actions** column of the pod security policy.

## Associate a pod security policy with service accounts

After you enable the pod security policy feature for a cluster, you must authorize service accounts that are disassociated from the default pod security policy to use other pod security policies. Otherwise, the following error occurs when the system attempts to deploy pods with these service accounts:

```
Error from server (Forbidden): error when creating xxx: pods "xxx" is forbidden: unable to validate against any pod security policy: []
```

You can associate a pod security policy with a specified service account or all service accounts in a specified namespace.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Security** \> **PSPs**.

5.  Click the **Association Rules** tab and click **Add Association Rule**.

6.  In the Add Association Rule dialog box, specify **Namespace**, **Account**, and **PSP**.

7.  Click **OK**.


## Disassociate the default pod security policy

To make a custom pod security policy that you have associated with service accounts take effect, you must disassociate the default pod security policy.

-   When the system disassociates the default pod security policy, the system authorizes all service accounts in the kube-system namespace and the system:nodes user group to use the default pod security policy. This ensures that the system components and containers run as normal.
-   All service accounts in a namespace are authorized to use the default pod security policy if no custom pod security policy is associated with the namespace. This ensures that the containers in the namespace run as normal.

**Note:**

-   For a namespace in which service accounts are associated with a custom pod security policy:

    To make the custom pod security policy take effect, after you disassociate the default pod security policy, the services accounts are not authorized to use the default pod security policy. Therefore, before you disassociate the default pod security policy, make sure that the service accounts in the namespace are associated with the desired custom pod security policy.

-   For a namespace that is newly created:

    The system does not automatically associate the default pod security policy with service accounts in the newly created namespace. Therefore, after you create a namespace, you must create a pod security policy and associate it with service accounts in the namespace at the earliest opportunity.


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Security** \> **PSPs**.

5.  In the **PSP Management Guide** section, click **Disassociate Default PSP**.

6.  In the Disassociate message, click **OK**.


## Enable the admission controller for pod security policies

By default, ACK automatically enables the admission controller for newly created clusters. To make custom pod security policies take effect within a cluster, enable the admission controller for kube-apiserver.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Security** \> **PSPs**.

5.  Enable the admission controller.

    -   In the upper-right corner of the PSPs page, ![Disable](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7514386261/p131566.png) indicates that the admission controller is disabled. You can turn on **PSP Status** and click **OK** in the Change PSP State message to enable the admission controller.
    -   In the upper-right corner of the PSPs page, ![Enable](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7514386261/p131571.png) indicates that the admission controller is enabled.
    **Note:** After you enable the admission controller, kube-apiserver is restarted. To minimize the impacts on your workloads, we recommend that you change the status of the admission controller during off-peak hours.


## Examples

The following example shows how a pod security policy is created, associated with service accounts, and then enforced.

1.  [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

2.  Create custom pod security policies based on the privileged-restricted and privileged-root-volumes-restricted templates. For more information, see [Create a pod security policy](#section_70j_m0v_87s).

3.  Run the following commands to create two namespaces, create service accounts in the namespaces, and then create RBAC bindings for these service accounts.

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

4.  On the Association Rules tab, associate the custom pod security policies with the service accounts.

    1.  Click **Add Association Rule**. In the Add Association Rule dialog box, set **Namespace** to **ack-restrictive**, **Account** to **ack-dev**, and **PSP** to **privileged-restricted**. Then, click **OK**.

    2.  Click **Add Association Rule**. In the Add Association Rule dialog box, set **Namespace** to **ack-restrictive**, **Account** to **ack-dev**, and **PSP** to **privileged-root-volumes-restricted**. Then, click **OK**.

5.  Run the following commands to generate aliases for service account ack-dev in namespace ack-restrictive and service account ack-tester in namespace ack-all-restrictive:

    ```
    alias kubectl-dev='kubectl --as=system:serviceaccount:ack-restrictive:ack-dev -n ack-restrictive'
    alias kubectl-tester='kubectl --as=system:serviceaccount:ack-all-restrictive:ack-tester -n ack-all-restrictive'
    
    kubectl-dev auth can-i use  podsecuritypolicy/privileged-restricted
    Warning: resource 'podsecuritypolicies' is not namespace scoped in group 'policy'
    yes
                            
    ```

6.  Run the following command to create a privileged pod that uses the host network:

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
    Object: &{map["apiVersion":"v1" "kind":"Pod" "metadata":map["annotations":map["kubectl.kubernetes.io/last-applied-configuration":""] "name":"privileged" "namespace":"ack-restrictive"] "spec":map["containers":[map["command":["sh" "-c" "sleep 1h"] "image":"busybox" "name":"busybox"]] "hostNetwork":%!q(bool=true)]]}
    from server for: "STDIN": pods "privileged" is forbidden: User "system:serviceaccount:ack-restrictive:ack-dev" cannot get resource "pods" in API group "" in the namespace "ack-restrictive"
    ```

    The system fails to create the privileged pod and returns an error.

7.  Run the following command to create an unprivileged pod:

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

    The output indicates that the unprivileged pod is created.

8.  Run the following commands to create a pod that has root privileges and a pod that does not have root privileges:

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

9.  Run the following command to query the pods.

    The pod that has root privileges cannot be created. The associated custom pod security policy forbids service account ack-tester to create pods that have root privileges.

    Command:

    ```
    kubectl-tester get po
    ```

    Output:

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

    Output:

    ```
    Warning  Failed     28s (x5 over 2m1s)   kubelet, cn-shenzhen.192.168.1.52  Error: container has runAsNonRoot and image will run as root
    ```


