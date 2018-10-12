# Role authorization {#concept_z1k_tq2_xdb .concept}

Grant the system default roles AliyunCSDefaultRole and AliyunCSClusterRole to the service account when you activate Container Service. Only after the roles are correctly granted, Container Service can normally call services such as Elastic Compute Service \(ECS\), Object Storage Service \(OSS\), Network Attached Storage \(NAS\), and Server Load Balancer \(SLB\), create clusters, and store logs.

## Instructions {#section_knb_wq2_xdb .section}

-   If you have used Container Service before 15 January 2018, the system completes the role authorization by default. For the detailed granted permissions, see the following Default role permissions section. If you used Container Service with a Resource Access Management \(RAM\) user before, upgrade the authorization policy for the RAM user. For more information, see [Create custom authorization policies](intl.en-US/User Guide/Kubernetes cluster/授权管理/Create custom authorization policies.md#).
-   On 15 January 2018, Container Service is fully accessed to the cross-service authorization. New users who use the primary account can use Container Service only after having the cross-service authorization completed. If new users need to authorize RAM users to use Container Service, go to the RAM console to authorize the RAM users. For more information, see [Use sub-accounts](intl.en-US/User Guide/Kubernetes cluster/授权管理/Use sub-accounts.md#).

## Procedure {#section_mcg_xq2_xdb .section}

1.  If you have not granted the default roles to the service account correctly, the Cloud Resource Access Authorization page appears after you log on to the Container Service console. Click **Confirm Authorization Policy**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16635/153931288810824_en-US.png)

    **Note:** Container Service has configured the default role permissions. To modify the role permissions, go to the User Management page of the RAM console. Note that incorrect configurations might cause Container Service cannot obtain the required permissions.

2.  After completing the authorization, refresh the Container Service console and then perform the operations.

    To view the policy details of the roles AliyunCSDefaultRole and AliyunCSClusterRole, log on to the [RAM console](https://ram.console.aliyun.com/).


## Default role permissions {#section_phy_5q2_xdb .section}

For more information about permissions of each role, see the API documents of each product.

## AliyunCSDefaultRole permissions {#section_qhy_5q2_xdb .section}

The default role AliyunCSDefaultRole contains the following main permissions:

-   ECS-related permissions

|Action|Description|
|------|-----------|
|ecs:RunInstances|Query ECS instance information.|
|ecs:RenewInstance|Renew ECS instances.|
|ecs:Create\*|Create ECS-related resources, such as instances and disks.|
|ecs:AllocatePublicIpAddress|Allocate public IP addresses.|
|ecs:AllocateEipAddress|Allocate Elastic IP \(EIP\) addresses.|
|ecs:Delete\*|Delete ECS instances.|
|ecs:StartInstance|Start ECS-related resources.|
|ecs:StopInstance|Stop ECS instances.|
|ecs:RebootInstance|Restart ECS instances.|
|ecs:Describe\*|Query ECS-related resources.|
|ecs:AuthorizeSecurityGroup|Configure inbound security group rules.|
|ecs:RevokeSecurityGroup|Revoke security group rules.|
|ecs:AuthorizeSecurityGroupEgress|Configure outbound security group rules.|
|ecs:AttachDisk|Add disks.|
|ecs:DetachDisk|Clean up disks.|
|ecs:AddTags|Add tags.|
|ecs:ReplaceSystemDisk|Change system disks of ECS instances.|
|ecs:ModifyInstanceAttribute|Modify ECS instance attributes.|
|ecs:JoinSecurityGroup|Add ECS instances to specified security groups.|
|ecs:LeaveSecurityGroup|Remove ECS instances from specified security groups.|
|ecs:UnassociateEipAddress|Unbind EIP addresses.|
|ecs:ReleaseEipAddress|Release EIP addresses.|

-   Virtual Private Cloud \(VPC\)-related permissions

|Permission name \(Action\)|Permission description|
|--------------------------|----------------------|
|vpc:Describe\*|Query information of VPC-related resources.|
|vpc:DescribeVpcs|Query VPC information.|
|vpc:AllocateEipAddress|Allocate EIP addresses.|
|vpc:AssociateEipAddress|Associate with EIP addresses.|
|vpc:UnassociateEipAddress|Do not associate with EIP addresses.|
|vpc:ReleaseEipAddress|Release EIP addresses.|
|vpc:CreateRouteEntry|Create router interfaces.|
|vpc:DeleteRouteEntry|Delete router interfaces.|

-   SLB-related permissions

|Action|Description|
|------|-----------|
|slb:Describe\*|Query information related to Server Load Balancer.|
|slb:CreateLoadBalancer|Create Server Load Balancer instances.|
|slb:DeleteLoadBalancer|Delete Server Load Balancer instances.|
|slb:RemoveBackendServers|Unbind Server Load Balancer instances.|
|slb:StartLoadBalancerListener|Start specified listeners.|
|slb:StopLoadBalancerListener|Stop specified listeners.|
|slb:CreateLoadBalancerTCPListener|Create TCP-based listening rules for Server Load Balancer instances.|
|slb:AddBackendServers|Add backend servers.|

## AliyunCSClusterRole permissions {#section_h3y_5q2_xdb .section}

The default role AliyunCSClusterRole contains the following main permissions:

-   OSS-related permissions

|Action|Description|
|------|-----------|
|oss: PutObject|Upload file or folder objects.|
|oss: GetObject|Get file or folder objects.|
|oss: ListObjects|Query file list information.|

-   NAS-related permissions

|Action|Description|
|------|-----------|
|nas:Describe\*|Return NAS-related information.|
|nas:CreateAccessRule|Create permission rules.|

-   SLB-related permissions

|Action|Description|
|------|-----------|
|slb:Describe\*|Query information related to Server Load Balancer.|
|slb:CreateLoadBalancer|Create Server Load Balancer instances.|
|slb:DeleteLoadBalancer|Delete Server Load Balancer instances.|
|slb:RemoveBackendServers|Unbind Server Load Balancer instances.|
|slb:StartLoadBalancerListener|Start specified listeners.|
|slb:StopLoadBalancerListener|Stop specified listeners.|
|slb:CreateLoadBalancerTCPListener|Create TCP-based listening rules for Server Load Balancer instances.|
|slb:AddBackendServers|Add backend servers.|
|slb:DeleteLoadBalancerListener|Delete listening rules of Server Load Balancer instances.|
|slb:CreateVServerGroup|Create VServer groups and add backend servers.|
|slb:ModifyVServerGroupBackendServers|Change backend servers in VServer groups.|
|slb:CreateLoadBalancerHTTPListener|Create HTTP-based listeners for Server Load Balancer instances.|
|slb:SetBackendServers|Configure backend servers and set the weight for a group of ECS instances at the Server Load Balancer instance backend.|
|slb:AddTags|Add tags for Server Load Balancer instances.|

