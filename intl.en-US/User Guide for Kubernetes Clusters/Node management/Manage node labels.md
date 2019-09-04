# Manage node labels {#concept_ssx_dml_vdb .concept}

You can manage node labels in the Container Service console, including adding node labels in batches, filtering nodes by using a label, and deleting a node label quickly.

For how to use node labels to schedule pods to specified nodes, see [Set node scheduling](reseller.en-US/User Guide for Kubernetes Clusters/Node management/Set node scheduling.md#).

## Prerequisite {#section_edr_qml_vdb .section}

You have successfully created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).

## Add node labels in batches {#section_r4x_fnl_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Kubernetes **Clusters** \> **Nodes**in the left-side navigation pane.
3.  Select the cluster from the Clusters drop-down list and then click **Label Management** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694352_en-US.png)

4.  Select one or more nodes by selecting the corresponding check boxes and then click **Add Tag**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694353_en-US.png)

5.  Ener the name and value of the label in the displayed dialog box and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694354_en-US.png)

    Nodes with the same label are displayed on the Label Management page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694355_en-US.png)


## Filter nodes by using a label {#section_q4p_hnl_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Kubernetes **Clusters** \> **Nodes**in the left-side navigation pane.
3.  Select the cluster from the Clusters drop-down list and then click **Label Management** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694352_en-US.png)

4.  Click the label at the right of a node to filter nodes by using the label. In this example, click `group:worker`.

    Nodes with the label `group:worker` are filtered.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694357_en-US.png)


## Delete a node label {#section_iyr_d4l_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Kubernetes **Clusters** \> **Nodes**in the left-side navigation pane.
3.  Select the cluster from the Clusters drop-down list and then click **Label Management** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694352_en-US.png)

4.  Click the delete \(x\) button of a node label, for example, `group:worker`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694356_en-US.png)

    Click Confirm in the displayed dialog box. The node label is deleted.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6891/15675657694359_en-US.png)


