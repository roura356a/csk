# Considerations for configuring a LoadBalancer service

When you create a `LoadBalancer` service, Cloud Controller Manager \(CCM\) creates and configures Server Load Balancer \(SLB\) resources for the service, including SLB instances, listeners, and VServer groups. This topic describes the considerations for configuring a LoadBalancer service and the policies used by CCM to update SLB resources.

## Policies used by CCM to update SLB resources

Container Service for Kubernetes \(ACK\) provides the following methods to enable load balancing for a service: Specify an existing SLB instance. CCM creates an SLB instance. The two methods use different policies to update SLB resources. The following table lists the differences.

|Resource object|Existing SLB instance|SLB instance created and managed by CCM|
|---------------|---------------------|---------------------------------------|
|SLB|Use the following annotation to specify an existing SLB instance for a service: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id`-   CCM uses the existing SLB instance to enable load balancing. You can use other annotations to configure the SLB instance. CCM automatically creates VServer groups for the instance.
-   When the service is deleted, CCM does not delete the existing SLB instance that you specify by using the annotation.

|-   CCM automatically creates and configures SLB resources based on the service configurations, including the SLB instance, listeners, and VServer groups. CCM also manages these resources.
-   When the service is deleted, CCM deletes the SLB instance. |
|Listeners|Use the following annotation to configure listeners: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners`-   If you set the annotation to false, CCM does not configure or manage listeners for the SLB instance.
-   If you set the annotation to true, CCM configures and manages listeners for the SLB instance based on the service configurations. If the SLB instance has existing listeners, CCM creates new listeners to replace the existing listeners.

|CCM configures listeners for the SLB instance based on the service configurations.|
|VServer groups|If the endpoint of an Elastic Compute Service \(ECS\) instance in a VServer group of a service changes or the cluster nodes change, CCM updates the VServer groups.-   The policies for updating VServer groups vary depending on the mode of the service.
    -   If `externalTrafficPolicy` is set to Cluster for a service, CCM adds all cluster nodes to the VServer groups of the SLB instance. If node labels are specified in the service configuration, CCM adds cluster nodes that have the specified labels to the VServer groups of the SLB instance.

**Note:** SLB limits the number of VServer groups to which an ECS instance can be added. If a service is in Cluster mode, the quota is consumed at a high rate. When the quota is used up, service reconciliation fails. To resolve this issue, set externalTrafficPolicy to Local for a service.

    -   If `externalTrafficPolicy` is set to Local for a service, CCM adds only the nodes where the pods of the service are deployed to the VServer groups of the SLB instance. This can reduce the consumption rate of the resources. Source IP addresses can also be retained in layer 4 load balancing.
-   CCM does not add master nodes of a cluster to the VServer groups of an SLB instance.
-   Assume that you have run the `kubectl drain` command to remove a node from a cluster, or run the `kubectl cordon` command to mark a node as unschedulable. By default, CCM does not remove such a node from VServer groups of an SLB instance. To remove such a node, set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend` to `on`. |

## Considerations for reusing an existing SLB instance

-   **Before you reuse an existing SLB instance, check whether the instance meets the following requirements:**
    -   The SLB instance that you want to reuse is created in the SLB console. You cannot reuse SLB instances created by CCM.
    -   If you want to reuse an internal SLB instance, the SLB instance and the cluster must be deployed in the same virtual private cloud \(VPC\).
-   CCM configures SLB instances for `LoadBalancer` services only.

    **Note:** If you change a service of the `LoadBalancer` type to another service type, CCM deletes the configurations of the SLB instance from the service. In this case, you cannot access the service through the SLB instance.

-   If specific conditions are met, CCM uses a declarative API to automatically update the configurations of an SLB instance based on the service configurations. Assume that you have set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners` to `true` and modified the listener configurations in the SLB console. CCM may overwrite the listener configurations.

    **Note:** We recommend that you do not modify the configurations of an SLB instance in the SLB console if the SLB instance is created or managed by CCM. Otherwise, CCM may overwrite the configurations and the service may be unavailable.


## Considerations for using CCM to configure an SLB instance

-   CCM configures SLB instances for `LoadBalancer` services only.

    **Note:** If you change a service of the `LoadBalancer` type to another service type, CCM deletes the SLB instance that CCM creates for the service.

-   If specific conditions are met, CCM uses a declarative API to automatically update the configurations of an SLB instance based on the service configurations. If you modify the configurations of an SLB instance in the SLB console, CCM may overwrite the configurations.

    **Note:** We recommend that you do not modify the configurations of an SLB instance in the SLB console if the SLB instance is created or managed by CCM. Otherwise, CCM may overwrite the configurations and the service may be unavailable.


## Resource quotas

**VPC**

-   A node in a cluster is mapped to a route entry of a route table. By default, each route table for a VPC contains a maximum of 48 entries. If the number of nodes in a cluster exceeds 48, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    **Note:** In the ticket, describe your request to modify the parameter `vpc_quota_route_entrys_num`. After the request is accepted, the maximum number of custom entries that can be created in one route table increases.

-   For more information about VPC resource quotas, see [Limits](/intl.en-US/Product Introduction/Limits.md).

    To query the VPC resource quotas, go to the [Quota Management page in the VPC console](https://vpc.console.aliyun.com/quota%EF%BC%89).


**SLB**

-   If the type of a service is `LoadBalancer`, CCM creates an SLB instance for the service. By default, you can retain a maximum of 60 SLB instances under each account. To create more SLB instances, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    **Note:** In the ticket, describe your request to modify the parameter `slb_quota_instances_num`. After the request is accepted, the maximum number of SLB instances that can be retained under each account increases.

-   CCM adds ECS instances to the VServer groups of an SLB instance based on the service configurations.
    -   By default, an ECS instance can be added to a maximum of 50 VServer groups. To add the ECS instance to more VServer groups, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** In the ticket, describe your request to modify the parameter `slb_quota_backendservers_num`. After the request is accepted, the maximum number of VServer groups to which an ECS instance can be added increases.

    -   By default, a maximum of 200 backend servers can be added to an SLB instance. To add more backend servers, submit a ticket.

        **Note:** In the ticket, describe your request to modify the parameter `slb_quota_backendservers_num`. After the request is accepted, the maximum number of backend servers that can be added to an SLB instance increases.

-   CCM creates listeners based on the ports specified for a service. By default, a maximum of 50 listeners can be created for an SLB instance. To create more listeners, submit a ticket.

    **Note:** In the ticket, describe your request to modify the parameter `slb_quota_listeners_num`. After the request is accepted, the maximum number of listeners that can be created for an SLB instance increases.

-   For more information about SLB resource quotas, see [Limits](/intl.en-US/User Guide/Limits/Limits.md).

    To query the SLB resource quotas, go to the [Quota Management page in the SLB console](https://slbnew.console.aliyun.com/slb/quota).


