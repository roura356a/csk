# Integration and usage with CloudMonitor {#concept_cwm_bnz_g2b .concept}

## Prerequisites {#section_uzw_fnz_g2b .section}

Check whether `alicloud-monitor-controller` has been deployed in the `kube-system` namespace. If not, upgrade the version of the cluster.

## Procedure {#section_vzw_fnz_g2b .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Deployment** in the left-side navigation pane.
3.  Select the target deployment, click **Monitor** on the right. You can also click **Monitor** on the Deployment page of the built-in kubernetes dashboard.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588610498_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710499_en-US.png)

    In this case, you jump to the corresponding Application group details page of CloudMonitor.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710500_en-US.jpg)

4.  Application group supports monitoring in two dimensions: **group** and **instance**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710501_en-US.jpg)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710502_en-US.jpg)

5.  For alarm settings, the index of group level starts with group, and the instance level index starts with pod.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710503_en-US.jpg)


## Upgrade cluster version {#section_f1x_fnz_g2b .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane to enter the Deployment List page. Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710504_en-US.png)

3.  Select the target cluster, kube-system namespace, and use the following sample template. Then click **Create**.

    **Note:** Replace REGION and CLUSTER\_ID with your actual cluster information, and redeploy heapster yaml template.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710505_en-US.png)

    An example of heapster template is as follows. If you have an earlier version of the heapster in the cluster, you can log on to the Kubernetes cluster and run the `kubectl apply -f xxx.yaml` command to upgrade it.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: heapster 
      namespace: kube-system 
    spec:
      replicas: 1 
      template:
        metadata:
          labels:
            task: monitoring
            k8s-app: heapster
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ''
        spec:
          serviceAccount: admin
          containers:
          - name: heapster
            image: registry. ##REGION##.aliyuncs.com/acs/heapster-amd64:v1.5.1.1
            imagePullPolicy: IfNotPresent
            command:
            - /heapster
            - --source=kubernetes:https://kubernetes.default
            - --historical-source=influxdb:http://monitoring-influxdb:8086
            - --sink=influxdb:http://monitoring-influxdb:8086
            - --sink=socket:tcp://monitor.csk. ##REGION##.aliyuncs.com:8093? clusterId=##CLUSTER_ID##&public=true
    ```

    The example layout of alicloud-monitor-controller is as follows. Run the `kubectl create -f xxx.yaml` command to deploy alicloud-monitor-controller.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: alicloud-monitor-controller
      namespace: kube-system
    spec: 
      replicas: 1 
      template:
        metadata:
          labels:
            task: monitoring
            k8s-app: alicloud-monitor-controller
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ''
        spec:
          hostNetwork: true
          tolerations:
          - effect: NoSchedule
            operator: Exists
            key: node-role.kubernetes.io/master
          - effect: NoSchedule
            operator: Exists
            key: node.cloudprovider.kubernetes.io/uninitialized
          serviceAccount: admin
          containers:
          - name: alicloud-monitor-controller
            image: registry. ##REGION##.aliyuncs.com/acs/alicloud-monitor-controller:v1.0.0
            imagePullPolicy: IfNotPresent
            command:
            - /alicloud-monitor-controller
            - agent
            - --regionId=##REGION##
            - --clusterId=##CLUSTER_ID##
            - --logtostderr
            - --v=4
    ```

4.  Go to the Kubernetes console. In the kube-system namespace, you can see that the two deployments are running, and the upgrade is complete.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710506_en-US.png)


If you do not know the REGION information, you can go to the ECS console and select the region where your cluster resides. The last segment of the page URL address is REGION.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15813/153620588710507_en-US.jpg)

