# How do I rename an SLB instance that I created in Cloud Controller Manager \(CCM\) of V1.9.3.10 or an earlier version? {#concept_184803 .concept}

To rename the SLB instance, you must manually add a specific tag to the SLB instance before you can rename it. To do so complete the following steps:

**Note:** You can directly rename an SLB instance that you created in CCM of a version later than V1.9.3.10. This is because the system automatically adds a tag to the SLB instance created in these versions of CCM.

1.  Log on to the Master node of the target Kubernetes cluster. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
2.  Run the `# kubectl get svc -n ${namespace} ${service}` command to view the IP address of the service provided by the target SLB instance.

    **Note:** 

    -   You must replace $\{namespace\} and $\{service\} with the target namespace and service names.
    -   The service type is displayed as `LoadBalancer`.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/158824/155607662444550_en-US.png)

3.  Run the following command to generate the tag required by the target SLB instance:

    `# kubectl get svc -n ${namespace} ${service} -o jsonpath="{.metadata.uid}"|awk -F "-" '{print "kubernetes.do.not.delete: "substr("a"$1$2$3$4$5,1,32)}'`

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/158824/155607662644551_en-US.png)

4.  Log on to the [SLB console](https://slb.console.aliyun.com)[SLB console](https://partners-intl.console.aliyun.com/#/sls). Then, select the corresponding region to which the target SLB instance belongs, and use the IP addressed obtained in step 2 to search for the SLB instance.
5.  Add a tag for the target SLB instance according to the obtained key and its value shown in the preceding figure \(in step 3\). For more information, see [Manage tags](../../../../intl.en-US/User Guide/Server Load Balancer instance/Manage tags.md#).

