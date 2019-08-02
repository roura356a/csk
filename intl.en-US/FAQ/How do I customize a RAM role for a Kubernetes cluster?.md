# How do I customize a RAM role for a Kubernetes cluster? {#concept_wdt_bpx_3hb .concept}

You cannot create a RAM role manually. However, when cluster Worker nodes are created, a Worker RAM role is automatically created for the Kubernetes cluster. You can then add policies to the Worker RAM role to grant the role the required permissions.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
4.  Click the target cluster name.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/156473183444670_en-US.png)

5.  In the Cluster Resources area, click **Worker RAM Role**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/156473183444686_en-US.png)

6.  On the RAM Roles page, click the policy name on the Permission tab page to view the policy details.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/156473183444693_en-US.png)

7.  On the Policy Document tab page, click **Modify Policy Document**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154834/156473183543453_en-US.png)

8.  On the displayed page, add the target policies to the Policy Document area, and then click **OK**.

    In this example, the policies containing the permissions of scaling and deleting clusters are added to the policy document. For more information about permissions supported by a Kubernetes cluster, see [Table 1](../../../../reseller.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom authorization policies.md#table_pzw_5s2_xdb).

    ``` {#codeblock_mhs_27d_nb3}
    {
                "Action": [
                  "cs:ScaleCluster",
                  "cs:DeleteCluster"
                ],
                "Resource": "*",
                "Effect": "Allow"
             }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/156473183544694_en-US.png)


