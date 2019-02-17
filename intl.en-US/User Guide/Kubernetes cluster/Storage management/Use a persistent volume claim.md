# Use a persistent volume claim {#task_ib4_ylf_j2b .task}

On the Container Service console, use an image or a template to deploy an application, so that you can use a persistent volume claim. In this example, an image is used to create an application.  If you want to use a persistent volume claim with the template, see [EN-US\_TP\_6936.md\#](reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud cloud disks.md#).

-   You have created a Kubernetes cluster. For more information, see [EN-US\_TP\_6880.md\#](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#). 
-   If you have already created a persistent volume claim, use the cloud disk to create a cloud disk volume claim PVC disk. For more information, see [EN-US\_TP\_15369.md\#](reseller.en-US/User Guide/Kubernetes cluster/Storage claim management/Create a persistent storage volume claim.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane. Enter the Deployment List page and click **Create by image** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16692/155041255710727_en-US.png)

3.  On the Basic Information page, configure the application name, deploy the cluster, and the namespace. Then click **Next**.  

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15504125576754_en-US.png)

4.  On the Application Configuration page, select Image. Then configure the cloud storage type of data volume, cloud disk, NAS, and OSS types are supported. In this example, use the cloud storage volume claim and click **Next**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15504125576755_en-US.png)

5.  See [EN-US\_TP\_6899.md\#](reseller.en-US/User Guide/Kubernetes cluster/Application Management/Create a service.md#) to configure the test-nginx application, and click **Create**. 
6.  After the application is created, click **Apply** \> **Container Group** in the left-side navigation pane. Find the container group to which the application belongs, and click **Details**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15504125576759_en-US.png)

7.  On the Container Group details page, click **Storage** to view the container group is properly bound to the PVC disk. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15370/15504125576761_en-US.png)


