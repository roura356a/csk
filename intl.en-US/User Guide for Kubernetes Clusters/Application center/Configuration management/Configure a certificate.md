---
keyword: [Application Center, certificate]
---

# Configure a certificate

This topic describes how to add a Transport Layer Security \(TLS\) certificate and an SSH known host to a cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) by Application Center in the ACK console. You must configure a TLS certificate and an SSH known host before you can connect to a repository that supports only HTTPS.

## Add a TLS certificate

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Application Center** \> **Application**.

3.  Click **Certificates** to go to the Repository certificates page.

4.  Click **ADD TLS CERTIFICATES**. In the Create TLS repository certificate pane, set the **Repository server name** and **TLS certificate \(PEM format\)** parameters.

    **Note:** The hostname in the TLS certificate must match the repository server name. Otherwise, the system fails to create the certificate.

5.  Click **CREATE**.


## Add an SSH known host

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Application Center** \> **Application**.

3.  Click **Certificates** to go to the Repository certificates page.

4.  Click **ADD SSH KNOWN HOSTS**. In the Create SSH known host entries pane, set the **SSH known hosts data** parameter.

5.  Click **CREATE**.


**Related topics**  


[Connect to a repository](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Configuration management/Connect to a repository.md)

