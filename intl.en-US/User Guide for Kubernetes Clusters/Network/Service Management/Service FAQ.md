---
keyword: [FAQ about Services, SLB, FAQ]
---

# Service FAQ

This topic provides answers to some frequently asked questions about Services of Container Service for Kubernetes \(ACK\).

**FAQ about Server Load Balancer \(SLB\)**

-   [Why are no events collected during the synchronization between a Service and an SLB instance?](#section_690_vh0_1lt)
-   [How do I handle an SLB instance that remains in the Pending state?](#section_og0_e8q_4am)
-   [What do I do if the vServer groups of an SLB instance are not updated?](#section_nmr_gvy_po3)
-   [What do I do if the annotations of a Service do not take effect?](#section_slc_3sk_7vf)
-   [Why is the configuration of an SLB instance modified?](#section_g0s_3ac_8p8)
-   [Why does the cluster fail to access the IP address of the SLB instance?](#section_bp8_26q_o83)
-   [What do I do if I accidentally delete an SLB instance?](#section_s2u_6bx_q0b)
-   [If I delete a Service, is the SLB instance associated with the Service automatically deleted?](#section_apr_koj_k8v)
-   [How do I rename an SLB instance when the Cloud Controller Manager \(CCM\) version is V1.9.3.10 or earlier?](#section_61e_9ty_sgk)
-   [How does CCM calculate node weights in Local mode?](#section_7xa_hpn_yfw)
-   [How can I use SLB instances in an ACK cluster?](~~141866~~)

**FAQ about CCM upgrades**

-   [How do I troubleshoot failures to upgrade CCM?](#section_xyq_0k8_8ea)

**FAQ about using existing SLB instances**

-   [Why does the system fail to use an existing SLB instance for more than one Services?](#test)
-   [Why is no listener created when I reuse an existing SLB instance?](#section_a33_ozj_j1k)

**Others**

-   [How is session persistence implemented in Kubernetes Services?](~~149276~~)

## Why are no events collected during the synchronization between a Service and an SLB instance?

If no events are collected after you run the `kubectl -n {your-namespace} describe SVC {your-svc-name}` command, verify that your CCM version is **V1.9.3.276-g372aa98-aliyun** or later. If your CCM version is earlier than **V1.9.3.276-g372aa98-aliyun**, no events are collected during the synchronization between a Service and an SLB instance. For more information about how to view and upgrade your CCM version, see [Manually upgrade the cloud controller manager](/intl.en-US/Bulletin/[Component Upgrades] Upgrade the cloud controller manager.md).

If your CCM version is **V1.9.3.276-g372aa98-aliyun** or later, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## How do I handle an SLB instance that remains in the Pending state?

1.  Run the `kubectl -n {your-namespace} describe svc {your-svc-name}` command to view the events.

2.  Troubleshoot the errors that are reported in the events. For more information about how to troubleshoot errors that are reported in the events, see [Errors and solutions](#section_kr4_89m_2dh).

    If no errors are reported in the events, see [Why are no events collected during the synchronization between a Service and an SLB instance?](#section_690_vh0_1lt).


## What do I do if the vServer groups of an SLB instance are not updated?

1.  Run the `kubectl -n {your-namespace} describe svc {your-svc-name}` command to view the events.

2.  Troubleshoot the errors that are reported in the events. For more information about how to troubleshoot errors that are reported in the events, see [Errors and solutions](#section_kr4_89m_2dh).

    If no errors are reported in the events, see [Why are no events collected during the synchronization between a Service and an SLB instance?](#section_690_vh0_1lt).


## What do I do if the annotations of a Service do not take effect?

1.  Perform the following steps to view the errors:

    1.  Run the `kubectl -n {your-namespace} describe svc {your-svc-name}` command to view the events.

    2.  Troubleshoot the errors that are reported in the events. For more information about how to troubleshoot errors that are reported in the events, see [Errors and solutions](#section_kr4_89m_2dh).

2.  If no errors are reported, you can resolve the issue based on the following scenarios:

    -   Make sure that your CCM version meets the requirements of the annotations. For more information about the correlation between annotations and CCM versions, see [Common annotations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).
    -   On the Services page, find the Service that you want to manage and click **View in YAML** in the **Actions** column. In the panel that appears, check whether annotations are configured for the Service. If annotations are not configured for the Service, you must configure annotations for the Service.

        For more information about how to configure annotations, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).

        For more information about how to view the list of Services, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

    -   Verify that the annotations are valid.

## Why is the configuration of an SLB instance modified?

When specific conditions are met, CCM calls a declarative API to update the configuration of an SLB instance based on the Service configuration. If you modify the configurations of an SLB instance in the SLB console, CCM may overwrite the configurations. We recommend that you use annotations to configure an SLB instance. For more information about how to configure annotations for an SLB instance, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).

**Note:** If the SLB instance is created and managed by CCM, we recommend that you do not modify the configuration of the SLB instance in the SLB console. Otherwise, CCM may overwrite the configuration and the Service may be unavailable.

## Why does the system fail to use an existing SLB instance for more than one Services?

-   Check the version of CCM. If the version is earlier than v1.9.3.105-gfd4e547-aliyun, CCM cannot use existing SLB instances for more than one Services. For more information about how to view and upgrade your CCM version, see [Manually upgrade the cloud controller manager](/intl.en-US/Bulletin/[Component Upgrades] Upgrade the cloud controller manager.md).
-   Check whether the reused SLB instance is created by the cluster. The SLB instance cannot be reused if it is created by the cluster.
-   Check whether the SLB instance is used by the API server. The SLB instance cannot be reused if it is used by the API server.
-   If the SLB instance is an internal-facing SLB instance, check whether the SLB instance and the cluster are deployed in the same virtual private cloud \(VPC\). The SLB instance cannot be reused if they are deployed in different VPCs.

## Why is no listener created when I reuse an existing SLB instance?

Make sure that you set `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners` to `true` in the annotation settings. If you do not set the value to true, no listener is automatically created.

**Note:** The following list explains why CCM does not overwrite listeners of an existing SLB instance:

-   If you overwrite the listeners of an existing SLB instance that distributes network traffic to a Service, a Service interruption may occur.
-   CCM supports limited backend configurations and cannot handle complex configurations. If the vServer group requires complex configurations, you can manually create listeners in the SLB console instead of overwriting the existing listeners.

In both cases, we recommend that you do not overwrite the listeners of existing SLB instances. However, you can overwrite an existing listener if the port of the listener is no longer in use.

## How do I troubleshoot failures to upgrade CCM?

For more information about solutions to CCM upgrade failures, see[CCM upgrade failures](https://www.alibabacloud.com/help/doc-detail/164988.htm).

## Why does the cluster fail to access the IP address of the SLB instance?

For more information about why does a cluster fail to access the IP address of the SLB instance, see[Kubernetes clusters cannot access the IP address of the SLB instance](https://www.alibabacloud.com/help/doc-detail/171437.htm).

## If I delete a Service, is the SLB instance associated with the Service automatically deleted?

If the Service has the `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: {your-slb-id}` annotation which indicates that the SLB instance is reused by the Service, the SLB instance is not deleted after you delete the Service. If the SLB instance is not reused, the SLB instance is deleted when the Service is deleted.

If you change the type of the Service, for example, from LoadBalancer to NodePort, the SLB instance associated with the Service is also deleted.

## What do I do if I accidentally delete an SLB instance?

-   **Scenario 1: What do I do if I accidentally delete the SLB instance of the API server?**

    The deleted SLB instance cannot be restored. You must create a new SLB instance. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

-   **Scenario 2: What do I do if I delete the SLB instance of an Ingress?**

    Perform the following steps to recreate the SLB instance:

    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  Choose **Network** \> **Services**.
    5.  On the top of the Services page, select **kube-system** from the **Namespace** drop-down list. Then, find **nginx-ingress-lb** in the Services list and click **View in YAML** in the **Actions** column.

        If you cannot find **nginx-ingress-lb** in the Services list, use the following template to create a Service named **nginx-ingress-lb**:

        ```
        apiVersion: v1
        kind: Service
        metadata:
          labels:
            app: nginx-ingress-lb
          name: nginx-ingress-lb
          namespace: kube-system
        spec:
          externalTrafficPolicy: Local
          ports:
          - name: http
            port: 80
            protocol: TCP
            targetPort: 80
          - name: https
            port: 443
            protocol: TCP
            targetPort: 443
          selector:
            app: ingress-nginx
          type: LoadBalancer
        ```

    6.  In the Edit YAML dialog box, delete the content in the **status** field. Then, click **Update**. This way, CCM creates a new SLB instance.
-   **Scenario 3: What do I do if I delete an SLB instance that is configured to handle workloads?**
    -   If you no longer need the Service that is associated with the SLB instance, delete the Service.
    -   If you want to keep the Service, perform the following steps:
        1.  Log on to the [the ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane, choose **Services and Ingresses** \> **Services**.
        5.  On the top of the Services page, select **All Namespaces** from the **Namespace** drop-down list. Then, find the Service in the Services list and click **View in YAML** in the **Actions** column.
        6.  In the Edit YAML dialog box, delete the content in the **status** field. Then, click **Update**. This way, CCM creates a new SLB instance.

## How do I rename an SLB instance when the CCM version is V1.9.3.10 or earlier?

For CCM versions later than V1.9.3.10, a tag is automatically added to the SLB instances in the cluster. You need only to change the value if you want to rename an SLB instance. For CCM V1.9.3.10 and earlier, you must manually add a specific tag to an SLB instance if you want to rename the SLB instance.

**Note:**

-   You can rename an SLB instance by adding a tag to the instance only if the CCM version is V1.9.3.10 or earlier.
-   The Service type is LoadBalancer.

1.  Log on to a master node in an ACK cluster. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

2.  Run the `kubectl get svc -n ${namespace} ${service}` command to view the Service type and IP address of the Service.

    ![Service type](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3545359951/p44550.png)

    **Note:** Replace namespace with the cluster namespace and service with the Service name.

3.  Run the following command to create the tag that you want to add to the SLB instance:

    ```
    kubectl get svc -n ${namespace} ${service} -o jsonpath="{.metadata.uid}"|awk -F "-" '{print "kubernetes.do.not.delete: "substr("a"$1$2$3$4$5,1,32)}'
    ```

    ![tag](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3545359951/p44551.png)

4.  Log on to the [SLB console](https://slb.console.aliyun.com), select the region where the SLB instance is deployed, and then find the specified SLB instance based on the IP address that is returned in Step 2.

5.  Add the tag that is generated in Step 3 to the SLB instance. Callout 1 in the preceding figure is the tag key, and callout 2 is the tag value. For more information, see [Manage node labels](/intl.en-US/Classic Load Balancer/User Guide/Instance/Tags/Tag overview.md).


## How does CCM calculate node weights in Local mode?

In this example, pods with the app=nginx label are deployed on three ECS instances. In the following figure, when externalTrafficPolicy is set to Local, the pods provide services for external users by using Service A. The following sections describe how node weights are calculated.

![CCM2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4545359951/p103113.png)

-   **For CCM versions earlier than V1.9.3.164-g2105d2e-aliyun**

    For CCM versions that are earlier than V1.9.3.164-g2105d2e-aliyun, the following figure shows that the weight of each ECS instance in Local mode is 100. This indicates that traffic loads are evenly distributed to the ECS instances. However, the load amounts of the pods are different because the pods are unevenly deployed on the ECS instances. For example, the pod on ECS 1 takes the heaviest load and the pods on ECS 3 take the lightest load.

    ![CCM3](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4545359951/p103116.png)

-   **For CCM versions that are later than V1.9.3.164-g2105d2e-aliyun but earlier than V1.9.3.276-g372aa98-aliyun**

    For CCM versions that are later than V1.9.3.164-g2105d2e-aliyun but earlier than V1.9.3.276-g372aa98-aliyun, the node weights are calculated based on the number of pods deployed on each node, as shown in the following figure. The weights of the ECS instances are 16, 33, and 50 based on this calculation. Therefore, traffic loads are distributed to the ECS instances at the ratio of approximately 1:2:3.

    The node weight is calculated based on the following formula.

    ![Formula](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4545359951/p103112.png)

    ![ccm4](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4545359951/p103117.png)

-   **For CCM V1.9.3.276-g372aa98-aliyun and later**

    The weights of pods are slightly imbalanced due to the precision of the calculation formula. For CCM V1.9.3.276-g372aa98-aliyun and later, the weight of each node equals the number of pods deployed on the node. In the following figure, the weights of the ECS instances are 1, 2, and 3. Traffic loads are distributed to the ECS instances at the ratio of 1:2:3. This way, the pods have a more balanced load than the pods in the preceding figure.

    The node weight is calculated based on the following formula.

    ![Node weight](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4545359951/p118174.png)

    ![node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4545359951/p118175.png)


## Service errors and solutions

The following table lists solutions to errors occurred in Services.

|Error message|Description and solution|
|-------------|------------------------|
|`The backend server number has reached to the quota limit of this load balancers`|The quota of the vServer groups of the SLB instance is insufficient.

We recommend that you set externalTrafficPolicy of the SLB instance to Local \(`externalTrafficPolicy: Local`\) because the quota of vServer groups is quickly consumed in Cluster mode. If you set externalTrafficPolicy of the SLB instance to Cluster, you can specify vServers by adding tags. For more information about how to add tags in annotations to associate with vServers, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md). |
|`The backend server number has reached to the quota limit of this load balancer`|The number of vServers of the SLB instance has reached the upper limit. If multiple Services share an SLB instance, the number of vServers of each of these Services are accumulated. We recommend that you create a new SLB instance when you create a Service.|
|`The loadbalancer does not support backend servers of eni type`|Shared-resource SLB instances do not support elastic network interfaces \(ENIs\). If you want to specify an ENI as a backend server, the SLB instance that you create must be a high-performance SLB instance. Add the `annotation: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec: "slb.s1.small"` annotation to the Service. **Note:** Make sure that the annotations that you add meet the requirements of the CCM version. For more information about the correlation between annotations and CCM versions, see [Common annotations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md). |
|`alicloud: not able to find loadbalancer named [%s] in openapi, but it's defined in service.loaderbalancer.ingress. this may happen when you removed loadbalancerid annotation`|The system fails to associate a Service with the SLB instance.

-   Check whether the SLB instance exists. If the SLB instance does not exist and the Service is not used, delete the Service.
-   If the SLB instance exists, perform the following steps:
    1.  Check whether the SLB instance is manually created in the SLB console. If the SLB instance is manually created in the SLB console, reuse the SLB instance by adding the relevant annotation to it. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).
    2.  If the SLB instance is automatically created in ACK, check whether the **kubernetes.do.not.delete** label is added to the SLB instance. If the label is not added to the SLB instance, see [t1227076.md\#section\_30n\_419\_ho6](/intl.en-US/User Guide for Kubernetes Clusters/Network/FAQ about network management.md). |
|`ORDER.ARREARAGE Message: The account is arrearage.`|You have overdue payments.|
|`Status Code: 400 Code: Throttlingxxx`|A large number of API operations are called during a specific period of time and API throttling is triggered.|
|`Status Code: 400 Code: RspoolVipExist Message: there are vips associating with this vServer group.`|The listener that is associated with the vServer group cannot be deleted. Solution:

1.  Check whether the annotation of the Service contains the ID of the SLB instance, for example, `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: {your-slb-id}`.

If the annotation of the Service contains the ID of the SLB instance, it indicates that the SLB instance is reused.

2.  Log on to the SLB console and delete the listener that uses the Service port. For more information about how to delete listeners for an SLB instance, see [Configure forwarding rules](/intl.en-US/Application Load Balancer/ALB User Guide/Listeners/Configure forwarding rules.md). |
|`Status Code: 400 Code: NetworkConflict`|The reused internal-facing SLB instance and the cluster are not deployed in the same VPC. Make sure that your SLB instance and the cluster are in the same VPC.|
|`Status Code: 400 Code: VSwitchAvailableIpNotExist Message: The specified VSwitch has no available ip.`|The unused IP addresses in the current vSwitch is insufficient. You can specify another vSwitch in the same VPC by using the `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id: "${YOUR_VSWITCH_ID}"` annotation.|
|`The specified Port must be between 1 and 65535.`|`targetPort` of the STRING type is not supported in ENI mode. Set the `targetPort` field in the Service YAML to an INTEGER value.|
|`Status Code: 400 Code: ShareSlbHaltSales Message: The share instance has been discontinued.`|Shared-resource SLB instances are unavailable for purchase. By default, CCM of earlier versions creates shared-resource SLB instances. To resolve this issue, upgrade CCM. |

**Related topics**  


[Considerations for configuring a LoadBalancer type Service](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Considerations for configuring a LoadBalancer type Service.md)

[Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md)

[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Core components/Cloud Controller Manager.md)

