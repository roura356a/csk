---
keyword: [Application center, Certificates]
---

# Configure a certificate

Before you connect to a repository that supports HTTPS connections, you must configure a certificate for connecting to the repository. This topic describes how to add a TLS certificate and an SSH known host to the application center.

## Add a TLS certificate

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Click **ADD TSL CERTIFICATE**. On the Create TLS repository certificate page, specify **Repository server name** and **TLS certificate \(PEM format\)**.

    **Note:** The repository server name and the TLS certificate must be in the PEM format. Otherwise, the certificate creation fails.

3.  Click **Create**.


## Add an SSH known host

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Application Center** \> **Configuration** and click **Certificates**. The Repository certificates page appears.

3.  Click **ADD SSH KNOWN HOSTS**. On the Create SSH known host entries page, specify **SSH known hosts data**.

4.  Click **CREATE**.


**Related topics**  


[Connect to a repository](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Configuration management/Connect to a repository.md)

