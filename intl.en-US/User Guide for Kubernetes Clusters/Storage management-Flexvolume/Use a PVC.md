# Use a PVC

In the Container Service for Kubernetes \(ACK\) console, you can create an application from an image or a template. When you create the application, you can specify a persistent volume claim \(PVC\) that the application uses to request physical storage. In this example, an application is created from an image. You can also create an application from a template and specify a PVC in the template. For more information, see [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md).

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A PVC is created. In this example, a PVC named pvc-disk is created based on cloud storage. For more information, see [Create a PVC](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Create a Persistent Volume Claim.md).

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Workloads**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure the basic settings.

    For more information, see [Configure basic settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    ![Basic settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8309301161/p10973.png)

7.  On the **Container** wizard page, select an image and configure a data volume based on cloud storage. Cloud disks, NAS file systems, and Object Storage Service \(OSS\) buckets can be specified as cloud storage. In this example, select the pvc-disk PVC and click **Next**. For more information, see [Configure the containers](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

8.  On the **Advanced** wizard page, create a service for the test-nginx application and click **Create**.

9.  After the application is created, you are redirected to the **Creation Task Submitted** page. You can click **View Details** to view application details.

    The Basic Information page of the newly created test-nginx application appears by default.

10. On the **Pods** tab, find the pod to which the application belongs and click **View Details**.

11. You are redirected to the Overview page of the pod. Click the **Volumes** tab. Verify that the pod is associated with the pvc-disk PVC.


