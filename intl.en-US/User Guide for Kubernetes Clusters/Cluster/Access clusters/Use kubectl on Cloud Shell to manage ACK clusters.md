---
keyword: [Cloud Shell, Kubernetes cluster, K8s]
---

# Use kubectl on Cloud Shell to manage ACK clusters

Cloud Shell is a web-based command-line tool provided by Alibaba Cloud. You can use Cloud Shell on any browser to run commands to manage Alibaba Cloud resources. This topic describes how to use kubectl on Cloud Shell to manage Container Service for Kubernetes \(ACK\) clusters in the ACK console.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

To manage ACK clusters by using kubectl, you must first install and set up the kubectl client. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md). You can also open Cloud Shell in the ACK console and run kubectl commands on Cloud Shell to manage ACK clusters.

Cloud Shell is a web-based command line tool. You can use Cloud Shell on any browser to run commands to manage Alibaba Cloud resources. When you start Cloud Shell, it automatically creates a dedicated Linux VM free of charge. The VM is pre-installed with cloud management tools and system tools, including Python, Java, Node.js, Alibaba Cloud CLI, cURL, SSH, kubectl, Fun, Terraform, Ansible, and Vim. You can use these tools and the [web integrated development environment \(IDE\)](https://docs.gitlab.com/ee/user/project/web_ide/) editor provided by Cloud Shell to manage cloud resources. For more information, see [What is Cloud Shell?]().

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.

4.  Click ![cloudshell.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9074183061/p141958.png) and click Mount File Storage.

    You can mount an Apsara File Storage NAS \(NAS\) file system to persist commonly used scripts and files. Otherwise, these files are deleted when the instances in the cluster are deleted. You can click **Create Now** or **Skip** based on your requirements.


You can use kubectl on Cloud Shell to manage ACK clusters.

**Note:** When you open Cloud Shell, Cloud Shell automatically reads the kubeconfig file of the cluster. Then, you can use kubectl to manage the cluster.

For example, run the following command to query pods:

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

![Manage the cluster2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3435359951/p34727.png)

**Related topics**  


[Use Cloud Shell]()

[Tools supported by Cloud Shell]()

[Download files from Cloud Shell]()

