---
keyword: [grant roles to a service account, default roles, role permissions]
---

# ACK default roles

When you activate Container Service for Kubernetes \(ACK\), you must grant default roles to a service account. Then, the service account can be used to call services, such as Elastic Compute Service \(ECS\), Object Storage Service \(OSS\), Apsara File Storage NAS, and Server Load Balancer \(SLB\), create clusters, and save cluster logs. This topic describes the permissions of the ACK default roles.

## Role permissions

This topic describes the permissions of the following roles:

-   [AliyunCSManagedLogRole](#section_q0d_zoc_k5u)
-   [AliyunCSManagedCmsRole](#section_sdf_auf_nsx)
-   [AliyunCSManagedCsiRole](#section_ot3_h1u_t4i)
-   [AliyunCSManagedVKRole](#section_1np_fxp_gl4)
-   [AliyunCSClusterRole](#section_ogz_1ou_4sv)
-   [AliyunCSServerlessKubernetesRole](#section_6ht_wqj_yms)
-   [AliyunCSKubernetesAuditRole](#section_rf2_r4h_mip)
-   [AliyunCSManagedNetworkRole](#section_2xh_x0c_se3)
-   [AliyunCSDefaultRole](#section_ju1_tsb_brp)
-   [AliyunCSManagedKubernetesRole](#section_j4k_173_ejk)
-   [AliyunCSManagedArmsRole](#section_s8z_f50_zrm)

## AliyunCSManagedLogRole

The Logtail component of ACK uses AliyunCSManagedLogRole to access resources of other cloud services.

|Permission|Description|
|----------|-----------|
|log:CreateProject|Creates a project.|
|log:GetProject|Queries a project by name.|
|log:DeleteProject|Deletes a specified project.|
|log:CreateLogStore|Creates a Logstore in a project.|
|log:GetLogStore|Queries the attributes of a Logstore.|
|log:UpdateLogStore|Updates the attributes of a Logstore.|
|log:DeleteLogStore|Deletes a Logstore.|
|log:CreateConfig|Creates a log collection configuration.|
|log:UpdateConfig|Updates a log collection configuration.|
|log:GetConfig|Queries the details of a log collection configuration.|
|log:DeleteConfig|Deletes a specified log collection configuration.|
|log:CreateMachineGroup|Creates a machine group to apply log collection configurations.|
|log:UpdateMachineGroup|Updates the information of a machine group.|
|log:GetMachineGroup|Queries the information of a specified machine group.|
|log:DeleteMachineGroup|Deletes a machine group.|
|log:ApplyConfigToGroup|Applies a log collection configuration to a machine group.|
|log:GetAppliedMachineGroups|Queries the machines to which a log collection configuration is applied in a machine group.|
|log:GetAppliedConfigs|Queries the configurations that are applied to a machine group.|
|log:RemoveConfigFromMachineGroup|Removes a configuration from a machine group.|
|log:CreateIndex|Creates an index for a specified Logstore.|
|log:GetIndex|Queries the index of a specified Logstore.|
|log:UpdateIndex|Updates the index of a specified Logstore.|
|log:DeleteIndex|Deletes the index of a specified Logstore.|
|log:CreateSavedSearch|Creates a saved search.|
|log:GetSavedSearch|Queries a specified saved search.|
|log:UpdateSavedSearch|Updates a saved search.|
|log:DeleteSavedSearch|Deletes a saved search.|
|log:CreateDashboard|Creates a dashboard.|
|log:GetDashboard|Queries a specified dashboard.|
|log:UpdateDashboard|Updates a dashboard.|
|log:DeleteDashboard|Deletes a dashboard.|
|log:CreateJob|Creates a job. For example, create an alert or a subscription.|
|log:GetJob|Queries a job.|
|log:DeleteJob|Deletes a job.|
|log:UpdateJob|Updates a job.|
|log:PostLogStoreLogs|Adds logs to a specified Logstore.|
|log:CreateSortedSubStore|Creates a sorted sub-Logstore.|
|log:GetSortedSubStore|Queries a sorted sub-Logstore.|
|log:ListSortedSubStore|Lists sorted sub-Logstores.|
|log:UpdateSortedSubStore|Updates a sorted sub-Logstore.|
|log:DeleteSortedSubStore|Deletes a sorted sub-Logstore.|
|log:CreateApp|Creates applications, such as Cost Manager and Log Audit Service.|
|log:UpdateApp|Updates applications, such as Cost Manager and Log Audit Service.|
|log:GetApp|Queries applications, such as Cost Manager and Log Audit Service.|
|log:DeleteApp|Deletes applications, such as Cost Manager and Log Audit Service.|
|cs:DescribeTemplates|Queries container templates.|
|cs:DescribeTemplateAttribute|Queries the attributes of a container template.|

## AliyunCSManagedCmsRole

The Cloud Monitor component of an ACK cluster uses AliyunCSManagedCmsRole to access resources of other cloud services.

|Permission|Description|
|----------|-----------|
|cms:DescribeMonitorGroups|Queries application groups.|
|cms:DescribeMonitorGroupInstances|Queries the resources in a specified application group.|
|cms:CreateMonitorGroup|Creates an application group.|
|cms:DeleteMonitorGroup|Deletes a specified application group.|
|cms:ModifyMonitorGroupInstances|Modifies the resources in an application group.|
|cms:CreateMonitorGroupInstances|Adds resources to an application group.|
|cms:DeleteMonitorGroupInstances|Deletes resources from an application group.|
|cms:TaskConfigCreate|Creates a monitoring job configuration.|
|cms:TaskConfigList|Lists monitoring job configurations.|
|cms:DescribeMetricList|Queries the time series metrics of a cloud service in a specified period.|
|cs:DescribeMonitorToken|Queries the token that is required to use the Cloud Monitor component.|
|ahas:GetSentinelAppSumMetric|Queries the metrics that are monitored by the AHAS Sentinel application.|
|log:GetLogStoreLogs|Queries logs in a Logstore.|
|slb:DescribeMetricList|Queries the time series metrics of a cloud service in a specified period.|
|sls:GetLogs|Queries logs in a Logstore of a specified project in Log Service.|
|sls:PutLogs|Updates logs in a Logstore of a specified project in Log Service.|

## AliyunCSManagedCsiRole

The volume plug-in of an ACK cluster uses AliyunCSManagedCsiRole to access resources of other cloud services.

-   Permissions on ECS resources

    |Permission|Description|
    |----------|-----------|
    |ecs:AttachDisk|Attaches a pay-as-you-go data disk or a system disk to an ECS instance.|
    |ecs:DetachDisk|Detaches a pay-as-you-go disk from an ECS instance.|
    |ecs:DescribeDisks|Queries one or more disks that you have created and local disks.|
    |ecs:CreateDisk|Creates a pay-as-you-go or subscription data disk.|
    |ecs:ResizeDisk|Expands a disk. You can expand a system disk or a data disk.|
    |ecs:CreateSnapshot|Creates a snapshot for a disk.|
    |ecs:DeleteSnapshot|Deletes a specified snapshot. If you call this operation to delete a snapshot that is being created, the snapshot creation task is canceled.|
    |ecs:CreateAutoSnapshotPolicy|Creates an automatic snapshot policy.|
    |ecs:ApplyAutoSnapshotPolicy|Attaches an automatic snapshot policy to one or more disks.|
    |ecs:CancelAutoSnapshotPolicy|Detaches an automatic snapshot policy from one or more disks.|
    |ecs:DeleteAutoSnapshotPolicy|Deletes an automatic snapshot policy.|
    |ecs:DescribeAutoSnapshotPolicyEX|Queries automatic snapshot policies that you have created.|
    |ecs:ModifyAutoSnapshotPolicyEx|Modifies an automatic snapshot policy.|
    |ecs:AddTags|Attaches tags to an ECS instance.|
    |ecs:DescribeTags|Queries tags of an ECS instance.|
    |ecs:DescribeSnapshots|Queries all the snapshots of an ECS instance or a disk.|
    |ecs:ListTagResources|Queries tags that are attached to one or more ECS instances.|
    |ecs:TagResources|Creates tags and attaches the tags to a specified group of ECS instances.|
    |ecs:UntagResources|Detaches tags from a specified group of ECS instances and deletes the tags.|
    |ecs:ModifyDiskSpec|Upgrades the performance level of an enhanced SSD.|
    |ecs:CreateSnapshot|Creates a snapshot for a disk.|
    |ecs:DeleteDisk|Releases a pay-as-you-go data disk.|
    |ecs:DescribeInstanceAttribute|Queries all attributes of an ECS instance.|
    |ecs:DescribeInstances|Queries the information of one or more ECS instances.|

-   Permissions on NAS file systems

    |Permission|Description|
    |----------|-----------|
    |nas:DescribeFileSystems|Queries the descriptions of file systems.|
    |nas:DescribeMountTargets|Queries the descriptions of mount targets.|
    |nas:AddTags|Attaches one or more tags to a file system or overwrites the tags.|
    |nas:DescribeTags|Queries existing tags.|
    |nas:RemoveTags|Detaches one or more tags from a file system.|
    |nas:CreateFileSystem|Creates a file system.|
    |nas:DeleteFileSystem|Deletes a file system.|
    |nas:DescribeFileSystems|Queries the descriptions of file systems.|
    |nas:ModifyFileSystem|Modifies the information of file systems.|
    |nas:CreateMountTarget|Creates a mount target.|
    |nas:DeleteMountTarget|Deletes a mount target.|
    |nas:DescribeMountTargets|Queries the descriptions of mount targets.|
    |nas:ModifyMountTarget|Modifies the information of mount targets.|


## AliyunCSManagedVKRole

The Virtual Kubelet component of an ACK cluster uses AliyunCSManagedVKRole to access resources of other cloud services.

-   Permissions on VPC resources

    |Permission|Description|
    |----------|-----------|
    |vpc:DescribeVSwitches|Queries existing VSwitches.|
    |vpc:DescribeVpcs|Queries existing VPCs.|
    |vpc:AssociateEipAddress|Binds an elastic IP address \(EIP\) to a cloud service instance in the same region.|
    |vpc:DescribeEipAddresses|Queries the EIPs that you create in a specified region.|
    |vpc:AllocateEipAddress|Applies for an EIP.|
    |vpc:ReleaseEipAddress|Releases a specified EIP.|

-   Permissions on ECS resources

    |Permission|Description|
    |----------|-----------|
    |ecs:DescribeSecurityGroups|Queries the basic information of the security groups that you create.|
    |ecs:CreateNetworkInterface|Creates an elastic network interface \(ENI\).|
    |ecs:CreateNetworkInterfacePermission|Grants permissions to create an ENI.|
    |ecs:DescribeNetworkInterfaces|Queries ENIs.|
    |ecs:AttachNetworkInterface|Attaches an ENI to a VPC-connected ECS instance.|
    |ecs:DetachNetworkInterface|Detaches an ENI from an ECS instance.|
    |ecs:DeleteNetworkInterface|Deletes an ENI.|
    |ecs:DeleteNetworkInterfacePermission|Grants permissions to delete an ENI.|

-   Permissions on Alibaba Cloud DNS PrivateZone resources

    |Permission|Description|
    |----------|-----------|
    |pvtz:AddZone|Creates a private zone.|
    |pvtz:DeleteZone|Deletes a private zone.|
    |pvtz:DescribeZones|Queries private zones.|
    |pvtz:DescribeZoneInfo|Queries the information of a specified private zone.|
    |pvtz:BindZoneVpc|Binds a private zone to a VPC or unbinds a private zone from a VPC.|
    |pvtz:AddZoneRecord|Adds a DNS record to a private zone.|
    |pvtz:DeleteZoneRecord|Deletes a DNS record.|
    |pvtz:DeleteZoneRecordsByRR|Deletes DNS records.|
    |pvtz:DescribeZoneRecordsByRR|Queries DNS records.|
    |pvtz:DescribeZoneRecords|Queries DNS records.|

-   Permissions on elastic container instances \(ECIs\)

    |Permission|Description|
    |----------|-----------|
    |eci:CreateContainerGroup|Creates a pod.|
    |eci:DeleteContainerGroup|Deletes a pod.|
    |eci:DescribeContainerGroups|Queries the information of multiple pods.|
    |eci:DescribeContainerLog|Queries the logs of a pod.|
    |eci:UpdateContainerGroup|Updates a pod.|
    |eci:UpdateContainerGroupByTemplate|Updates an ECI by template.|
    |eci:CreateContainerGroupFromTemplate|Creates an ECI by template.|
    |eci:RestartContainerGroup|Restarts an ECI.|
    |eci:ExportContainerGroupTemplate|Exports an ECI template.|
    |eci:DescribeContainerGroupMetric|Queries the monitoring data of an ECI.|
    |eci:DescribeMultiContainerGroupMetric|Queries the monitoring data of multiple pods.|
    |eci:ExecContainerCommand|Runs commands on a container.|
    |eci:CreateImageCache|Creates an image cache.|
    |eci:DescribeImageCaches|Queries the information of image caches.|
    |eci:DeleteImageCache|Deletes an image cache.|


## AliyunCSClusterRole

When the applications are running, an ACK cluster uses AliyunCSClusterRole to access resources of other cloud services.

-   Permissions on ECS resources

    |Permission|Description|
    |----------|-----------|
    |ecs:Describe\*|Queries ECS resources.|

-   Permissions on OSS resources

    |Permission|Description|
    |----------|-----------|
    |oss:PutObject|Uploads a file or a folder.|
    |oss:GetObject|Obtains a file or a folder.|
    |oss:ListObjects|Queries files.|

-   Permissions on Cloud Monitor Service \(CMS\)

    |Permission|Description|
    |----------|-----------|
    |cms:List\*|Lists permissions on CMS resources.|
    |cms:Get\*|Obtains permissions on CMS resources.|
    |cms:UpdateAlert|Updates an alert.|
    |cms:CreateAlert|Creates an alert.|
    |cms:DeleteAlert|Deletes an alert.|
    |cms:UpdateDimensions|Updates monitoring metrics configurations.|
    |cms:CreateDimensions|Creates monitoring metrics configurations.|
    |cms:DeleteDimensions|Deletes monitoring metrics configurations.|
    |cms:SendAlarm|Sends a monitoring alert.|
    |cms:CreateProject|Creates a monitoring project.|
    |cms:DeleteProject|Deletes a monitoring project.|
    |cms:UpdateProject|Updates a monitoring project.|
    |cms:QueryAlarm|Queries a monitoring alert.|
    |cms:ListAlarm|Lists monitoring alerts.|
    |cms:CreateAlarm|Creates a monitoring alert.|
    |cms:DeleteAlarm|Deletes a monitoring alert.|
    |cms:UpdateAlarm|Updates a monitoring alert.|

-   Permissions on Server Load Balancer \(SLB\) resources

    |Permission|Description|
    |----------|-----------|
    |slb:Describe\*|Queries the information about an SLB instance.|
    |slb:CreateLoadBalancer|Creates an SLB instance.|
    |slb:DeleteLoadBalancer|Deletes an SLB instance.|
    |slb:RemoveBackendServers|Unbinds backend servers from an SLB instance.|
    |slb:StartLoadBalancerListener|Starts a specified listener.|
    |slb:StopLoadBalancerListener|Stops a specified listener.|
    |slb:CreateLoadBalancerTCPListener|Creates a TCP listener for an SLB instance.|
    |slb:AddBackendServers\*|Adds backend servers to an SLB instance.|
    |slb:DeleteLoadBalancerListener|Deletes an SLB instance.|
    |slb:CreateVServerGroup|Creates a VServer group and adds backend servers to the VServer group.|
    |slb:ModifyVServerGroupBackendServers|Modifies backend servers in a VServer group.|
    |slb:CreateLoadBalancerHTTPListener|Creates an HTTP listener for an SLB instance.|
    |slb:SetBackendServers|Configures backend servers of an SLB instance and sets weights for the backend servers. The backend servers are ECS instances.|
    |slb:AddTags|Attaches tags to an SLB instance.|

-   Permissions on Log Service resources

    |Permission|Description|
    |----------|-----------|
    |log:Get\*|Obtains permissions on Log Service resources.|
    |log:List\*|Lists permissions on Log Service resources.|
    |log:CreateProject|Creates a project.|
    |log:DeleteProject|Deletes a specified project.|
    |log:UpdateProject|Updates a project.|
    |log:CreateMachineGroup|Creates a machine group to apply log collection configurations.|
    |log:DeleteMachineGroup|Deletes a machine group.|
    |log:UpdateMachineGroup|Updates the information of a machine group.|
    |log:CreateLogStore|Creates a Logstore in a project.|
    |log:DeleteLogStore|Deletes a Logstore.|
    |log:UpdateLogStore|Updates the attributes of a Logstore.|
    |log:CreateIndex|Creates an index for a specified Logstore.|
    |log:DeleteIndex|Deletes the index of a specified Logstore.|
    |log:UpdateIndex|Updates the index of a specified Logstore.|
    |log:CreateConfig|Creates a log collection configuration.|
    |log:DeleteConfig|Deletes a specified log collection configuration.|
    |log:UpdateConfig|Updates a log collection configuration.|
    |log:ApplyConfigToGroup|Applies a log collection configuration to a machine group.|


## AliyunCSServerlessKubernetesRole

By default, a serverless Kubernetes cluster uses AliyunCSServerlessKubernetesRole to access resources of other cloud services.

-   Permissions on VPC resources

    |Permission|Description|
    |----------|-----------|
    |DescribeVSwitches|Queries existing VSwitches.|
    |DescribeVpcs|Queries existing VPCs.|
    |AssociateEipAddress|Binds an EIP to a cloud service instance in the same region.|
    |DescribeEipAddresses|Queries the EIPs that you create in a specified region.|
    |AllocateEipAddress|Applies for an EIP.|
    |ReleaseEipAddress|Releases a specified EIP.|
    |AddCommonBandwidthPackageIp|Binds an EIP to an EIP bandwidth plan.|
    |RemoveCommonBandwidthPackageIp|Unbinds an EIP from an EIP bandwidth plan.|

-   Permissions on ECS resources

    |Permission|Description|
    |----------|-----------|
    |DescribeSecurityGroups|Queries the basic information of the security groups that you create.|
    |CreateNetworkInterface|Creates an ENI.|
    |CreateNetworkInterfacePermission|Grants permissions to create an ENI.|
    |DescribeNetworkInterfaces|Queries ENIs.|
    |AttachNetworkInterface|Attaches an ENI to a VPC-connected ECS instance.|
    |DetachNetworkInterface|Detaches an ENI from an ECS instance.|
    |DeleteNetworkInterface|Deletes an ENI.|
    |DeleteNetworkInterfacePermission|Grants permissions to delete an ENI.|

-   Permissions on SLB resources

    |Permission|Description|
    |----------|-----------|
    |slb:Describe\*|Queries SLB resources.|
    |slb:CreateLoadBalancer|Creates an SLB instance.|
    |slb:DeleteLoadBalancer|Deletes a pay-as-you-go SLB instance.|
    |slb:RemoveBackendServers|Removes backend servers from an SLB instance.|
    |slb:StartLoadBalancerListener|Starts a listener.|
    |slb:StopLoadBalancerListener|Stops a listener.|
    |slb:DeleteLoadBalancerListener|Deletes a listener of an SLB instance.|
    |slb:CreateLoadBalancerTCPListener|Creates a TCP listener for an SLB instance.|
    |slb:AddBackendServers\*|Adds backend servers to an SLB instance.|
    |slb:UploadServerCertificate|Uploads a server certificate.|
    |slb:CreateLoadBalancerHTTPListener|Creates an HTTP listener for an SLB instance.|
    |slb:CreateLoadBalancerHTTPSListener|Creates an HTTPS listener for an SLB instance.|
    |slb:CreateLoadBalancerUDPListener|Creates a UDP listener.|
    |slb:ModifyLoadBalancerInternetSpec|Changes the billing method of a public-facing SLB instance.|
    |slb:CreateRules|Adds forwarding rules to a specified HTTP or HTTPS listener.|
    |slb:DeleteRules|Deletes forwarding rules.|
    |slb:SetRule|Modifies a forwarding rule of a VServer group.|
    |slb:CreateVServerGroup|Adds backend servers to a VServer group.|
    |slb:SetVServerGroupAttribute|Modifies the configurations of a VServer group.|
    |slb:AddVServerGroupBackendServers|Adds backend servers to a VServer group.|
    |slb:RemoveVServerGroupBackendServers|Removes backend servers from a specified VServer group.|
    |slb:ModifyVServerGroupBackendServers|Changes the backend servers of a VServer group.|
    |slb:DeleteVServerGroup|Deletes a VServer group.|
    |slb:SetLoadBalancerTCPListenerAttribute|Modifies the configuration of a TCP listener.|
    |slb:SetLoadBalancerHTTPListenerAttribute|Modifies the configuration of an HTTP listener.|
    |slb:SetLoadBalancerHTTPSListenerAttribute|Modifies the configuration of an HTTPS listener.|
    |slb:AddTags|Adds tags to a specified SLB instance.|

-   Permissions on Alibaba Cloud DNS PrivateZone

    |Permission|Description|
    |----------|-----------|
    |AddZone|Creates a private zone.|
    |DeleteZone|Deletes a private zone.|
    |DescribeZones|Queries private zones.|
    |DescribeZoneInfo|Queries the information of a specified private zone.|
    |BindZoneVpc|Binds a private zone to a VPC or unbinds a private zone from a VPC.|
    |AddZoneRecord|Adds a DNS record to a private zone.|
    |DeleteZoneRecord|Deletes a DNS record.|
    |DeleteZoneRecordsByRR|Deletes DNS records.|
    |DescribeZoneRecordsByRR|Queries DNS records.|
    |DescribeZoneRecords|Queries DNS records.|

-   Permissions on Container Registry \(ACR\) resources

    |Permission|Description|
    |----------|-----------|
    |Get\*|Queries ACR resources.|
    |List\*|Queries image repositories.|
    |PullRepository|Pulls an image.|

-   Permissions on ECIs

    |Permission|Description|
    |----------|-----------|
    |CreateContainerGroup|Creates a pod.|
    |DeleteContainerGroup|Deletes a pod.|
    |DescribeContainerGroups|Queries the information of multiple pods.|
    |DescribeContainerLog|Queries the logs of a pod.|
    |UpdateContainerGroup|Updates a pod.|
    |UpdateContainerGroupByTemplate|Updates an ECI by template.|
    |CreateContainerGroupFromTemplate|Creates an ECI by template.|
    |RestartContainerGroup|Restarts an ECI.|
    |ExportContainerGroupTemplate|Exports an ECI template.|
    |DescribeContainerGroupMetric|Queries the monitoring data of an ECI.|
    |DescribeMultiContainerGroupMetric|Queries the monitoring data of multiple pods.|
    |ExecContainerCommand|Runs commands on a container.|
    |CreateImageCache|Creates an image cache.|
    |DescribeImageCaches|Queries the information of image caches.|
    |DeleteImageCache|Deletes an image cache.|

-   Permissions on RAM resources

    |Permission|Description|
    |----------|-----------|
    |ram:PassRole|Visits the Alibaba Cloud CodePipeline console.|

-   Permissions on OSS resources

    |Permission|Description|
    |----------|-----------|
    |oss:GetObject|Obtains a file or a folder.|
    |oss:GetObjectMeta|Queries the metadata information of an object.|

-   Permissions on Function Compute

    |Permission|Description|
    |----------|-----------|
    |fc:CreateService|Creates a service.|
    |fc:ListServices|Queries services.|
    |fc:GetService|Queries a specified service.|
    |fc:UpdateService|Updates a specified service.|
    |fc:DeleteService|Deletes a specified service.|
    |fc:CreateFunction|Creates a function.|
    |fc:ListFunctions|Queries the functions of a service.|
    |fc:GetFunction|Queries the configurations of a specified function.|
    |fc:GetFunctionCode|Queries the code of a function.|
    |fc:UpdateFunction|Updates a function, including its configurations and code.|
    |fc:DeleteFunction|Deletes a specified function.|
    |fc:CreateTrigger|Creates a function trigger.|
    |fc:ListTriggers|Queries the triggers of a function.|
    |fc:GetTrigger|Queries a specified trigger.|
    |fc:UpdateTrigger|Updates the configurations of a specified trigger.|
    |fc:DeleteTrigger|Deletes the triggers of a specified function.|
    |fc:PublishServiceVersion|Releases a Function Compute version.|
    |fc:ListServiceVersions|Lists Function Compute versions.|
    |fc:DeleteServiceVersion|Deletes a Function Compute version.|
    |fc:CreateAlias|Creates an alias and binds it to a customer master key \(CMK\).|
    |fc:ListAliases|Lists all aliases of the current Alibaba Cloud account in the current region.|
    |fc:GetAlias|Queries the information about an alias.|
    |fc:UpdateAlias|Binds an alias to a different CMK.|
    |fc:DeleteAlias|Deletes an alias.|


## AliyunCSKubernetesAuditRole

The auditing feature of ACK uses AliyunCSKubernetesAuditRole to access resources of other cloud services.

|Permission|Description|
|----------|-----------|
|log:CreateProject|Creates a project.|
|log:GetProject|Queries a project by name.|
|log:DeleteProject|Deletes a specified project.|
|log:CreateLogStore|Creates a Logstore in a project.|
|log:GetLogStore|Queries the attributes of a Logstore.|
|log:UpdateLogStore|Updates the attributes of a Logstore.|
|log:DeleteLogStore|Deletes a Logstore.|
|log:CreateConfig|Creates a log collection configuration.|
|log:UpdateConfig|Updates a log collection configuration.|
|log:GetConfig|Queries the details of a log collection configuration.|
|log:DeleteConfig|Deletes a specified log collection configuration.|
|log:CreateMachineGroup|Creates a machine group to apply log collection configurations.|
|log:UpdateMachineGroup|Updates the information of a machine group.|
|log:GetMachineGroup|Queries the information of a specified machine group.|
|log:DeleteMachineGroup|Deletes a machine group.|
|log:ApplyConfigToGroup|Applies a log collection configuration to a machine group.|
|log:GetAppliedMachineGroups|Queries the machines to which a log collection configuration is applied in a machine group.|
|log:GetAppliedConfigs|Queries the configurations that are applied to a machine group.|
|log:RemoveConfigFromMachineGroup|Removes a configuration from a machine group.|
|log:CreateIndex|Creates an index for a specified Logstore.|
|log:GetIndex|Queries the index of a specified Logstore.|
|log:UpdateIndex|Updates the index of a specified Logstore.|
|log:DeleteIndex|Deletes the index of a specified Logstore.|
|log:CreateSavedSearch|Creates a saved search.|
|log:GetSavedSearch|Queries a specified saved search.|
|log:UpdateSavedSearch|Updates a saved search.|
|log:DeleteSavedSearch|Deletes a saved search.|
|log:CreateDashboard|Creates a dashboard.|
|log:GetDashboard|Queries a specified dashboard.|
|log:UpdateDashboard|Updates a dashboard.|
|log:DeleteDashboard|Deletes a dashboard.|
|log:CreateJob|Creates a job. For example, create an alert or a subscription.|
|log:GetJob|Queries a job.|
|log:DeleteJob|Deletes a job.|
|log:UpdateJob|Updates a job.|
|log:PostLogStoreLogs|Adds logs to a specified Logstore.|

## AliyunCSManagedNetworkRole

The network component of an ACK cluster uses AliyunCSManagedNetworkRole to access resources of other cloud services.

|Permission|Description|
|----------|-----------|
|ecs:CreateNetworkInterface|Creates an ENI.|
|ecs:DescribeNetworkInterfaces|Queries ENIs.|
|ecs:AttachNetworkInterface|Attaches an ENI to a VPC-connected ECS instance.|
|ecs:DetachNetworkInterface|Detaches an ENI from an ECS instance.|
|ecs:DeleteNetworkInterface|Deletes an ENI.|
|ecs:DescribeInstanceAttribute|Queries the information of one or more ECS instances.|
|ecs:AssignPrivateIpAddresses|Assigns one or more secondary private IP addresses to an ENI.|
|ecs:UnassignPrivateIpAddresses|Unbinds one or more secondary private IP addresses from an ENI.|
|ecs:DescribeInstances|Queries the information of one or more ECS instances.|
|vpc:DescribeVSwitches|Queries the information of one or more VSwitches.|

## AliyunCSDefaultRole

By default, AliyunCSDefaultRole is used to access resources of other cloud services when you perform operations on ACK clusters.

-   Permissions on ECS resources

    |Permission|Description|
    |----------|-----------|
    |ecs:RunInstances|Starts an ECS instance.|
    |ecs:RenewInstance|Renews an ECS instance.|
    |ecs:Create\*|Creates ECS resources, such as ECS instances and disks.|
    |ecs:AllocatePublicIpAddress|Assigns a public IP address to an ECS instance.|
    |ecs:AllocateEipAddress|Assigns an EIP to an ECS instance.|
    |ecs:Delete\*|Deletes an ECS instance.|
    |ecs:StartInstance|Starts ECS resources.|
    |ecs:StopInstance|Stops an ECS instance.|
    |ecs:RebootInstance|Restarts an ECS instance.|
    |ecs:Describe\*|Queries ECS resources.|
    |ecs:AuthorizeSecurityGroup|Sets inbound rules for a security group.|
    |ecs:RevokeSecurityGroup|Revokes security group rules.|
    |ecs:AuthorizeSecurityGroupEgress|Sets outbound rules for a security group.|
    |ecs:AttachDisk|Attaches a disk to an ECS instance.|
    |ecs:DetachDisk|Detaches a disk from an ECS instance.|
    |ecs:WaitFor\*|Waits for the execution of a task.|
    |ecs:AddTags|Adds tags to an ECS instance.|
    |ecs:ReplaceSystemDisk|Replaces the system disk of an ECS instance.|
    |ecs:ModifyInstanceAttribute|Modifies the attributes of an ECS instance.|
    |ecs:JoinSecurityGroup|Adds an ECS instance to a security group.|
    |ecs:LeaveSecurityGroup|Removes an ECS instance from a security group.|
    |ecs:UnassociateEipAddress|Detaches an EIP from an ECS instance.|
    |ecs:ReleaseEipAddress|Releases an EIP.|
    |ecs:CreateKeyPair|Creates an SSH key pair.|
    |ecs:ImportKeyPair|Imports the public key of an RSA key pair that is created by using a third-party tool.|
    |ecs:AttachKeyPair|Attaches an SSH key pair to one or more Linux-based ECS instances.|
    |ecs:DetachKeyPair|Detaches an SSH key pair from one or more Linux-based ECS instances.|
    |ecs:DeleteKeyPairs|Deletes one or more SSH key pairs.|
    |ecs:AttachInstanceRamRole|Attaches a RAM role to one or more ECS instances.|
    |ecs:DetachInstanceRamRole|Detaches a RAM role from one or more ECS instances.|
    |ecs:AllocateDedicatedHosts|Creates one or more pay-as-you-go or subscription dedicated hosts.|
    |ecs:CreateOrder|Creates an order to purchase ECS instances.|
    |ecs:DeleteInstance|Releases a pay-as-you-go instance or an expired subscription instance.|
    |ecs:CreateDisk|Creates a pay-as-you-go or subscription data disk.|
    |ecs:Createvpc|Creates a VPC for an ECS instance.|
    |ecs:Deletevpc|Deletes the VPC that is connected to an ECS instance.|
    |ecs:DeleteVSwitch|Deletes the VSwitch that is connected to an ECS instance.|
    |ecs:ResetDisk|Rolls back a disk to a specified state by using a disk snapshot.|
    |ecs:DeleteSnapshot|Deletes a specified snapshot.|
    |ecs:AllocatePublicIpAddress|Assigns a public IP address to an ECS instance.|
    |ecs:CreateVSwitch|Creates a VSwitch for an ECS instance.|
    |ecs:DeleteSecurityGroup|Deletes a security group.|
    |ecs:CreateImage|Creates a custom image.|
    |ecs:RemoveTags|Deletes tags from an ECS instance.|
    |ecs:ReleaseDedicatedHost|Releases a pay-as-you-go dedicated host.|
    |ecs:CreateInstance|Creates a subscription or pay-as-you-go ECS instance.|
    |ecs:RevokeSecurityGroupEgress|Deletes an outbound rule of a security group. This revokes outbound permissions of the security group.|
    |ecs:DeleteDisk|Releases a pay-as-you-go data disk.|
    |ecs:StopInstance|Stops an ECS instance.|
    |ecs:CreateSecurityGroup|Creates a security group.|
    |ecs:RevokeSecurityGroup|Deletes an inbound rule of a security group. This revokes inbound permissions of the security group.|
    |ecs:DeleteImage|Deletes a custom image.|
    |ecs:ModifyInstanceSpec|Modifies the instance type of ECS instances or public bandwidth of a pay-as-you-go ECS instance.|
    |ecs:CreateSnapshot|Creates a snapshot for a disk.|
    |ecs:CreateCommand|Creates a Cloud Assistant command.|
    |ecs:InvokeCommand|Triggers a Cloud Assistant command on one or more ECS instances.|
    |ecs:StopInvocation|Stops the process of a running Cloud Assistant command on one or more ECS instances.|
    |ecs:DeleteCommand|Deletes a Cloud Assistant command.|
    |ecs:RunCommand|Creates a Cloud Assistant command of the shell, PowerShell, or batch type, and runs the command on one or more ECS instances.|
    |ecs:DescribeInvocationResults|Queries the result of running a Cloud Assistant command on a specified ECS instance.|
    |ecs:ModifyCommand|Modifies a Cloud Assistant command.|

-   Permissions on VPC resources

    |Permission|Description|
    |----------|-----------|
    |vpc:Describe\*|Queries VPC resources.|
    |vpc:AllocateEipAddress|Assigns an EIP to an ECS instance.|
    |vpc:AssociateEipAddress|Binds an EIP to an ECS instance.|
    |vpc:UnassociateEipAddress|Unbinds an EIP from an ECS instance.|
    |vpc:ReleaseEipAddress|Releases an EIP.|
    |vpc:CreateRouteEntry|Creates a route entry.|
    |vpc:DeleteRouteEntry|Deletes a route entry.|
    |vpc:CreateVSwitch|Creates a VSwitch.|
    |vpc:DeleteVSwitch|Deletes a VSwitch.|
    |vpc:CreateVpc|Creates a VPC.|
    |vpc:DeleteVpc|Deletes a VPC.|
    |vpc:CreateNatGateway|Creates a network address translation \(NAT\) gateway.|
    |vpc:DeleteNatGateway|Deletes a specified NAT gateway.|
    |vpc:CreateSnatEntry|Adds a source network address translation \(SNAT\) entry to a specified SNAT table.|
    |vpc:DeleteSnatEntry|Deletes an SNAT entry from a specified SNAT table.|
    |vpc:ModifyEipAddressAttribute|Modifies the name, description, and peak bandwidth of a specified EIP.|
    |vpc:CreateForwardEntry|Adds a destination network address translation \(DNAT\) entry to a specified DNAT table.|
    |vpc:DeleteBandwidthPackage|Creates a NAT service plan.|
    |vpc:CreateBandwidthPackage|Deletes a NAT service plan.|
    |vpc:DeleteForwardEntry|Deletes a DNAT entry from a specified DNAT table.|
    |vpc:TagResources|Creates tags and attaches them to a specified resource.|
    |vpc:DeletionProtection|Enables or disables deletion protection for an instance.|

-   Permissions on SLB resources

    |Permission|Description|
    |----------|-----------|
    |slb:Describe\*|Queries the information about an SLB instance.|
    |slb:CreateLoadBalancer|Creates an SLB instance.|
    |slb:DeleteLoadBalancer|Deletes an SLB instance.|
    |slb:RemoveBackendServers|Unbinds backend servers from an SLB instance.|
    |slb:StartLoadBalancerListener|Starts a specified listener.|
    |slb:StopLoadBalancerListener|Stops a specified listener.|
    |slb:CreateLoadBalancerTCPListener|Creates a TCP listener for an SLB instance.|
    |slb:AddBackendServers|Adds backend servers to an SLB instance.|
    |slb:CreateVServerGroup|Creates a VServer group and adds backend servers to the VServer group.|
    |slb:CreateLoadBalancerHTTPSListener|Creates an HTTPS listener for an SLB instance.|
    |slb:CreateLoadBalancerUDPListener|Creates a UDP listener.|
    |slb:ModifyLoadBalancerInternetSpec|Changes the billing method of a public-facing SLB instance.|
    |slb:SetBackendServers|Configures backend servers of an SLB instance and sets weights for the backend servers. The backend servers are ECS instances.|
    |slb:AddVServerGroupBackendServers|Adds backend servers to a VServer group.|
    |slb:DeleteVServerGroup|Deletes a VServer group.|
    |slb:ModifyVServerGroupBackendServers|Changes the backend servers of a VServer group.|
    |slb:CreateLoadBalancerHTTPListener|Creates an HTTP listener for an SLB instance.|
    |slb:RemoveVServerGroupBackendServers|Removes backend servers from a specified VServer group.|
    |slb:DeleteLoadBalancerListener|Deletes a listener of an SLB instance.|
    |slb:AddTags|Adds tags to a specified SLB instance.|
    |slb:RemoveTags|Removes tags from a specified SLB instance.|
    |slb:SetLoadBalancerDeleteProtection|Enables or disables deletion protection for an SLB instance.|

-   Permissions on Domain Name System \(DNS\) resources

    |Permission|Description|
    |----------|-----------|
    |dns:Describe\*|Queries DNS resources.|
    |dns:AddDomainRecord|Adds a DNS record.|

-   Permissions on RDS resources

    |Permission|Description|
    |----------|-----------|
    |rds:Describe\*|Queries RDS resources.|
    |rds:ModifySecurityIps|Modifies the IP address whitelist of an RDS instance.|

-   Permissions on Resource Orchestration Service \(ROS\)

    |Permission|Description|
    |----------|-----------|
    |ros:Describe\*|Queries ROS resources.|
    |ros:WaitConditions|Waits for the execution of an ROS script.|
    |ros:AbandonStack|Stops a stack.|
    |ros:DeleteStack|Deletes a stack.|
    |ros:CreateStack|Creates a stack.|
    |ros:UpdateStack|Updates a stack.|
    |ros:ValidateTemplate|Validates an ROS template.|
    |ros:DoActions|Performs actions.|
    |ros:InquiryStack|Queries a stack.|
    |ros:SetDeletionProtection|Enables or disables deletion protection.|
    |ros:PreviewStack|Previews a stack.|

-   Permissions on Auto Scaling \(ESS\)

    |Permission|Description|
    |----------|-----------|
    |ess:Describe\*|Queries ESS resources.|
    |ess:CreateScalingConfiguration|Creates a scaling configuration.|
    |ess:EnableScalingGroup|Enables a scaling group.|
    |ess:ExitStandby|Switches the status of a standby ECS instance in a scaling group to running.|
    |ess:DetachDBInstances|Removes one or more RDS instances from a scaling group.|
    |ess:DetachLoadBalancers|Removes one or more SLB instances from a scaling group.|
    |ess:AttachInstances|Adds one or more ECS instances to a scaling group.|
    |ess:DeleteScalingConfiguration|Deletes a scaling configuration.|
    |ess:AttachLoadBalancers|Adds one or more SLB instances.|
    |ess:DetachInstances|Removes one or more ECS instances from a scaling group.|
    |ess:ModifyScalingRule|Modifies a scaling group rule.|
    |ess:RemoveInstances|Removes ECS instances from a specified scaling group.|
    |ess:ModifyScalingGroup|Modifies a scaling group.|
    |ess:AttachDBInstances|Adds one or more RDS instances.|
    |ess:CreateScalingRule|Creates a scaling rule.|
    |ess:DeleteScalingRule|Deletes a scaling rule.|
    |ess:ExecuteScalingRule|Runs a scaling rule.|
    |ess:SetInstancesProtection|Enables or disables protection for one or more ECS instances in a scaling group.|
    |ess:ModifyNotificationConfiguration|Modifies a notification configuration for auto scaling events and resource changes.|
    |ess:CreateNotificationConfiguration|Creates a notification configuration for auto scaling events and resource changes.|
    |ess:EnterStandby|Switches the status of an ECS instance in the scaling group to standby.|
    |ess:DeleteScalingGroup|Deletes a scaling group.|
    |ess:CreateScalingGroup|Creates a scaling group.|
    |ess:DeleteNotificationConfiguration|Deletes a notification configuration for auto scaling events and resource changes.|
    |ess:DisableScalingGroup|Disables a scaling group.|
    |ModifyScalingConfiguration|Modifies a scaling configuration.|
    |SetGroupDeletionProtection|Enables or disables deletion protection for a scaling group.|

-   Permissions on RAM resources

    |Permission|Description|
    |----------|-----------|
    |ram:PassRole|Authorizes a RAM user to use other cloud services.|
    |ram:Get\*|Queries permissions on RAM resources.|
    |ram:List\*|Lists permissions on RAM resources.|
    |ram:DetachPolicyFromRole|Revokes a specified permission from a role.|
    |ram:AttachPolicyToRole|Grants a permission to a specified role.|
    |ram:DeletePolicy|Deletes a specified permission policy.|
    |ram:DeletePolicyVersion|Deletes a policy of a specified version.|
    |ram:DeleteRole|Deletes a RAM role.|
    |ram:CreateRole|Creates a RAM role.|
    |ram:CreatePolicy|Creates a RAM policy.|
    |ram:CreateServiceLinkedRole|Creates permissions to be granted to service linked roles.|

-   Permissions on CMS resources

    |Permission|Description|
    |----------|-----------|
    |cms:CreateMyGroups|Creates private application groups.|
    |cms:AddMyGroupInstances|Adds resources to a private application group.|
    |cms:DeleteMyGroupInstances|Deletes resources from a private application group.|
    |cms:DeleteMyGroups|Deletes private application groups.|
    |cms:GetMyGroups|Queries private application groups.|
    |cms:ListMyGroups|Lists private application groups.|
    |cms:UpdateMyGroupInstances|Updates resources in a private application group.|
    |cms:UpdateMyGroups|Updates private application groups.|
    |cms:TaskConfigCreate|Creates a monitoring job configuration.|
    |cms:TaskConfigList|Lists monitoring job configurations.|

-   Permissions on ESS resources

    |Permission|Description|
    |----------|-----------|
    |ess:CreateLifecycleHook|Creates one or more lifecycle hooks for a scaling group.|
    |ess:DescribeLifecycleHooks|Queries lifecycle hooks.|
    |ess:ModifyLifecycleHook|Modifies a lifecycle hook.|
    |ess:DeleteLifecycleHook|Deletes a lifecycle hook.|

-   Permissions on Edge Node Service \(ENS\) resources

    |Permission|Description|
    |----------|-----------|
    |ens:Describe\*|Queries the permissions on ENS resources.|
    |ens:CreateInstance|Creates an ENS instance.|
    |ens:StartInstance|Starts an ENS instance.|
    |ens:StopInstance|Stops an ENS instance.|
    |ens:ReleasePrePaidInstance|Releases a subscription instance.|


## AliyunCSManagedKubernetesRole

A managed Kubernetes cluster uses AliyunCSManagedKubernetesRole to access resources of other cloud services.

-   Permissions on ECS resources

    |Permission|Description|
    |----------|-----------|
    |ecs:Describe\*|Queries ECS resources.|
    |ecs:CreateRouteEntry|Creates a route entry.|
    |ecs:DeleteRouteEntry|Deletes a route entry.|
    |ecs:CreateNetworkInterface|Creates an ENI.|
    |ecs:DeleteNetworkInterface|Deletes an ENI.|
    |ecs:CreateNetworkInterfacePermission|Grants permissions to create an ENI.|
    |ecs:DeleteNetworkInterfacePermission|Grants permissions to delete an ENI.|
    |ecs:ModifyInstanceAttribute|Modifies the attributes of an instance.|
    |ecs:AttachKeyPair|Attaches an SSH key pair to one or more Linux-based ECS instances.|
    |ecs:StopInstance|Stops an instance.|
    |ecs:StartInstance|Starts an instance.|
    |ecs:ReplaceSystemDisk|Replaces the system disk or the operating system of an ECS instance.|

-   Permissions on SLB resources

    |Permission|Description|
    |----------|-----------|
    |slb:Describe\*|Queries SLB resources.|
    |slb:CreateLoadBalancer|Creates an SLB instance.|
    |slb:DeleteLoadBalancer|Deletes an SLB instance.|
    |slb:ModifyLoadBalancerInternetSpec|Changes the billing method of a public-facing SLB instance.|
    |slb:RemoveBackendServers|Removes backend servers from an SLB instance.|
    |slb:AddBackendServers|Adds backend servers to an SLB instance.|
    |slb:RemoveTags|Removes tags from a specified SLB instance.|
    |slb:AddTags|Adds tags to a specified SLB instance.|
    |slb:StopLoadBalancerListener|Stops a listener.|
    |slb:StartLoadBalancerListener|Starts a listener.|
    |slb:SetLoadBalancerHTTPListenerAttribute|Modifies the configuration of an HTTP listener.|
    |slb:SetLoadBalancerHTTPSListenerAttribute|Modifies the configuration of an HTTPS listener.|
    |slb:SetLoadBalancerTCPListenerAttribute|Modifies the configuration of a TCP listener.|
    |slb:SetLoadBalancerUDPListenerAttribute|Modifies the configuration of a UDP listener.|
    |slb:CreateLoadBalancerHTTPSListener|Creates an HTTPS listener for an SLB instance.|
    |slb:CreateLoadBalancerHTTPListener|Creates an HTTP listener for an SLB instance.|
    |slb:CreateLoadBalancerTCPListener|Creates a TCP listener for an SLB instance.|
    |slb:CreateLoadBalancerUDPListener|Creates a UDP listener.|
    |slb:DeleteLoadBalancerListener|Deletes a listener of an SLB instance.|
    |slb:CreateVServerGroup|Adds backend servers to a VServer group.|
    |slb:DescribeVServerGroups|Queries VServer groups.|
    |slb:DeleteVServerGroup|Deletes a VServer group.|
    |slb:SetVServerGroupAttribute|Modifies the configurations of a VServer group.|
    |slb:DescribeVServerGroupAttribute|Queries the information about a VServer group.|
    |slb:ModifyVServerGroupBackendServers|Changes the backend servers of a VServer group.|
    |slb:AddVServerGroupBackendServers|Adds backend servers to a VServer group.|
    |slb:ModifyLoadBalancerInstanceSpec|Modifies the specifications of an SLB instance.|
    |slb:ModifyLoadBalancerInternetSpec|Changes the billing method of a public-facing SLB instance.|
    |slb:RemoveVServerGroupBackendServers|Removes backend servers from a specified VServer group.|

-   Permissions on VPC resources

    |Permission|Description|
    |----------|-----------|
    |vpc:Describe\*|Queries VPC resources.|
    |vpc:DeleteRouteEntry|Deletes a custom route entry.|
    |vpc:CreateRouteEntry|Creates a custom route entry.|

-   Permissions on ACR resources

    |Permission|Description|
    |----------|-----------|
    |cr:Get\*|Queries ACR resources.|
    |cr:List\*|Queries image repositories.|
    |cr:PullRepository|Pulls an image.|


## AliyunCSManagedArmsRole

The application real-time monitoring agent of an ACK cluster uses AliyunCSManagedArmsRole to access resources of other cloud services.

|Permission|Description|
|----------|-----------|
|arms:CreateApp|Creates an application monitoring job.|
|arms:DeleteApp|Deletes an application monitoring job.|
|arms:ConfigAgentLabel|Modifies the tags of the application monitoring agent.|
|arms:GetAssumeRoleCredentials|Queries the key that is required for a RAM user to assume a RAM role during application monitoring.|
|arms:CreateProm|Creates a monitoring job based on Alibaba Cloud Prometheus Monitoring.|
|arms:SearchEvents|Queries alert events.|
|arms:SearchAlarmHistories|Queries the records of sending alerts.|
|arms:SearchAlertRules|Queries monitoring alert rules.|
|arms:GetAlertRules|Obtains monitoring alert rules.|
|arms:CreateAlertRules|Creates monitoring alert rules.|
|arms:UpdateAlertRules|Updates monitoring alert rules.|
|arms:StartAlertRule|Enables a monitoring alert rule.|
|arms:StopAlertRule|Disables a monitoring alert rule.|
|arms:CreateContact|Creates an alert contact.|
|arms:SearchContact|Queries an alert contact.|
|arms:UpdateContact|Updates an alert contact.|
|arms:CreateContactGroup|Creates an alert contact group.|
|arms:SearchContactGroup|Queries an alert contact group.|
|arms:UpdateContactGroup|Updates an alert contact group.|

**Related topics**  


[Use ACK for the first time](/intl.en-US/Quick Start/Use ACK for the first time.md)

