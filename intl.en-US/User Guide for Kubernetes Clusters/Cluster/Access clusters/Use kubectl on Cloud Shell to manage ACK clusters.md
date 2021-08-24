---
keyword: [Cloud Shell, Kubernetes clusters, K8s]
---

# Use kubectl on Cloud Shell to manage ACK clusters

Cloud Shell is a web-based command-line tool provided by Alibaba Cloud. You can use Cloud Shell to run commands in a browser to manage Alibaba Cloud resources. This topic describes how to use kubectl on Cloud Shell in the ACK console to manage Container Service for Kubernetes \(ACK\) clusters.

Cloud Shell can connect to ACK clusters only from the Internet. Therefore, you must make sure that the API server of the cluster that you want to access is accessible over the Internet. For more information, see [Control public access to the API server of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Access the Kubernetes API server over the Internet.md).

If you want to manage ACK clusters by using kubectl, you must first install and set up the kubectl client. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md). You can also open Cloud Shell in the ACK console. The system automatically loads the kubeconfig file of the cluster. Then, you can use kubectl on Cloud Shell to manage the cluster without installation or configuration.

Cloud Shell is a web-based command-line tool. You can run Cloud Shell in a browser to manage Alibaba Cloud resources. When you start Cloud Shell, Cloud Shell automatically creates a Linux VM for you. The Linux VM is free of charge. The VM is pre-installed with cloud management tools and system tools, including Python, Java, Node.js, Alibaba Cloud CTI, cURL, SSH, kubectl, Fun, Terraform, Ansible, and Vim. You can use these tools with [Web IDE](https://docs.gitlab.com/ee/user/project/web_ide/) provided by Cloud Shell to manage cloud resources. For more information, see [What is Cloud Shell?]().

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.

4.  Click ![cloudshell.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9074183061/p141958.png) and then click Mount File Storage.

    You can mount an Apsara File Storage NAS \(NAS\) file system to persist commonly used scripts and files. This prevents the files from being deleted when the instances in the cluster are released. You can click **Create Now** or **Skip** based on your requirements.


You can use kubectl on Cloud Shell to manage ACK clusters.

**Note:** When you open Cloud Shell, the system automatically loads the kubeconfig file of the cluster. Then, you can use kubectl to manage the cluster.

For example, you can run the following command to query the pods of the cluster:

```
kubectl get pod
```

Expected output:

```
NAME                             READY   STATUS    RESTARTS   AGE
nginx-dynamic-5b4bdb64c4-gxqs5   1/1     Running   0          178m
web-0                            1/1     Running   0          34d
web-1                            1/1     Running   0          34d
```

![Manage the cluster 2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3435359951/p34727.png)

**Related topics**  


[Use Cloud Shell]()

[Tools supported by Cloud Shell]()

[Download files from Cloud Shell]()

