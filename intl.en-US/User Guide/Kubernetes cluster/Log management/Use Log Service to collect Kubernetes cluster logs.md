# Use Log Service to collect Kubernetes cluster logs {#concept_nsj_sxm_t2b .concept}

Log Service is integrated with Kubernetes clusters of Alibaba Cloud Container Service. You can enable Log Service when creating a cluster to quickly collect container logs for the Kubernetes cluster, such as the standard output of the container and text files of the container.

## Enable Log Service when creating a Kubernetes cluster {#section_ohf_y5r_gfb .section}

If you have not created any Kubernetes clusters, follow steps in this section to enable Log Service:

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Clusters** in the left-side navigation pane and click **Create Kubernetes Cluster** in the upper-right corner.
3.  For how to configure a cluster on the creation page, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
4.  Drag to the bottom of the page and select the **Using Log Service** check box. The log plug-in will be installed in the newly created Kubernetes cluster.
5.  When you select the Using Log Service check box, project options are displayed. A project is the unit in Log Service to manage logs. For more information about projects, see [Project ](../../../../reseller.en-US/Product Introduction/Basic concepts/Project .md#). Currently, two ways of using a project are available:
    -   Select an existing project to manage collected logs.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793109250_en-US.png)

    -   The system automatically creates a new project to manage collected logs. The project is automatically named `k8s-log-{ClusterID}`, where ClusterID represents the unique identifier of the created Kubernetes cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793119251_en-US.png)

6.  After you complete the configurations, click **Create** in the upper-right corner. In the displayed dialog box, click **OK**.

    After the cluster creation is completed, the newly created Kubernetes cluster is displayed on the cluster list page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793119449_en-US.png)


## Manually install Log Service components in a created Kubernetes cluster {#section_shf_y5r_gfb .section}

If you have created a Kubernetes cluster, following instructions in this section to use Log Service:

-   Log Service components are not installed. Manually install the components.
-   Log Service components are installed but in an earlier version. Upgrade the components. If you do not upgrade the components, you can only use the Log Service console or custom resource definition \(CRD\) to configure log collection.

**Check the Log Service component version**

