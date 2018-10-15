# Schedule a pod to a specified node {#concept_imx_4bn_vdb .concept}

You can add a node label and then configure the `nodeSelector` to schedule a pod to  a specified node.  For more information about the implementation principle of nodeSelector, see [nodeselector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector).

For business scenario needs, to deploy a service used for management and control to a master node, or deploy services to a machine with an SSD disk,  you can use this method to schedule pods to specified nodes.

## Prerequisites {#section_n3r_jcn_vdb .section}

You have successfully created a Kubernetes cluster. For more information, see [EN-US\_TP\_6880.md\#](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).

## Step 1 Add a node label {#section_o3r_jcn_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under the Kubernetes menu, click **Clusters** \> ** Nodes ** in the left-side navigation pane.
3.  Select the cluster from the Cluster drop-down list and then click **Label Management** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847744463_en-US.png)

4.  Select one or more nodes by selecting the corresponding check boxes and then click **Add Tag**.  In this example, select a worker node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847744472_en-US.png)

5.  Ener the name and value of the label in the displayed dialog box and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847744474_en-US.png)

    The node label `group:worker` is displayed on the Label Management page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847744475_en-US.png)


You can also add a node label by running the command  `kubectl label nodes <node-name> <label-key>=<label-value>`.

## Step 2 Deploy a pod to a specified node {#section_g2j_dkt_12b .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under the Kubernetes menu, click **Applications** \> **Deployment** in the left-side navigation pane.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847754476_en-US.png)

4.  Configure the template to deploy a pod. After completing the configurations, click **DEPLOY**.

    -   **Clusters**: Select a cluster.
    -   **Namespace**: Select the namespace to which the resource object belongs. In this example, use default as the namespace.
    -   **Resource Type**: Select Custom in this example.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847754477_en-US.png)

    The orchestration template in this example is as follows:

    ```
    apiVersion: v1
     kind: Pod
     metadata:
       labels:
         name: hello-pod
       name: hello-pod
     spec:
       containers:
         - image: nginx
           imagePullPolicy: IfNotPresent
           name: hello-pod
           ports:
             - containerPort: 8080
               protocol: TCP
           resources: {}
           securityContext:
             capabilities: {}
             privileged: false
           terminationMessagePath: /dev/termination-log
       dnsPolicy: ClusterFirst
       restartPolicy: Always
       nodeSelector:                    
         group: worker  ##The same as the node label configured in the preceding step.
     status :{}
    ```

5.  A message indicating the deployment status is displayed after you click **DEPLOY**.  After the successful deployment, click **Kubernetes  Dashboard** in the message to go to the dashboard and check the deployment status.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847754478_en-US.png)

6.  Click the pod name to view the pod details.

    You can view the information such as the pod label and node ID,  which indicates the pod is successfully deployed to a node with the label `group:worker`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6900/15395847754479_en-US.png)


