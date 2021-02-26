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