1.  Use Cloud Shell to connect to the target Kubernetes cluster.

    For more information, see [Use kubectl commands in Cloud Shell to manage a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#).

2.  Run the following command to fast determine whether an upgrade or migration operation is required:

    ``` {#codeblock_tjt_aey_q02}
    $ kubectl describe daemonsets -n kube-system logtail-ds | grep ALICLOUD_LOG_DOCKER_ENV_CONFIG                    
    ```

    -   If `ALICLOUD_LOG_DOCKER_ENV_CONFIG: true` is output, the components can be used directly without requiring upgrade or migration.
    -   If other results are output, check the components further.
3.  Run the following command to determine whether Helm is used to install the components.

    ``` {#codeblock_m9h_ty2_izc}
    $ helm get alibaba-log-controller | grep CHART
    CHART: alibaba-cloud-log-0.1.1                    
    ```

    -   0.1.1 in the output indicates the version of the Log Service components. Please use the version of 0.1.1 and later. If the version is too early, please see [Upgrade Log Service components](#section_b3f_y5r_gfb) to upgrade the components. If you have used Helm to install the components of a valid version, you can skip next steps.
    -   If no results are output, the components are installed by using Helm. But the DaemonSet installation method might be used. Follow the next step to check further.
4.  DaemonSet can be an old one or a new one:

    ``` {#codeblock_1bo_qsb_hdc}
    $ kubectl get daemonsets -n kube-system logtail                    
    ```

    -   If no result is output or `No resources found.` is output, the Log Service components are not installed. For information about the installation method, see [Manually install Log Service components](#section_zhf_y5r_gfb).
    -   If the correct result is output, an old DaemonSet is used to install the components which require upgrade. For information about upgrading the components, see [Upgrade Log Service components](#section_b3f_y5r_gfb).

## Manually install Log Service components {#section_zhf_y5r_gfb .section}

1.  Use Cloud Shell to connect to the target Kubernetes cluster.

    For more information, see [Use kubectl commands in Cloud Shell to manage a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#).

2.  Run the `echo $ALIBABA_CLOUD_ACCOUNT_ID` command in Cloud Shell to get the ID of your account in Alibaba Cloud.
3.  Run the following command:

    **Note:** For this command, you need to specify the following parameters as required: `${your_k8s_cluster_id}`, `{your_ali_uid}`, and `{your_k8s_cluster_region_id}`.

    ``` {#codeblock_0qf_9ox_8cv}
    wget https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh -O alicloud-k8s-log-installer.sh; chmod 744 ./alicloud-k8s-log-installer.sh; ./alicloud-k8s-log-installer.sh --cluster-id ${your_k8s_cluster_id} --ali-uid ${your_ali_uid} --region-id ${your_k8s_cluster_region_id}
    ```

    **Parameter description** 

    -   your\_k8s\_cluster\_id: indicates you Kubernetes cluster ID.
    -   your\_ali\_uid: indicates the ID of your account in Alibaba Cloud. It can be obtained in step [2](#).

**Installation example**

``` {#codeblock_qer_mim_20v}
[root@iZbp******biaZ ~]# wget https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh -O alicloud-k8s-log-installer.sh; chmod 744 ./alicloud-k8s-log-installer.sh; ./alicloud-k8s-log-installer.sh --cluster-id c77a*****************0106 --ali-uid 19*********19 --region-id cn-hangzhou
--2018-09-28 15:25:33--  https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh
Resolving acs-logging.oss-cn-hangzhou.aliyuncs.com... 118.31.219.217, 118.31.219.206
Connecting to acs-logging.oss-cn-hangzhou.aliyuncs.com|118.31.219.217|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2273 (2.2K) [text/x-sh]
Saving to: ‘alicloud-k8s-log-installer.sh’

alicloud-k8s-log-installer.sh                      100%[================================================================================================================>]   2.22K  --. -KB/s    in 0s

2018-09-28 15:25:33 (13.5 MB/s) - ‘alicloud-k8s-log-installer.sh’ saved [2273/2273]

--2018-09-28 15:25:33--  http://logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/kubernetes/alibaba-cloud-log.tgz
Resolving logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com... 118.31.219.49
Connecting to logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com|118.31.219.49|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2754 (2.7K) [application/x-gzip]
Saving to: ‘alibaba-cloud-log.tgz’

alibaba-cloud-log.tgz                              100%[================================================================================================================>]   2.69K  --. -KB/s    in 0s

2018-09-28 15:25:34 (79.6 MB/s) - ‘alibaba-cloud-log.tgz’ saved [2754/2754]

[INFO] your k8s is using project : k8s-log-c77a92ec5a3ce4e64a1bf13bde1820106
NAME:   alibaba-log-controller
LAST DEPLOYED: Fri Sep 28 15:25:34 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/CustomResourceDefinition
NAME                                   AGE
aliyunlogconfigs.log.alibabacloud.com  0s

==> v1beta1/ClusterRole
alibaba-log-controller  0s

==> v1beta1/ClusterRoleBinding
NAME                    AGE
alibaba-log-controller  0s

==> v1beta1/DaemonSet
NAME        DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE SELECTOR  AGE
logtail-ds  2        2        0      2           0          <none>         0s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
alibaba-log-controller  1        1        1           0          0s

==> v1/Pod(related)
NAME                                     READY  STATUS             RESTARTS  AGE
logtail-ds-6v979                         0/1    ContainerCreating  0         0s
logtail-ds-7ccqv                         0/1    ContainerCreating  0         0s
alibaba-log-controller-84d8b6b8cf-nkrkx  0/1    ContainerCreating  0         0s

==> v1/ServiceAccount
NAME                    SECRETS  AGE
alibaba-log-controller  1        0s


[SUCCESS] install helm package : alibaba-log-controller success.
			
```

## Upgrade Log Service components {#section_b3f_y5r_gfb .section}

If you have installed Log Service components of an early version through Helm or DaemonSet, upgrade or migrate the components as follows.

**Note:** You need to use Cloud Shell to connect to the target Kubernetes cluster. For more information, see [Use kubectl commands in Cloud Shell to manage a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#).

Use Helm to upgrade Log Service components \(recommended\)

1.  Run the following command to download the latest Helm package of Log Service components:

    ``` {#codeblock_41u_jl8_zgb}
    wget http://logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/kubernetes/alibaba-cloud-log.tgz -O alibaba-cloud-log.tgz
    					
    ```

2.  Upgrade the components by using helm upgrade. The command is as follows:

    ``` {#codeblock_4lj_r2x_xwd}
    helm get values alibaba-log-controller --all > values.yaml && helm upgrade alibaba-log-controller alibaba-cloud-log.tgz --recreate-pods -f values.yaml
    					
    ```


Use DaemonSet to upgrade Log Service components

You can upgrade Log Service components by modifying the DaemonSet template. If your image account is acs, upgrade the image tag to the latest version that can be viewed in the image repository. If your image account is acs, upgrade the image tag to the latest version that can be viewed in the image repository.

**Note:** 

-   If upgrading the tag has not enabled a rolling update of Logtail, you must manually remove the Logtail pod to trigger a Logtail update.
-   You need to check whether Logtail runs on all nodes, including Master nodes. If Logtail does not run on all nodes, you must set [tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) for Logtail.

``` {#codeblock_ey3_2z5_wm1}
tolerations:
- operator: "Exists"
```

For more information, see [Latest Helm package configurations](http://logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/kubernetes/alibaba-cloud-log.tgz).

## DaemonSet migrate {#section_e3f_y5r_gfb .section}

This upgrade method is applicable to the situation that you find the components are installed through the old DaemonSet when you **check the Log Service component version**. This method does not support configuring Log Service in Container Service. You can upgrade the components as follows:

1.  At the end of the installation command, add a parameter which is the name of the project of Log Service used by your Kubernetes cluster.

    For example, if the project name is k8s-log-demo and the cluster ID is c12ba2028cxxxxxxxxxx6939f0b, then the installation command is:

    ``` {#codeblock_e21_9lh_v6d}
    wget https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh -O alicloud-k8s-log-installer.sh; chmod 744 ./alicloud-k8s-log-installer.sh; ./alicloud-k8s-log-installer.sh --cluster-id c12ba2028cxxxxxxxxxx6939f0b --ali-uid 19*********19 --region-id cn-hangzhou --log-project k8s-log-demo
    						
    ```

2.  After you complete the installation, log on the [Log Service console](https://partners-intl.console.aliyun.com/#/sls).
3.  In the Log service console, apply the history collection configuration of the project and Logstore to the new machine group `k8s-group-${your_k8s_cluster_id}`.
4.  After one minute, the history collection configuration is unbound from the history machine group.
5.  When log collection is normal, you can delete the previously installed Logtail DaemonSet.

**Note:** During the upgrade, some logs are duplicated. The CRD configuration management takes effect only for the configuration created by using CRD. The history configuration does not support the CRD management because the history configuration is created by using the non-CRD mode.

## Configure Log Service when creating an application {#section_g3f_y5r_gfb .section}

Container Service allows you to configure Log Service to collect container logs when creating an application. Currently, you can use the console or a YAML template to create an application.

**Create an application by using the console**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**. Then, click **Create by Image** in the upper-right corner.
3.  Configure **Name**, **Cluster**, **Namespace**, **Replicas**, and **Type**, and then click **Next**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793119451_en-US.png)

4.  On the Container page, select the Tomcat image and configure container log collection.

    The following describes only configurations related to Log Service. For information about other application configurations, see [Create a deployment application by using an image](reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

5.  Configure **Log Service**. Click the **+** sign to create a configuration which consists of a **Logstore name** and a **log path**.

    -   Logstore name: Specify a Logstore in which collected logs are stored. If your specified Logstore does not exist, the system automatically creates the Logstore in the project of Log Service with which the cluster is associated .

        **Note:** A Logstore name cannot contain underscores \(\_\). You can use hyphens \(-\) instead.

    -   Log path: Specify the path where logs to be collected reside. For example, use /usr/local/tomcat/logs/catalina. \*.log to collect text logs of tomcat.

        **Note:** If you specify the log path as stdout, the container standard output and standard error output will be collected.

        Each configuration is automatically created as a configuration for the corresponding Logstore. By default, the simple mode \(by row\) is used to collect logs. To use more collection modes, log on go to the Log Service console, and enter the corresponding project \(prefixed with k8s-log by default\) and Logstore to modify the configuration.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793119460_en-US.png)

6.  Custom tag. Click the **+** sign to create a new custom tag. Each custom tag is a key-value pair which will be added to collected logs. You can use a custom tag to mark container logs. For example, you can create a custom tag as a version number.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793119473_en-US.png)

7.  When you complete all the configurations of the container, click **Next** in the upper-right corner to perform further configurations. For more information, see [Create a deployment application by using an image](reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#).

**Create an application by using a YAML template**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**. Then, click **Create by Template** in the upper-right corner.
3.  The syntax of the YAML template is the same as the Kubernetes syntax. To specify the collection configuration for the container, you need to use env to add **collection configuration** and **custom tag** for the container, and create corresponding volumeMounts and volumns. The following is a simple pod example:

    ``` {#codeblock_yi7_oea_8fh}
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-demo
    spec:
      containers:
      - name: my-demo-app
        image: 'registry.cn-hangzhou.aliyuncs.com/log-service/docker-log-test:latest'
        env:
        ######### Configure environment variables  ###########
        - name: aliyun_logs_log-stdout
          value: stdout
        - name: aliyun_logs_log-varlog
          value: /var/log/*.log
        - name: aliyun_logs_mytag1_tags
          value: tag1=v1
        ###############################
        ######### Configure vulume mount ###########
        volumeMounts:
        - name: volumn-sls-mydemo
          mountPath: /var/log
      volumes:
      - name: volumn-sls-mydemo
        emptyDir: {}
      ###############################
    ```

    -   Configure three parts in order based on your needs.
    -   In the first part, use environment variables to create your **collection configuration** and **custom tag**. All environment variables related to configuration are prefixed with `aliyun_logs_`.
    -   Rules for creating the collection configuration are as follows:

        ``` {#codeblock_fju_ai1_993}
        - name: aliyun_logs_{Logstore name}
          value: {log path}                            
        ```

        In the example, create two collection configurations. The `aliyun_logs_log-stdout` env creates a configuration that contains a Logstore named log-stdout and the log path of stdout. The standard output of the container is collected and stored to the Logstore named log-stdout.

        **Note:** A Logstore name cannot contain underscores \(\_\). You can use hyphens \(-\) instead.

    -   Rules for creating **a custom tag** are as follows:

        ``` {#codeblock_1ar_fio_mvx}
        - name: aliyun_logs_{a name without '_'}_tags
          value: {Tag name}={Tag value}                            
        ```

        After a tag is configured, when logs of the container are collected, fields corresponding to the tag are automatically attached to Log Service.

    -   If you specify a non-stdout log path in your collection configuration, create corresponding volumnMounts in this part.

        In the example, the /var/log/\*.log log path is added to the collection configuration, therefore, the /var/log volumeMounts is added.

4.  When you complete a YAML template, click **DEPLOY** to deliver the configurations in the template to the Kubernetes cluster to execute.

## Set environment variables to control log collection {#section_ui7_ytd_l8y .section}

The environment variables of containers support multiple advanced parameters, which that can be used to control the collection of logs. The following table details these parameters.

|Parameter|Description|Example|Remarks|
|---------|-----------|-------|-------|
|`aliyun_logs_{key}`| -   Required. This parameter is used to specify the name of a log collection configuration. The value of `{key}` must start with a letter, and be followed by letters, numbers, and hyphens \(-\). It cannot contain underlines \(\_\).
-   If you do not specify a Logstore by using the optional parameter `aliyun_logs_{key}_logstore`, logs are automatically collected to the Logstore named `{key}`.
-   To collect the standard output of containers, set this parameter as stdout. To collect logs of a specific path, set this parameter as the path.

 | -   ``` {#codeblock_wp8_gkd_zgt}
- name: aliyun_logs_catalina

   stdout
```

-   ``` {#codeblock_x3o_4bd_uzq}
- name: aliyun_logs_access-log

   /var/log/nginx/access.log
```


 | -   By default, the simple collection mode is used to collect logs. If you want to parse collected logs, we recommend that you use the Log Service console to follow the procedure described in [Container text logs](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Container text logs.md#), [Container stdout](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Container stdout.md#), or [Configure Kubernetes log collection on CRD](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Configure Kubernetes log collection on CRD.md#).
-   The value of `{key}` must be unique within the target Kubernetes cluster.

 |
|`aliyun_logs_{key}_tags`|Optional. This parameter is used to identify logs. The value of this parameter is in the format of \{tag-key\}=\{tag-value\}.| ``` {#codeblock_60n_dan_by5}
- name: aliyun_logs_catalina_tags

   app=catalina
```

 |-|
|`aliyun_logs_{key}_project`|Optional. This parameter is used to specify a project in Log Service to store collected logs. If you do not set this parameter, logs are collected to the project that you set for the target Kubernetes cluster.| ``` {#codeblock_uw0_2mg_aru}
- name: aliyun_logs_catalina_project

   my-k8s-project
```

 |You must set the a project in the same region where your Logtail is located.|
|`aliyun_logs_{key}_logstore`|Optional. This parameter is used to specify a Logstore in Log Service. If you do not specify this parameter, the Logstore named `{key}`is used to store collected logs.| ``` {#codeblock_652_tro_4g6}
- name: aliyun_logs_catalina_tags

   my-logstore
```

 |-|
|`aliyun_logs_{key}_shard`|Optional. This parameter is used to specify the number of shards in a Logstore. The value range of this parameter is one to ten. By default, if you do not specify this parameter, two shards are created.| ``` {#codeblock_me7_gih_vsl}
- name: aliyun_logs_catalina_shard

   4
```

 |-|
|`aliyun_logs_{key}_ttl`|Optional. This parameter is used to specify the length of time \(in days\) for which logs can be stored. The value range of this parameter is 1 to 3650. -   If you set this parameter as 3650, logs can be stored permanently.
-   By default, if you do not set this parameter, logs can be stored for 90 days.

 | ``` {#codeblock_gyf_0ti_bx2}
- name: aliyun_logs_catalina_ttl

   3650
```

 |-|
|`aliyun_logs_{key}_machinegroup`|Optional. This parameter is used to specify a machine group. If you do not set this parameter, the default machine group takes effect.| ``` {#codeblock_qo2_19f_qzk}
- name: aliyun_logs_catalina_machinegroup

   my-machine-group
```

 |-|

**Scenario 1: Collect the log data of multiple applications to one Logstore**

Set the aliyun\_logs\_\{key\}\_logstore parameter to implement this task. For example, to collect the stdout logs of two applications to a Logstore named `stdout-logstore`, use this parameter to set the environment variables of two applications.

Set the environment variables of Application 1 as follows:

``` {#codeblock_02l_mmy_gh1}
######### set environment variables ###########
    - name: aliyun_logs_app1-stdout
      value: stdout
    - name: aliyun_logs_app1-stdout_logstore
      value: stdout-logstore
```

Set the environment variables of Application 2 as follows:

``` {#codeblock_3vv_sj5_wgo}
######### set environment variables ###########
    - name: aliyun_logs_app2-stdout
      value: stdout
    - name: aliyun_logs_app2-stdout_logstore
      value: stdout-logstore
```

**Scenario 2: Collect the log data of each application to an exclusive project** 

**Note:** This means that a project stores only the log data of one specific application.

To implement this task, follow these steps to configure each application:

1.  In each project, create a machine group and customize an identifier for the machine group.

    **Note:** The identifier name is in the format of k8s-group-\{cluster-id\}.

2.  For the environment variables of each application, set the following parameter: `project`, `logstore`, and `machinegroup`.

    **Note:** The value of the `machinegroup` parameter is the name of the machine group that you create in step 1.

    The following shows the example settings for environment variables of an application:

    ``` {#codeblock_etp_avu_zzt}
    ######### set environment variables ###########
        - name: aliyun_logs_app1-stdout
          value: stdout
        - name: aliyun_logs_app1-stdout_project
          value: app1-project
        - name: aliyun_logs_app1-stdout_logstore
          value: app1-logstore
        - name: aliyun_logs_app1-stdout_machinegroup
          value: app1-machine-group
    ```


## View logs {#section_t3f_y5r_gfb .section}

In this example, view logs of the tomcat application created in the console. After you complete the application configuration, logs of the tomcat application are collected and stored to Log Service. You can view your logs as follows:

1.  Log on to the [Log Service console](https://partners-intl.console.aliyun.com/#/sls).
2.  In the console, select the project \(k8s-log-\{Kubernetes cluster ID\} by default\) corresponding to the Kubernetes cluster.
3.  In the Logstore list, locate the Logstore specified in your configuration and click **Search**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793129474_en-US.png)

4.  In this example, on the log search page, you can view the standard output logs of the tomcat application and text logs in the container, and you can find your custom tag is attached to log fields.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15643793129541_en-US.png)


## More information {#section_rg2_tdh_hfb .section}

1.  By default, the system use the simple mode to collect your data, that is, to collect data by row without parsing. To perform more complex configurations, see the following Log Service documents and log on to the Log Service console to modify configurations.
    -   [Container text logs](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Container text logs.md#)
    -   [Container stdout](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Container stdout.md#)
2.  In addition to configuring log collection through the console, you can also directly collect logs of the Kubernetes cluster through the CRD configuration. For more information, see [Configure Kubernetes log collection on CRD](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Configure Kubernetes log collection on CRD.md#).
3.  For troubleshooting exceptions, see [Troubleshoot collection errors](../../../../reseller.en-US/FAQ/Log collection/Troubleshoot collection errors.md#).

