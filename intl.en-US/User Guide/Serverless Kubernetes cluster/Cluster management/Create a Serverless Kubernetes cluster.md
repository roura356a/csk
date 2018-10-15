# Create a Serverless Kubernetes cluster {#task_e3c_311_ydb .task}

You can create a Serverless Kubernetes cluster quickly and easily in the Container Service console. 

Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), and the [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding service.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane to enter the Cluster List page. 
3.  Click **Create a Serverless Kubernetes cluster** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15395899745636_en-US.png)

4.  Enter the cluster name. 

    Cluster name can only contain 1 - 63 characters, including numbers, Chinese characters, English characters, and hyphens \(-\).

5.  Select the region and availability zone where the cluster resides. 

    **Note:** Serverless Kubernetes cluster is currently in beta stage, and only East China 2 \(Shanghai\) region is available.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15395899745637_en-US.png)

6.  Set the cluster network type. 

    -   **Network type**: Kubernetes clusters only support the VPC network type.
    -   **VPC**: **Auto Create** and **Use existing** options are supported.

        **Note:** 

        -   **Auto Create**: System automatically creates VPC, NAT gateway and configures SNAT rules in VPC when the cluster is created. 
        -   **Use existing**: Select the target VPC and VSwitch from the list of existing VPCs. To access the public network, for example, to download container images, configure the NAT gateway. We recommend that container images be uploaded to the Alibaba Cloud image service in the region where the cluster resides, and pull the image by the VPC address. 
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15395899745638_en-US.png)

7.  Check whether to enable the PrivateZone-based service discovery feature, which allows you to access the service by the domain name within the cluster VPC. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15395899745994_en-US.png)

    **Note:** Before using this feature, confirm that you have enabled the PrivateZone service, see [EN-US\_TP\_14696.md\#](reseller.en-US/User Guide/Serverless Kubernetes cluster/Service discovery based on Alibaba Cloud DNS Private Zone.md#).

8.  Check the **Serverless Kubernetes service protocol**. 
9.  Click **Create cluster** to start the deployment. 

After the cluster is created, you can view the cluster in the Kubernetes cluster list in the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15395899745639_en-US.png)

You can also click **Manage** at the right of the cluster to view the basic and connection information of this cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15395899745640_en-US.png)

