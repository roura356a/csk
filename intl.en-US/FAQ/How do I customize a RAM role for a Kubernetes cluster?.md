# How do I customize a RAM role for a Kubernetes cluster? {#concept_wdt_bpx_3hb .concept}

You cannot create a RAM role manually. However, when cluster Worker nodes are created, a Worker RAM role is automatically created for the Kubernetes cluster. You can then add policies to the Worker RAM role to grant the role the required permissions.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Clusters** .
4.  Click the target cluster name to view the cluster details.
5.  In the Cluster Resources area, click **Worker RAM Role**.
6.  On the RAM Roles page, click the policy name on the Permission tab page to view the policy details.
7.  Click **Modify Policy Document** on the Policy Document tab page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154834/155488524443453_en-US.png)

8.  Add the target policies to the Policy Document area, and then click **OK**. In this example, the policies containing the permissions of scaling and deleting clusters are added to the policy document. For more information about permissions supported by a Kubernetes cluster, see [Container Service RAM actions](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Create custom authorization policies.md#table_pzw_5s2_xdb).

    ```
    {
                "Action": [
                  "cr:ScaleCluster",
                  "cr:DeleteCluster"
                ],
                "Resource": "*",
                "Effect": "Allow"
             }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154834/155488524443455_en-US.png)


