---
keyword: [Application center, Repositories]
---

# Connect to a repository

After you connect to a repository, you can select it when you create an application. This topic describes how to use SSH or HTTPS to connect to a repository.

## Use SSH to connect to a repository

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Application Center** \> **Configuration** and click **Repositories**. The Repositories page appears.

3.  Click **CONNECT REPO USING SSH**. On the Connect repo using SSH page, configure the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Name \(required for Helm\)**|The name of the repository. You can specify a custom name.|
    |**Repository URL**|The URL of the repository. **Note:** You must specify a repository URL that can be accessed from the Internet. |
    |**SSH private key data**|The SSH private key for identity verification. You can connect to the repository only after you pass the identity verification.|
    |**Skip server verification**|Specifies whether to skip the GitHub server verification.|
    |**Enable LFS support \(Git only\)**|Specifies whether to enable Git Large File Storage \(LFS\). If you enable this feature, you can store large files in the GitHub repository that you connect to.|

4.  Click **CONNECT**.


## Use HTTPS to connect to a repository

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Application Center** \> **Configuration** and click **Repositories**. The Repositories page appears.

3.  Click **CONNECT REPO USING HTTPS**. On the Connect repo using HTTPS page, configure the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Type**|The type of the repository. Select **git** or **helm**.|
    |**Repository URL**|The URL of the repository. **Note:** You must specify a repository URL that can be accessed from the Internet. |
    |**Username \(optional\)**|The user name and password for identity verification. You can connect to the repository only after you pass the identity verification.|
    |**Password \(optional\)**|
    |**TLS client certificate \(optional\)**|The TLS client certificate and certificate key for identity verification. You can connect to the repository only after you pass the identity verification.|
    |**TLS client certificate key \(optional\)**|
    |**Skip server verification**|Specifies whether to skip the GitHub server verification.|
    |**Enable LFS support \(Git only\)**|Specifies whether to enable Git LFS. If you enable this feature, you can store large files in the GitHub repository that you connect to.|

4.  Click **CONNECT**.


**Related topics**  


[Configure a certificate](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Configuration management/Configure a certificate.md)

