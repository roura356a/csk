---
keyword: [RAM, custom permission policies]
---

# Create custom RAM permission policies

This topic describes how to create custom permission policies and attach these policies to Resource Access Management \(RAM\) users. In this example, custom permission policies to query, modify, and delete clusters of Container Service for Kubernetes \(ACK\) are created and attached to the current RAM user.

Before you create custom permission policies, you must fully understand the basic structure and syntax of the permission policy language. For more information, see [Permission policy language](https://www.alibabacloud.com/help/zh/doc-detail/28663.htm).

The system policies that are provided by ACK are coarse-grained and may not meet your business requirements. You can create custom policies to provide fine-grained management on ACK. For example, if you want to control the operation permissions on a specified cluster, you must create a custom permission policy to meet this requirement.

## Procedure

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) with your Alibaba Cloud account or as an authorized RAM user.

2.  In the left-side navigation pane, choose **Permissions** \> **Policies** to go to the Policies page.

3.  On the Policies page, click **Create Policy** to go to the Create Custom Policy page.

4.  Enter a **name** for the policy. Set **Configuration Mode** to Script. Enter the **policy content** as shown in the following figure.

    ![Create a custom permission policy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0235359951/p10480.png)

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
             "acs:cs:*:*:cluster/<cluster ID>"
         ]
     }],
     "Version": "1"
    }
    ```

    Configure the policy content based on the following descriptions:

    -   Enter the permission that you want to grant in the `Action` key.

        **Note:** Wildcard characters are supported in the Action key.

    -   Specify `Resource` in the following ways:
        -   Authorize access to only one cluster

            ```
            "Resource": [
                 "acs:cs:*:*:cluster/<cluster ID>"
             ]
            ```

        -   Authorize access to multiple clusters

            ```
            "Resource": [
                 "acs:cs:*:*:cluster/<cluster ID>",
                 "acs:cs:*:*:cluster/<cluster ID>"
             ]
            ```

        -   Authorize access to all clusters

            ```
            "Resource": [
                 "*"
             ]
            ```

            Replace `<cluster ID>` with the ID of the cluster that you want to manage.

5.  After the configurations are completed, click **OK**.

    On the Policies page, enter the name or description of the created policy into the search bar and click the search icon. You can find the newly created custom permission policy.

    ![Policy management](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0235359951/p47367.png)


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
|DescribeClusters|GetClustersByUid|Queries all clusters under your account.|
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

