# Use a persistent volume claim {#task_ib4_ylf_j2b .task}

In the Container Service console, use an image or a template to deploy an application, so that you can use a persistent volume claim \(PVC\). In this example, an image is used to create an application.  If you want to use a PVC through a template, see [Use Alibaba Cloud cloud disk volumes](reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud cloud disk volumes.md#).

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#). 
-   You have created a PVC. In this topic, a pvc-disk PVC is created by using a disk. For more information, see [Create a persistent volume claim](reseller.en-US/User Guide/Kubernetes cluster/Storage management/Create a persistent volume claim.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Application** \> **Deployment**. Then click **Create by Image** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16692/155116214510727_en-US.png)

3.  Set the application name and select the target cluster and the namespace. Then click **Next**.  

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15511621456754_en-US.png)

4.  Select an image, set the data volume of disk type, and then click **Next**. Disk, NAS, and OSS types are available. In this example, an existing disk PVC is used. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15511621456755_en-US.png)

5.  Set services for the test-nginx application, and then click **Create**. 
6.  In the left-side navigation pane, choose **Application** \> **Pods**. Then click **Details** on the right of the pod to which the test-nginx application belongs. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15511621456759_en-US.png)

7.  Click **Volumes** to verify that the target pod is associated with the pvc-disk. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15511621456761_en-US.png)


