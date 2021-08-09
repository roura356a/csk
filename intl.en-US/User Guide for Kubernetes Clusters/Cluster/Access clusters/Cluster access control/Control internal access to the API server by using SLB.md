---
keyword: [API server, SLB, access control]
---

# Control internal access to the API server by using SLB

To enforce internal access control for the API server of a Container Service for Kubernetes \(ACK\) cluster, you can set a whitelist or blacklist for the listener that listens on Port 6443 in the Server Load Balancer \(SLB\) console. This topic describes how to control internal access to the API server by using SLB.

SLB allows you to enforce access control by using listeners. You can configure access control when you create a listener or modify access control settings for an existing listener. For more information, see [Overview](/intl.en-US/Classic Load Balancer/User Guide/Access control/Overview.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Cluster Information page, click the **Basic Information** tab.

5.  On the Basic Information tab, find the **API Server Internal Endpoint** field in the **Cluster Information** section, and click **Set access control** next to the field.

    ![SLB](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1839425061/p175375.png)

6.  You are redirected to the **Access Control Settings** panel in the [SLB console](https://slb.console.aliyun.com/slb/cn-hangzhou/slbs/lb-bp1y52wyx5gqrw5mfnzpw/listeners/tcp/6443/acl-setting?spm=5176.2020520152.0.0.70a916dd6RPIeo). Turn on **Enable Access Control**, set Access Control Method to Whitelist or Blacklist, and then select the required access control list \(ACL\).

    Before you enable access control, you must create the required ACL. For more information about how to enable access control, see [Enable access control](/intl.en-US/Classic Load Balancer/User Guide/Access control/Enable access control.md).

    **Note:**

    If you set a whitelist, you must add the CIDR block 100.104.0.0/16 of the ACK cluster and the CIDR block of the vSwitch to the whitelist. The vSwitch is used by the master node on which the API server runs.

    If you set a blacklist, you must not add the CIDR block 100.104.0.0/16 of the ACK cluster and the CIDR block of the vSwitch to the blacklist. The vSwitch is used by the master node on which the API server runs.

7.  Click **OK**.


