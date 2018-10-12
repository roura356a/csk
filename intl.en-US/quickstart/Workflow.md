# Workflow {#concept_vdx_ck4_m2b .concept}

The complete workflow for Container Service is as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15912/15393150447633_en-US.png)

**Step 1: Create a cluster.**

You can select the network environment of the cluster, and set the number of nodes and configurations for the cluster.

If you use a sub-account, grant an appropriate role to the sub-account. For more information, see [Role authorization](../../../../reseller.en-US/User Guide/Kubernetes cluster/授权管理/Role authorization.md#).

**Step 2: Create an application by using an image or orchestration template.**

Select an existing image or orchestration template, or create a new image or orchestration template.

If your application is composed of services supported by multiple images, create the application by using an orchestration template.

**Step 3: Check the application status and the information of relevant services and containers after the deployment.**

