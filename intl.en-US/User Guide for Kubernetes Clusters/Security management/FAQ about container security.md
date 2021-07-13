---
keyword: FAQ about security groups
---

# FAQ about container security

This topic provides answers to some frequently asked questions about security groups.

-   [Why do containers fail to communicate with each other?](#section_7in_bdt_tfu)
-   [How do I specify a security group for an ACK cluster?](#section_y2v_i2p_x4f)
-   [Can I disable cluster auditing when I create a cluster or enable cluster auditing after the cluster is created?](#section_jnt_2kt_s4u)
-   [How do I renew the certificate of a dedicated Kubernetes cluster and renew the certificates of the components in the cluster?](#section_7qf_ryg_o3w)
-   [How do I fix the "no providers available to validate pod request" error during pod creation?](#section_lil_6sd_usm)
-   [Why am I unable to use existing Secrets in a new namespace?](#section_js7_ku8_n29)
-   [How do I fix the mount error when I mount the default token?](#section_5ic_ehe_2om)
-   [How do I query the auditing log?](#section_zhr_qfz_ofq)
-   [How do I collect diagnostic data from nodes in an ACK cluster?](~~86761~~)
-   [How do I collect diagnostic data from nodes in an edge Kubernetes cluster?](~~149335~~)
-   [The kubelet log of an ACK cluster that uses the CentOS 7.6 operating system contains the "Reason:KubeletNotReady Message:PLEG is not healthy:" information](~~178340~~)

## Why do containers fail to communicate with each other?

The following section describes the causes of network failures of different security group settings and provides solutions to the failures.

-   Cause: The inbound rule in which **Authorization Object** is **Pod CIDR Block** and **Protocol Type** is **All** is deleted.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  On the **Cluster Resources** tab, click the link to the right of **VPC**.

        ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4024707161/p148432.png)

    5.  On the Resources tab, click the number below **Security Group**.
    6.  Find the security group that you want to manage, and click **Add Rules** in the **Actions** column.
    7.  On the Inbound tab, click **Add Rule**.
    8.  Configure **Protocol Type**, **Port Range**, and **Authorization Object**. Then, click **Save**.

        **Note:**

        -   Set **Protocol Type** to **All**.
        -   Set **Authorization Object** to the pod CIDR block of the cluster.

            You can find the pod CIDR block in the Cluster Information section of the cluster details page in the ACK console.

            ![Cluster information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8255359951/p37009.png)

            For more information about **Authorization Object**, see [Scenarios for security groupsConfiguration guide for ECS security groups](/intl.en-US/Security/Security groups/Scenarios for security groups.md).

        The following figure shows the added inbound rule. The **Authorization Object** is the **Pod CIDR Block** of the cluster and **Protocol Type** is **All**.

-   Cause: The new Elastic Compute Service \(ECS\) instance and the Kubernetes cluster belong to different security groups.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  On the **Cluster Resources** tab, click the link to the right of **VPC**.

        ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4024707161/p148432.png)

    5.  On the VPC Details page, click the number below **Security Group** on the Resources tab. You are redirected to the Security Groups page in the ECS console. You can view the details of the security group on this page.
    6.  On the Security Groups page, view the name of the security group.

        ![Security Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37012.png)

    7.  In the left-side navigation pane of the ECS console, choose **Instances & Images** \> **Instances**.
    8.  On the Instances page, find the instance that you want to manage, and choose **More** \> **Network and Security Group** \> **Add to Security Group** in the **Actions** column. The Add to Security Group dialog box appears.

        ![Instances list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37135.png)

    9.  In the **Security Group** drop-down list, enter the security group name that you obtained in Step 6.

        ![Add an ECS instance to a security group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37136.png)

    10. Click **OK**.
    Verify the result

    1.  In the left-side navigation pane of the ECS console, choose **Instances & Images** \> **Instances**. On the Instances page, click the name of the instance that is added to the security group.
    2.  On the Security Groups tab, verify that the ECS instance is added to the security group to which the Kubernetes cluster belongs.

        ![Security Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255359951/p37607.png)


## How do I specify a security group for an ACK cluster?

You cannot specify a security group for an ACK cluster. A default security group is automatically specified for an ACK cluster when the cluster is created. You can modify the rules of the default security group.

## Can I disable cluster auditing when I create a cluster or enable cluster auditing after the cluster is created?

Yes. You can disable cluster auditing when you create a cluster and enable cluster auditing after the cluster is created. For more information, see [Enable cluster auditing](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable cluster auditing.md).

## How do I renew the certificate of a dedicated Kubernetes cluster and renew the certificates of the components in the cluster?

-   Approximately two months before a certificate expires, an internal message and SMS notification are sent to remind you about the expiration of the certificate. You can go to the clusters page in the console and click Renew to renew the certificate. For more information, see [Update the Kubernetes cluster certificates that are about to expire](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Manage clusters/Cluster certificates/Update the Kubernetes cluster certificates that are about to expire.md).
-   For more information about how to renew an expired certificate, see [Update expired certificates of a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Manage clusters/Cluster certificates/Update expired certificates of a Kubernetes cluster.md).

## How do I fix the "no providers available to validate pod request" error during pod creation?

-   If no custom pod security policy \(PSP\) is defined, the error appears because you deleted the default PSP. You can restore the default PSP to fix the error. For more information, see [Use pod security policies](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Use pod security policies.md).
-   If you want to use a custom PSP, see [Use a PSP](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use a PSP.md).

## Why am I unable to use existing Secrets in a new namespace?

Secrets are scoped to namespaces. You must create new Secrets in a new namespace.

## How do I fix the mount error when I mount the default token?

The following error message is returned:

```
Normal Scheduled 13m default-scheduler Successfully assigned dev/alibaba-demo-67fcdbfb8-zklnp to cn-hangzhou.10.7.3.16   Warning FailedMount 13m (x2 over 13m) kubelet, cn-hangzhou.10.7.3.16 MountVolume.SetUp failed for volume 'default-token-8twx9' : mount failed: exit status 1 Mounting command: systemd-run Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/pods/62d39b35-9a4d-11ea-9870-c24d56a0e904/volumes/kubernetes.io~secret/default-token-8twx9 --scope -- mount -t tmpfs tmpfs /var/lib/kubelet/pods/62d39b35-9a4d-11ea-9870-c24d56a0e904/volumes/kubernetes.io~secret/default-token-8twx9 Output: Failed to start transient scope unit: Argument list too long   Warning FailedCreatePodContainer 3m40s (x49 over 13m) kubelet, cn-hangzhou.10.7.3.16 unable to ensure pod container exists: failed to create container for [kubepods burstable pod62d39b35-9a4d-11ea-9870-c24d56a0e904] : Argument list too long
```

The systemd version is outdated.

-   Upgrade systemd. For more information, see [systemd](https://github.com/kubernetes/kubernetes/issues/57345#issuecomment-427349235).
-   Run the `systemctl daemon-reload` command to reload units. For more information, see [systemd](https://github.com/kubernetes/kubernetes/issues/57345#issuecomment-356179385).

## How do I query the auditing log?

**Query the auditing log about Role-Based Access Control \(RBAC\) operations**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the cluster information page, click the **Cluster Resources** tab. Then, click the link to the right of Log Service Project.

5.  On the **Log Storage** \> **Logstores** page, click the `audit-<cluster_id>` Logstore that you want to query and click **Search & Analysis**.

    ![43](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6854242261/p265346.png)

6.  In the upper-right corner, click **15 Minutes\(Relative\)** to specify the time period that you want to query.

    **Note:** Select a time period that covers the time when errors occurred. For example, 3 days, 7 day, or 15 days.

7.  In the **Search & Analyze** search bar, enter the following SQL statement and then click **Search & Analyze**.

    ```
    requestURI: "rbac.authorization.k8s.io" not (verb: get or verb: watch) 
    ```

8.  Click the ![45](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4969152261/p265359.png) icon. In the **Log Download** dialog box, select **Download All Logs with Cloud Shell** and click **OK**.


**Query the auditing log about ConfigMap operations**

In the **Search & Analyze** search bar, enter the following SQL statement and click **Search & Analyze**. For more information, see [How do I query the auditing log?](#section_zhr_qfz_ofq).

```
requestURI: "configmaps" and <configmap\_name\> not (verb: get or verb: watch or verb: list) 
```

**Note:** Replace <configmap\_name\> with the name of the ConfigMap that you want to query.

**Query the auditing log about Deployment scaling operations**

In the **Search & Analyze** search bar, enter the following SQL statement and click **Search & Analyze**. For more information, see [How do I query the auditing log?](#section_zhr_qfz_ofq).

```
requestURI: deployments and (verb: update or verb: patch) and replicas and deployments and <deployment\_name\> not deployment-controller
```

**Note:** Replace <deployment\_name\> with the name of the Deployment that you want to query.

