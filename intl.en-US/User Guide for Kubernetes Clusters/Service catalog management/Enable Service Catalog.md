# Enable Service Catalog

You can enable Service Catalog in the Container Service for Kubernetes \(ACK\) console.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **Service Catalog**. On the Service Catalog page, find the cluster that you want to view.

3.  If you have not deployed Service Catalog, you are prompted to install Service Catalog.

4.  After Service Catalog is installed, you can find a service broker that is automatically installed on the Service Catalog page. You can click mariadb-broker to view the details.

    **Note:** Service Catalog is implemented based on an additional API server and a controller. After Service Catalog is installed, a namespace named catalog is created.

5.  In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster that you want to view and choose **More** \> **Dashboard** in the **Actions** column.

6.  In the left-side navigation pane of the Kubernetes dashboard, select catalog from the Namespace drop-down list. You can find that the resource objects related to the Service Catalog API server and controller are deployed in this namespace.


After you have completed the preceding steps, Service Catalog is enabled. You can create a managed service by using the service broker in Service Catalog and apply the managed service to your applications.

