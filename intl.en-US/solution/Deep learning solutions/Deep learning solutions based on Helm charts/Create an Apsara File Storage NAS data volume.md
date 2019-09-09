# Create an Apsara File Storage NAS data volume {#task_bh5_dll_vdb .task}

Before you create an Apsara File Storage NAS data volume, you must have a storage package in the region where the Kubernetes cluster is located. You can use an existing storage package or purchase a new storage package. For more information, see [Purchase a storage package](https://help.aliyun.com/document_detail/27524.html).

1.  Log on to the [Apsara File Storage NAS console](https://nas.console.aliyun.com/). 
2.  On the File System List page, select the region where the GPU cluster is located and click **Create File System**. 

    ![](images/9093_en-US.png)

3.  In the dialog box that appears, configure the following information to create an Apsara File Storage NAS file system. 

    ![](images/9094_en-US.png)

    -   **Region**: Select the region where the Kubernetes cluster is located. For example, you can select **China East 1 \(Hangzhou\)**.
    -   **Storage Type**: Select Capacity-type.
    -   **Protocol Type**: Select NFS \(including NFSv3 and NFSv4\).
    -   **Zone**: Select **China East 1 Zone B**. Different zones in the same region can communicate with each other.
    -   **Storage Package**: Select the storage package you have prepared.
4.  Click **OK** to complete the file system creation. You can see the file system in the list. 
5.  In the Actions column, click **Manage** to go to the file system details page. 

    ![](images/9095_en-US.png)

6.  In the Mount Point navigation bar, click **Add Mount Point** and configure the information to add a mount point that is in a VPC. For more information, see the "Create a mount point" section of the [Mount a file system on an ECS instance running Windows](https://help.aliyun.com/document_detail/60431.html) topic. 

    ![](images/9096_en-US.png)

7.  After you add the mount point, you can view the address of the mount point. 

    ![](images/9097_en-US.png)


