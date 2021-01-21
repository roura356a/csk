# Considerations for configuring a LoadBalancer type Service

When you specify `Type=LoadBalancer` for a Service, Cloud Controller Manager \(CCM\) creates and configures Server Load Balancer \(SLB\) resources for the Service, including SLB instances, listeners, and VServer groups. This topic describes the considerations for configuring a LoadBalancer type Service and the policies that are used by CCM to update SLB resources.

## Policies that are used by CCM to update SLB resources

Container Service for Kubernetes \(ACK\) allows you to specify an existing SLB instance for a Service. You can also use CCM to automatically create one for the Service. The two methods use different policies to update SLB resources. The following table describes the differences.

|Resource object|Existing SLB instance|SLB instance created and managed by CCM|
|---------------|---------------------|---------------------------------------|
|SLB|Use the following annotation to specify an existing SLB instance for a Service: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id`-   CCM uses the specified SLB instance to enable load balancing. You can use other annotations to configure the SLB instance. CCM automatically creates VServer groups for the instance.
-   When the Service is deleted, CCM does not delete the existing SLB instance that is specified in the annotation.

|-   CCM automatically creates, configures, and manages SLB resources based on the Service configuration, including the SLB instance, listeners, and VServer groups.
-   When the Service is deleted, CCM deletes the created SLB instance. |
|Listener|Use the following annotation to configure listeners: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners:`.-   If you set the annotation to false, CCM does not configure or manage listeners for the SLB instance.
-   If you set the annotation to true, CCM configures and manages listeners for the SLB instance based on the Service configuration. If the SLB instance has existing listeners, CCM creates new listeners to replace the existing ones.

|CCM configures listeners for the SLB instance based on the Service configuration.|
|VServer group|When the endpoint of an Elastic Compute Service \(ECS\) instance in a VServer group for a Service changes or the cluster nodes are changed, CCM updates the VServer groups.-   The policies for updating VServer groups vary based on the mode of the Service.
    -   If `spec.externalTrafficPolicy = Cluster` is specified for a Service, CCM adds all cluster nodes to the VServer groups of the SLB instance. If node labels are specified in the Service configuration, CCM adds cluster nodes that have the specified labels to the VServer groups of the SLB instance.

**Note:** SLB limits the number of VServer groups to which an ECS instance can be added. If a Service is in Cluster mode, the quota is consumed at a high rate. When the quota is used up, Service reconciliation fails. To fix this issue, set Local mode for a Service.

    -   If `spec.externalTrafficPolicy = Local` is specified for a Service, CCM adds only the nodes where the pods that are related to the Service are deployed to the VServer groups of the SLB instance. This can reduce the consumption rate of the resources. Source IP addresses can also be retained in Layer 4 load balancing.
-   CCM does not add master nodes of a cluster to the VServer groups of an SLB instance.
-   Assume that you have run the `kubectl drain` command to remove a node from a cluster, or run the `kubectl cordon` command to mark a node as unschedulable. By default, CCM does not remove such a node from VServer groups of an SLB instance. To remove such a node, set annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend` to `on`.

**Note:** By default, CCM versions earlier than V1.9.3.164-g2105d2e-aliyun remove drained nodes or unschedulable nodes from the backend of the SLB instance. |

## Considerations for reusing an existing SLB instance

-   **Before you reuse an existing SLB instance, check whether the instance meets the following requirements:**
    -   The SLB instance that you want to reuse is created in the SLB console. You cannot reuse an SLB instance that is created by CCM.
    -   If you want to reuse an internal-facing SLB instance, the SLB instance and the cluster must be deployed in the same virtual private cloud \(VPC\).
-   CCM configures SLB instances only for `LoadBalancer` type Services.

    **Note:** If you change `Type=LoadBalancer` to `Type! =LoadBalancer` for a Service, CCM deletes the configuration of the SLB instance from the Service. In this case, you cannot access the Service by using the SLB instance.

-   When specific conditions are met, CCM uses a declarative API to automatically update the configuration of an SLB instance based on the Service configuration. If you set annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners:` to `true` for a Service, CCM may overwrite the listener configuration modifications that are made in the SLB console.

    **Note:** If the SLB instance is created or managed by CCM, we recommend that you do not modify the configuration of an SLB instance in the SLB console. Otherwise, CCM may overwrite the configuration and the Service may be unavailable.


## Considerations for using CCM to configure an SLB instance

-   CCM configures SLB instances only for `LoadBalancer` type Services.

    **Note:** If you change `Type=LoadBalancer` to `Type! =LoadBalancer` for a Service, CCM deletes the SLB instance that CCM previously created for the Service.

-   When specific conditions are met, CCM uses a declarative API to automatically update the configuration of an SLB instance based on the Service configuration. CCM may overwrite the listener configuration modifications that are made in the SLB console.

    **Note:** If the SLB instance is created or managed by CCM, we recommend that you do not modify the configuration of an SLB instance in the SLB console. Otherwise, CCM may overwrite the configuration and the Service may be unavailable.


## Resource quotas

**VPC**

-   A node in a cluster is mapped to a route entry in a route table. By default, each route table for a VPC contains a maximum of 48 entries. If the number of nodes in a cluster exceeds 48, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    **Note:** In the ticket, describe your request to modify parameter `vpc_quota_route_entrys_num`. After the request is accepted, the maximum number of custom entries that can be created in one route table is increased.

-   For more information about VPC resource quotas, see [Limits](/intl.en-US/Product Introduction/Limits.md).

    To query the VPC resource quotas, go to the [Quota Management page in the VPC console](https://vpc.console.aliyun.com/quota%EF%BC%89).


**SLB**

-   CCM creates an SLB instance for a `LoadBalancer` type Service. By default, you can retain a maximum of 60 SLB instances under each account. To create more SLB instances, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    **Note:** In the ticket, describe your request to modify parameter `slb_quota_instances_num`. After the request is accepted, the maximum number of SLB instances that can be retained under your account is increased.

-   CCM adds ECS instances to the VServer groups of an SLB instance based on the Service configuration.
    -   By default, an ECS instance can be added to a maximum of 50 VServer groups. To add the ECS instance to more VServer groups, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** In the ticket, describe your request to modify parameter `slb_quota_backendservers_num`. After the request is accepted, the maximum number of VServer groups to which an ECS instance can be added is increased.

    -   By default, a maximum of 200 backend servers can be added to an SLB instance. To add more backend servers, submit a ticket.

        **Note:** In the ticket, describe your request to modify parameter `slb_quota_backendservers_num`. After the request is accepted, the maximum number of backend servers that can be added to an SLB instance is increased.

-   CCM creates listeners based on the ports that are specified for a Service. By default, a maximum of 50 listeners can be created for an SLB instance. To create more listeners, submit a ticket.

    **Note:** In the ticket, describe your request to modify parameter `slb_quota_listeners_num`. After the request is accepted, the maximum number of listeners that can be created for an SLB instance is increased.

-   For more information about SLB resource quotas, see [Limits](/intl.en-US/Classic Load Balancer/User Guide/Limits/Limits.md).

    To query the SLB resource quotas, go to the [Quota Management page in the SLB console](https://slbnew.console.aliyun.com/slb/quota).


