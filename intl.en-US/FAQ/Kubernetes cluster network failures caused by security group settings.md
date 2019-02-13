# Kubernetes cluster network failures caused by security group settings {#concept_ezp_rn4_kgb .concept}

This topic describes the Kubernetes cluster network failures caused by cluster security group settings, and provides corresponding resolutions.

## Symptom {#section_yvh_yn4_kgb .section}

Containers cannot communicate with each other over the Kubernetes cluster network.

## Causes {#section_qsr_b13_mgb .section}

-   A relevant ingress security group rule is removed. The following are the details of the rule: the ingress **Authorization Objects** is **Pod Network CIDR** and the **Protocol Type** is **All**.
-   Newly added ECS instances and the Kubernetes cluster are located in different security groups.

## Resolution {#section_dlk_d13_mgb .section}

**Cause 1**: A relevant ingress security group rule is removed. The following are details of the rule: the ingress **Authorization Objects** is **Pod Network CIDR** and the **Protocol Type** is **All**.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Click the target cluster name to view cluster details.
4.  In the Cluster Resource area, click **VPC**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137001_en-US.png)

5.  In the Network Resources area, click the number on the right of **Security Group**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137002_en-US.png)

6.  In the **Actions** column of the target security group, click **Add Rules**.
7.  On the Ingress page, click **Add Security Group Rule** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137576_en-US.png)

8.  Set **Protocol Type** and **Authorization Objects**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137007_en-US.png)

    Select **All** from the **Protocol Type** drop-down list.

    Enter the **Pod Network CIDR** of the Kubernetes cluster for **Authorization Objects**.

    **Note:** 

    -   You can view the **Pod Network CIDR** of the Kubernetes cluster in the Cluster Information area on the cluster basic information page.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137009_en-US.png)

    -   For more information about the**Authorization Objects** settings, see [Scenarios](../../../../../reseller.en-US/User Guide/Security groups/Scenarios.md#).

**Verify the results**

The required ingress security group rule is added. The following are details of the rule: the ingress **Authorization Objects** is **Pod Network CIDR** and the **Protocol Type** is **All**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137003_en-US.png)

**Cause 2**: Newly added ECS instances and the Kubernetes cluster are located in different security groups.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Clusters**.
3.  Click the target **Cluster Name**.****
4.  In the Cluster Resource area, click **VPC**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137001_en-US.png)

5.  On the VPC Details page, click the number on the right of **Security Group** in the Network Resources area.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137002_en-US.png)

6.  On the Security Groups page, view the target security group name.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496137012_en-US.png)

7.  In the Elastic Compute Service console, click **Instances** in the left-side navigation pane.
8.  On the Instances page, choose **More** \> **Network and Security Group** \> **Add to Security Group** in the **Actions** column of the target instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496237135_en-US.png)

9.  Click the drop-down arrow on the right of the **Security Group** box, and enter the cluster security group name obtained in step 6.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496237136_en-US.png)

10. Click **OK**.

**Verify the results**

1.  In the left-side navigation pane of the Elastic Computer Service console, click **Instances** and then click the target instance.
2.  In the left-side navigation pane, click **Security Groups**.
3.  The Security Groups area shows that the target ECS instance has been added to the security group to which the Kubernetes cluster belongs.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/97289/155007496237607_en-US.png)


