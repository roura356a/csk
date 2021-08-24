---
keyword: [API server, SLB, access control]
---

# Configure network ACLs for the API server of an ACK cluster

When you create a Container Service for Kubernetes \(ACK\) cluster, the system automatically creates an internal-facing Server Load Balancer \(SLB\) instance for the API server of the cluster. The SLB instance serves as the internal endpoint of the API server. If you require fine-grained access control over the API server, you can configure network access control lists \(ACLs\) for the listener that listens on port 6443. The network ACLs are used as whitelists or blacklists for the API server. This topic describes how to control access to the API server by configuring a listener for the internal-facing SLB instance of the API server.

SLB allows you to enforce access control by configuring listeners. You can configure access control when you create a listener or modify access control settings for an existing listener. For more information, see [Overview](/intl.en-US/Classic Load Balancer/User Guide/Access control/Overview.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Cluster Information page, click the **Basic Information** tab.

5.  On the Basic Information tab, find **API Server Internal Endpoint** in the **Cluster Information** section. Then, click **Set access control**.

    ![SLB](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1839425061/p175375.png)

6.  You are redirected to the **Access Control Settings** panel in the [SLB console](https://slb.console.aliyun.com/slb/cn-hangzhou/slbs/lb-bp1y52wyx5gqrw5mfnzpw/listeners/tcp/6443/acl-setting?spm=5176.2020520152.0.0.70a916dd6RPIeo). Turn on **Enable Access Control**, set Access Control Method to Whitelist or Blacklist, and then select the required network ACL.

    Before you enable access control, you must create a network ACL. For more information about how to create a network ACL, see [Create an access control list](/intl.en-US/Classic Load Balancer/User Guide/Access control/Access control lists/Create an access control list.md). For more information about how to enable access control, see [Enable access control](/intl.en-US/Classic Load Balancer/User Guide/Access control/Enable access control.md).

    **Note:**

    -   If you set a whitelist, you must add the CIDR block 100.104.0.0/16 of the ACK cluster and the CIDR block of the vSwitch to the whitelist. The vSwitch is used by the master node on which the API server runs.
    -   If you set a blacklist, you must not add the CIDR block 100.104.0.0/16 of the ACK cluster or the CIDR block of the vSwitch to the blacklist. The vSwitch is used by the master node on which the API server runs.
7.  Click **OK**.


