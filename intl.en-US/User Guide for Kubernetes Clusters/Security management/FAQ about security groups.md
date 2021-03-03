# FAQ about security groups

This topic provides answers to some commonly asked questions about security groups to which the ECS instances in your Container Service for Kubernetes \(ACK\) cluster belong.

-   [Why do containers fail to communicate with each other?](#section_7in_bdt_tfu)
-   [How do I specify a security group for an ACK cluster?](#section_y2v_i2p_x4f)

## Why do containers fail to communicate with each other?

The following section analyzes the causes of the network failure in ACK clusters due to security group settings and provides solutions.

-   Cause: The inbound rule in which **Authorization Object** is **Pod CIDR Block** and **Protocol Type** is **All** is deleted.
    1.  Log on to the [Container Service for Kubernetes console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, click the cluster name or click **Details** in the **Actions** column.
    4.  On the **Cluster Resources** tab, click the value next to **VPC**.

        ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5432854061/p148432.png)

    5.  In the Network Resources section, click the number in the **Security Groups** card.

        ![Security Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37002.png)

    6.  Find the security group that you want to manage, and click **Add Rules** in the **Actions** column.
    7.  On the Inbound tab, click **Add Security Group Rule**.
    8.  Specify **Protocol Type** and **Authorization Object**, and click **OK**.

        **Note:**

        -   Set **Protocol Type** to **All**.
        -   Set **Authorization Object** to the **Pod CIDR Block** of the cluster.
        -   You can find **Pod CIDR Block** in the Cluster Information section on the Basic Information tab.

            ![Cluster Information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8255359951/p37009.png)

        -   For more information about **Authorization Object**, see [Scenarios for security groupsConfiguration guide for ECS security groups](/intl.en-US/Security/Security groups/Scenarios for security groups.md).
        The inbound rule is added. Its **Authorization Object** is the **Pod CIDR Block** of the cluster and **Protocol Type** is **All**.

-   Cause: The new ECS instance and the Kubernetes cluster belong to different security groups.

    1.  Log on to the [Container Service for Kubernetes console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and click the cluster name or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears.
    4.  On the **Cluster Resources** tab, click the value next to **VPC**.

        ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5432854061/p148432.png)

    5.  On the VPC Details page, click the number in the **Security Groups** card in the Network Resources section. You are redirected to the Security Groups page in the ECS console to view the details of the security group.

        ![Network Resources](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37002.png)

    6.  On the Security Groups page, view the name of the security group.

        ![Security Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37012.png)

    7.  Log on to the ECS console. In the left-side navigation pane, choose **Instances and Images** \> **Instances**.
    8.  On the Instances page, find the instance that you want to manage, and choose **More** \> **Network and Security Group** \> **Add to Security Group** in the **Actions** column. The Add to Security Group dialog box appears.

        ![Instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37135.png)

    9.  In the **Security Group** drop-down list, enter the security group name that you obtained in Step 6.

        ![Add an ECS instance to a security group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37136.png)

    10. Click **OK**.
    Verify the result

    1.  Log on to the ECS console. In the left-side navigation pane, choose **Instances and Images** \> **Instances**. On the Instances page, click the name of the instance that is added to the security group.
    2.  In the left-side navigation pane, click **Security Groups**.
    3.  On the Security Groups tab, the ECS instance is added to the security group to which the Kubernetes cluster belongs.

        ![Security Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37607.png)


## How do I specify a security group for an ACK cluster?

You cannot specify a security group for an ACK cluster. A default security group is automatically specified for an ACK cluster when the cluster is created. You can modify the rules of the default security group.

