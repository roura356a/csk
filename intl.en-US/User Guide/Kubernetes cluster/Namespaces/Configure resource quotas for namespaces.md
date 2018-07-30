# Configure resource quotas for namespaces {#task_sxr_qvx_b2b .task}

-   You have created a Kubernetes cluster. For more information, see [Create a cluster](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
-   You have created a namespace **test**. For more information, see [Create a namespace](intl.en-US/User Guide/Kubernetes cluster/Namespaces/Create a namespace.md#).
-   Connect to the master node SSH IP address of the cluster. For more information, see [Access Kubernetes clusters by using SSH](intl.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).

By default, a running pod can use the CPU and memory of nodes unlimitedly, which means any pod can use the computing resources of the cluster unlimitedly, and the pods of a namespace may use up the cluster resources.

One of the important functions of namespaces is to act as a virtual cluster for multiple purposes and meeting the requirements of multiple users. Therefore, configuring the resource quotas for a namespace is a kind of best practice.

You can configure the resource quotas for a namespace, including CPU, memory, and number of pods. For more information, see [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

1.   Log on to the [Container Service console](https://cs.console.aliyun.com/). 
2.   Under Kubernetes, click**Application** \> **Deployment**in the left-side navigation pane. Click **Create by template** in the upper-right corner. 

     ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14723/15329349446193_en-US.png) 

3.   On the Deploy templates page, select the cluster and namespace \(**test** in this example\) from the Clusters and Namespace drop-down lists. Use a custom template or the example template Resource – ResourceQuota. 

     ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14723/15329349456194_en-US.png) 

    You must configure the ResourceQuota template according to your cluster resources and the plan for the namespace resources. In this example, the template is as follows:

    ```
    apiVersion: v1
     kind: ResourceQuota # restrict resource quota for cpu, memory, storage, pvc, replicationcontroller, pods, service, secret, configmap
     metadata:
       name: quota
     # namespace: users-namespace # specify your namespace to apply resource quota
     spec:
       hard:
         cpu: "2" # adjust limits of cpu for your namespace
         memory: 4Gi # adjust memory upper limits for your namesapce
         requests.storage: 1024G # adjust request of storage size for your namespace
         persistentvolumeclaims: "50" # adjust number of pvc for your namespace
         pods: "50" #adjust number of Pod in your namespace
         replicationcontrollers: "10" # adjust number of ReplicationController in your namespace
         services: "10" # adjust number of service for your namespace
         secrets: "100" # adjust number of secrets for your namespace
         configmaps: "100" # adjust number of configmap for your namespace
    ```

4.   You have configured the resource quotas for this namespace. Connect to the master node SSH IP address and run the following command to view the resource quotas and usage of this namespace. 

    ```
    # kubectl describe quota quota --namespace=test
     Name: quota
     Namespace: test
     Resource Used Hard
     --------                ----  ----
     configmaps 0 100
     cpu 0 2
     memory 0 4Gi
     persistentvolumeclaims 0 50
     pods 0 50
     replicationcontrollers 0 10
     requests.storage 0 1024G
     secrets 1 100
     services 0 10
    ```


