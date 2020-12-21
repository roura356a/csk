---
keyword: [RAM, custom RAM policies]
---

# Create a custom RAM policy

This topic describes how to create a custom Resource Access Management \(RAM\) policy and attach the policy to a RAM user. In this topic, a RAM policy that defines the permissions to query, expand, and delete a cluster is attached to a RAM user.

Before you create a custom RAM policy, read [RAM policy language](https://www.alibabacloud.com/help/zh/doc-detail/28663.htm) to learn the policy structure and syntax.

The system policies created by ACK are coarse-grained. If the system policies cannot meet your needs, you can create custom policies to implement fine-grained access control for your ACK clusters. For example, if you want to control the permissions on a specific cluster, you must create a RAM policy.

Before you use role-based access control \(RBAC\) to authorize a RAM user, use a RAM policy to authorize the RAM user to manage the cluster. You can grant the cluster read and write policies to the RAM user as needed.

-   Read policy: authorizes the RAM user to view the basic information, such as cluster configurations and the kubeconfig file.
-   Write policy: authorizes the RAM user to manage the cluster. For example, the RAM user can scale the cluster, upgrade the cluster, remove nodes from the cluster, and add nodes to the cluster.

Before you use RBAC to authorize the RAM user, make sure that you have attached the read policy to the RAM user. You can use the following sample policy:

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

After you attach the read policy to the RAM user, use RBAC to authorize the RAM user to manage the resources in the ACK cluster. For more information, see [t17456.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

## Attach a RAM policy to the RAM user

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account or as an authorized RAM user.

2.  In the left-side navigation pane, choose **Permissions** \> **Policies**. The Policies page appears.

3.  On the Policies page, click **Create Policy**. The Create Custom Policy page appears.

4.  Specify **Policy Name**, set **Configuration Mode** to Script, and then add a statement in **Policy Document**.

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

    -   Enter the permissions that you want to grant in the `Action` section.

        **Note:** You can use wildcards in the Action section.

    -   Specify `Resource` in the following ways:
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

            Replace `cluster ID` with the ID of the cluster that you want to grant access to.

5.  Click **OK**.

    On the Policies page, you can enter the policy name or note to find the custom RAM policy.

    ![Policy management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0235359951/p47367.png)


## References

**Mappings between RAM actions and API operations**

|Operation|RAM Action|Description|
|---------|----------|-----------|
|AttachInstances|AttachInstances|Adds existing ECS instances to a cluster.|
|CancelClusterUpgrade|CancelK8sCluster|Cancels the upgrade of a cluster.|
|CancelComponentUpgrade|CancelComponentUpgrade|Cancels the upgrade of an add-on.|
|CreateCluster|CreateCluster|Creates a cluster.|
|CreateKubernetesTrigger|GenerateTriggerHook|Creates an application operation trigger.|
|CreateTemplate|CreateTemplate|Creates an application deployment template.|
|DeleteCluster|DeleteCluster|Deletes a cluster.|
|DeleteClusterNodes|DeleteClusterNodes|Removes a node and releases the ECS instance.|
|DeleteKubernetesTrigger|RevokeTriggers|Deletes an application operation trigger.|
|DeleteTemplate|V2DeleteTemplateInfo|Deletes an application deployment template.|
|DescribeAddons|Queryk8sComponentsVersion|Queries the information about add-ons in an ACK cluster.|
|DescribeApiVersion|GetCurrentVersions|Queries the API version.|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|Queries the versions of cluster add-ons.|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|Queries the upgrade status of an add-on in an ACK cluster.|
|DescribeClusterAttachScripts|GetClusterJoinScript|Obtains the script for manually adding nodes to the ACK cluster.|
|DescribeClusterDetail|GetClusterInfo|Queries the details of a cluster.|
|DescribeClusterLogs|GetClusterLogs|Queries the logs of a cluster.|
|DescribeClusterNodes|DescribeClusterNodes|Queries nodes in a cluster.|
|DescribeClusterResources|DescribeClusterResources|Queries all resources in a cluster.|
|DescribeClusters|GetClustersByUid and GetClusters|Queries all clusters under your account.|
|DescribeClustersV1|ListClusters|Queries all clusters under your account.|
|DescribeClusterUserKubeconfig|GetUserConfig|Obtains the kubeconfig file of an ACK cluster.|
|DescribeClusterV2UserKubeconfig|GetUserConfig|Obtains the kubeconfig file of an ACK cluster.|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|Queries external agents of the Kubernetes API server.|
|DescribeTemplates|V2ListTemplates|Queries deployment templates for an application.|
|DescribeUserQuota|GetUserQuota|Queries resource quotas of your account.|
|GetKubernetesTrigger|GetK8sTrigger|Queries the details about an application operation trigger.|
|GetUpgradeStatus|GetK8sClusterState|Queries the upgrade status of a cluster.|
|InstallClusterAddons|InstallK8sComponents|Installs an add-on for a cluster.|
|ModifyCluster|ModifyCluster|Modifies the information about a cluster.|
|ModifyClusterTags|UpdateClusterTags|Modifies the tags of a cluster.|
|PauseClusterUpgrade|UpgradeCluster|Suspends the upgrade of a cluster.|
|PauseComponentUpgrade|PauseComponentUpgrade|Suspends the upgrade of an add-on.|
|ReBindSecurityGroup|ReBindSecurityGroup|Rebinds the cluster to the security group.|
|RemoveClusterNodes|DeleteClusterNodes|Removes nodes from a cluster.|
|ResumeComponentUpgrade|ResumeComponentUpgrade|Resumes the upgrade of an add-on.|
|ResumeUpgradeCluster|UpgradeCluster|Resumes the upgrade of a cluster.|
|ScaleCluster|ScaleCluster|Scales out a cluster.|
|ScaleOutCluster|V2ScaleCluster|Adds worker nodes to a cluster.|
|UnInstallClusterAddons|UnInstallK8sComponents|Uninstalls an add-on from a cluster.|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|Updates the expiration time of custom configurations.|
|UpgradeCluster|UpgradeCluster|Upgrades a cluster.|
|UpgradeClusterAddons|UpgradeK8sComponents|Upgrades the add-ons of a cluster.|

