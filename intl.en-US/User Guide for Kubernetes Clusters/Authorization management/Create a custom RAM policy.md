---
keyword: [RAM, custom RAM policy]
---

# Create a custom RAM policy

This topic describes how to create a custom Resource Access Management \(RAM\) policy. In this topic, custom permission policies are created and attached to a RAM user to grant the RAM user permissions to query, modify, and delete Container Service for Kubernetes \(ACK\) clusters.

Before you create custom permission policies, you must fully understand the basic structure and syntax of the permission policy language. For more information, see [RAM policy language](https://www.alibabacloud.com/help/zh/doc-detail/28663.htm).

The system policies provided by ACK are coarse-grained. If the system policies cannot meet your requirements, you can create custom policies to implement fine-grained access control for your ACK clusters. For example, if you want to control the operation permissions on a specified cluster, you must create a custom permission policy to meet this requirement for fine-grained management.

Before you use role-based access control \(RBAC\) to authorize a RAM user, you must authorize the RAM user to manage a specified cluster in the RAM console. You can grant the RAM user read and write permissions on the cluster based on your requirements.

-   Read permissions: view basic information about a specified cluster, such as cluster configuration and the kubeconfig file.
-   Write permissions: manage a specified cluster, such as scaling and upgrading the cluster, and adding nodes to and removing nodes from the cluster.

Before you perform RBAC authorization for the RAM user, make sure that the RAM user has read-only permissions on the cluster that you want to manage.

```
{
  "Statement": [
    {
      "Action": "cs:Get*",
      "Effect": "Allow",
      "Resource": [
        "acs:cs:*:*:cluster/<yourclusterID>"
      ]
    }
  ],
  "Version": "1"
}
```

After the RAM user is granted the required RAM permissions on the cluster, use RBAC to authorize the RAM user to manage cluster resources. For more information, see [t17456.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

## Procedure

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account or as an authorized RAM user.

2.  In the left-side navigation pane, choose **Permissions** \> **Policies** to go to the Policies page.

3.  On the Policies page, click **Create Policy** to go to the Create Custom Policy page.

4.  Specify **Policy Name**. Set **Configuration Mode** to Script. Enter the policy content as required in the **Policy Document** section.

    ![Create a custom policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0235359951/p10480.png)

    ```
    {
     "Statement": [{
         "Action": [
             "cs:Get*",
             "cs:ScaleCluster",
             "cs:DeleteCluster"
         ],
         "Effect": "Allow",
         "Resource": [
             "acs:cs:*:*:cluster/Cluster ID"
         ]
     }],
     "Version": "1"
    }
    ```

    Configure the policy based on the following description:

    -   Enter the permissions that you want to grant in the `Action` field.

        **Note:** You can use wildcard characters in the Action field.

    -   Set the `Resource` field based on your requirements:
        -   Grant access to one cluster

            ```
            "Resource": [
                 "acs:cs:*:*:cluster/Cluster ID"
             ]
            ```

        -   Grant access to multiple clusters

            ```
            "Resource": [
                 "acs:cs:*:*:cluster/Cluster ID",
                 "acs:cs:*:*:cluster/Cluster ID"
             ]
            ```

        -   Grant access to all clusters

            ```
            "Resource": [
                 "*"
             ]
            ```

            Replace `Cluster ID` with the ID of your cluster.

5.  Click **OK**.

    Return to the Policies page. You can enter the policy name or note in the search box and click the search icon to find the newly created policy.

    ![Policy management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0235359951/p47367.png)


## References

**Mappings between RAM actions and API operations**

|API operation|RAM Action|Description|
|-------------|----------|-----------|
|AttachInstances|AttachInstances|Adds existing ECS instances to a specified ACK cluster.|
|CancelClusterUpgrade|CancelK8sCluster|Cancels the upgrade of a specified ACK cluster.|
|CancelComponentUpgrade|CancelComponentUpgrade|Cancels the upgrade of a specified component.|
|CreateCluster|CreateCluster|Creates an ACK cluster.|
|CreateKubernetesTrigger|GenerateTriggerHook|Creates an application trigger.|
|CreateTemplate|CreateTemplate|Creates an application deployment template.|
|DeleteCluster|DeleteCluster|Deletes an ACK cluster.|
|DeleteClusterNodes|DeleteClusterNodes|Removes a node and releases the ECS instance.|
|DeleteKubernetesTrigger|RevokeTriggers|Deletes an application trigger.|
|DeleteTemplate|V2DeleteTemplateInfo|Deletes an application deployment template.|
|DescribeAddons|Queryk8sComponentsVersion|Queries information about the components in a specified ACK cluster.|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|Queries the versions of the components in a specified ACK cluster.|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|Queries the upgrade status of a specified component in an ACK cluster.|
|DescribeClusterAttachScripts|GetClusterJoinScript|Queries the script that is used to manually add nodes to a specified ACK cluster.|
|DescribeClusterDetail|GetClusterById|Queries the details of a specified ACK cluster.|
|DescribeClusterLogs|GetClusterLogs|Queries the log of a specified ACK cluster.|
|DescribeClusterNodes|DescribeClusterNodes|Queries the nodes in a specified ACK cluster.|
|DescribeClusterResources|DescribeClusterResources|Queries all resources in a specified ACK cluster.|
|DescribeClusters|GetClustersByUid and GetClusters|Queries all ACK clusters that belong to your account.|
|DescribeClustersV1|ListClusters|Queries all ACK clusters that belong to your account.|
|DescribeClusterUserKubeconfig|GetUserConfig|Queries the kubeconfig file of a specified ACK cluster.|
|DescribeClusterV2UserKubeconfig|GetUserConfig|Queries the kubeconfig file of a specified ACK cluster.|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|Queries the configurations of an agent that is used to register an external Kubernetes cluster.|
|DescribeTemplates|V2ListTemplates|Queries application deployment templates.|
|DescribeUserQuota|GetUserQuota|Queries resource quotas of your account.|
|GetKubernetesTrigger|GetK8sTrigger|Queries the details about application triggers.|
|GetUpgradeStatus|GetK8sClusterState|Queries the upgrade status of a specified ACK cluster.|
|InstallClusterAddons|InstallK8sComponents|Installs a specified component in an ACK cluster.|
|ModifyCluster|ModifyCluster|Modifies information about a specified ACK cluster.|
|ModifyClusterTags|UpdateClusterTags|Modifies the labels of a specified ACK cluster.|
|PauseClusterUpgrade|UpgradeCluster|Suspends the upgrade of a specified ACK cluster.|
|PauseComponentUpgrade|PauseComponentUpgrade|Suspends the upgrade of a specified component.|
|ReBindSecurityGroup|ReBindSecurityGroup|Rebinds an ACK cluster to another security group.|
|RemoveClusterNodes|DeleteClusterNode|Removes nodes from a specified ACK cluster.|
|ResumeComponentUpgrade|ResumeComponentUpgrade|Resumes the upgrade of a specified component.|
|ResumeUpgradeCluster|UpgradeCluster|Resumes the upgrade of a specified ACK cluster.|
|ScaleCluster|ScaleCluster|Scales out an ACK cluster.|
|ScaleOutCluster|ScaleCluster|Adds existing nodes to a specified ACK cluster.|
|UnInstallClusterAddons|UnInstallK8sComponents|Uninstalls components from a specified ACK cluster.|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|Updates the expiration time of custom configurations.|
|UpgradeCluster|UpgradeCluster|Upgrades an ACK cluster.|
|UpgradeClusterAddons|UpgradeK8sComponents|Upgrades the components of a specified ACK cluster.|
|DescribeUserPermission|GetUserPermissions|Queries the permissions that are granted to a RAM user to manage ACK clusters.|
|GrantPermissions|GrantPermission|Updates the permissions of a specified RAM user to manage ACK clusters.|
|CreateClusterNodePool|CreateNodepool|Creates a node pool.|
|DeleteClusterNodepool|DeleteNodepool|Deletes a node pool.|
|DescribeClusterNodePoolDetail|GetNodepoolDetail|Queries detailed information about a node pool.|
|DescribeClusterNodePools|GetNodepools|Queries a node pool.|
|ModifyClusterNodePool|UpdateNodepool|Modifies a node pool.|
|ScaleClusterNodePool|ScaleNodepool|Scales out a node pool.|
|MigrateCluster|MigrateCluster|Migrates an ACK cluster.|

