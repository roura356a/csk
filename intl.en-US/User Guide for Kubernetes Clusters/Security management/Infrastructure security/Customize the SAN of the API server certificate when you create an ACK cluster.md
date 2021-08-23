---
keyword: [customize the SAN of the API server certificate, SAN]
---

# Customize the SAN of the API server certificate when you create an ACK cluster

The API server certificate of a Container Service for Kubernetes \(ACK\) cluster contains the Subject Alternative Name \(SAN\) field. By default, this field contains the domain name and IP address of the cluster. This field also contains the elastic IP address \(EIP\) and private IP address of the Server Load Balancer \(SLB\) instance that is associated with the API server of the cluster. You can customize the SAN if you want to access the ACK cluster by using a proxy or through a different domain name. This topic describes how to customize the SAN of the API server certificate when you create an ACK cluster. This topic also describes how to update the SAN of the API server certificate for an existing ACK cluster.

Only managed Kubernetes clusters and serverless Kubernetes \(ASK\) clusters are supported. Managed Kubernetes clusters include standard managed Kubernetes clusters and professional managed Kubernetes clusters. This section lists the methods that you can use to create three types of managed Kubernetes clusters. You need only to create one cluster in this example.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md)
-   [Create a managed Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md)

SAN is an extension to X.509. SAN allows you to associate various values with an SSL certificate by adding the values to the `subjectAltName` field. The values can be IP addresses, domain names, URIs, or email addresses.

## Limits

-   You cannot customize the SAN of the API server certificate when you create an ASK cluster.
-   You can update the SAN of the API server certificate for an existing ASK cluster.

## Customize the SAN of the API server certificate when you create an ACK cluster

This example describes how to customize the SAN of the API server certificate when you create a managed Kubernetes cluster.

On the **Cluster Configurations** wizard page, click **Show Advanced Options**. In the **Custom Certificate SANs** field, enter the SAN that you want to add to the API server certificate. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

**Note:** You can enter multiple values in the **Custom Certificate SANs** field. The values can be IP addresses, domain names, or URIs that comply with the conventions. Separate multiple values with commas \(,\).

![an2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2199959161/p256859.png)

In the preceding figure, two domain names and an IP address are entered in the **Custom Certificate SANs** field.

## Update the SAN of the API server certificate for an existing ACK cluster

**Note:** After you update the SAN of the API server certificate for an existing ACK cluster, the API server is restarted. Perform this operation during off-peak hours.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, click the **Basic Information** tab and click **Update** on the right side of **Custom Certificate SANs**.

5.  In the **Update Custom SAN** dialog box, set the **Custom Certificate SANs** parameter.

6.  Click **OK**.


