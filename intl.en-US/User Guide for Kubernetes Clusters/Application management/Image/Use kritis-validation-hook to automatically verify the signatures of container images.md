---
keyword: [container image verification, kritis-validation-hook]
---

# Use kritis-validation-hook to automatically verify the signatures of container images

This topic describes how to use Container Registry, Key Management Service \(KMS\), Security Center, and kritis-validation-hook to automatically verify the signatures of container images. This allows you to deploy only container images that have been signed by trusted authorities. This also reduces the risk of malicious code execution in your environment.

## Step 1: Install kritis-validation-hook

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.

5.  In the **Optional Add-ons** section of the Add-ons page, find **kritis-validation-hook** and click **Install** in the Actions column of the add-on.

6.  Authorize your cluster to run kritis-validation-hook.

    If you want to run kritis-validation-hook in a managed Kubernetes cluster, you must grant the cluster the permissions to access related resources. For more information, see [Permissions](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/kritis-validation-hook introduction.mdsection_c7v_xxm_q88).

    **Note:** By default, dedicated Kubernetes clusters are granted the permissions to run kritis-validation-hook.


## Step 2: Create a key that is used to sign images in the KMS console

1.  Log on to the [KMS console](https://kms.console.aliyun.com).

2.  In the upper-left corner of the Keys page, click **Create Key**.

3.  In the Create Key dialog box, set **Alias Name** and **Description**.

4.  **Note:** Specify **RSA\_2048** as **Key Spec** and specify **SIGN/VERIFY** as **Purpose**.

5.  Click **Advanced** and specify **Key Material Source**.

    -   **Alibaba Cloud KMS**: Use KMS to generate key material.
    -   **External**: Import key material from an external source. For more information about how to import key material, see [Import key material](/intl.en-US/Key Service/Key type/Use symmetric keys/Import key material.md).

        **Note:** If you select External, you must also select **I understand the implications of using the external key materials key**.

6.  Click **OK**.


## Step 3: Create a witness that uses the key in the Security Center console

1.  Log on to the [Security Center console](https://yundun.console.aliyun.com/?p=sas).

2.  In the left-side navigation pane, choose **Operation** \> **Container Signature**.

3.  On the **Witness** tab, click **Create Witness**.

    Set the witness information. For more information, see [Use the container signature feature](/intl.en-US/Operation/Use the container signature feature.md).

4.  Click **OK**.


## Step 4: Enable image signing in Container Registry

1.  Log on to the [Container Registry console](https://cr.console.aliyun.com/).

2.  On the Instances page, find the Enterprise Edition instance that you want to manage and click the instance name or click **Manage** in the Actions column.

3.  In the left-side navigation pane of the Manage page, choose **Repositories** \> **Namespaces**.

    Create a namespace and enable image signing for images in the namespace. For more information, see [Basic operations on a namespace]().

4.  Enable image signing for the created namespace.

    When you create signature signing rules, select the witness that is created in Step 3.

    1.  In the left-side navigation pane, choose **Content Trust** \> **Image Signature**.

    2.  On the Image Signature page, click **Create Signature Rule**.

        For more information about how to configure signature signing rules, see [Configure a signature rule for automatic image signing]().

        ![ACR signature](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0345359951/p100024.png)


## Step 5: Enable signature verification in Security Center

To enable signature verification for a namespace, add and enable a security policy for the cluster in the Security Center console.

1.  Log on to the [Security Center console](https://yundun.console.aliyun.com/?p=sas).

2.  In the left-side navigation pane, choose **Operation** \> **Container Signature**.

3.  On the **Security Policy** tab, click **Add Policy**.

    For more information about how to add a security policy, see [Create a security policy](/intl.en-US/Operation/Use the container signature feature.md).

    ![Signature verification](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0345359951/p100026.png)

4.  Click **OK**.


## Step 6: Check whether signature verification is enabled

**Note:** Only images in digest format are supported.

Run the following command to check whether signature verification is enabled:

-   After signature verification is enabled for the default namespace, unsigned images cannot be deployed in the namespace.

    To specify an image by tag, run the following command:

    ```
    kubectl -n default create deployment not-sign --image=alpine:3.11 -- sleep 10
    ```

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
    ```

    To specify an image by digest, run the following command:

    ```
    kubectl -n default create deployment not-sign --image=alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 -- sleep 10
    ```

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 is not attested
    ```

-   Push an image to a namespace where image signature signing is enabled, and verify that the image can be deployed after the image is signed.

    ```
    docker push kritis-demo-registry-vpc.cn-hongkong.cr.aliyuncs.com/kritis-demo/alpine:3.11
    ```

    ```
    The push refers to repository [kritis-demo-registry-vpc.cn-hongkong.cr.aliyuncs.com/kritis-demo/alpine]
    5216338b40a7: Pushed
    3.11: digest: sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 size: 528
    ```

    To accept requests to deploy signed images, run the following command:

    ```
    kubectl -n default create deployment is-signed --image=kritis-demo-registry-vpc.cn-hongkong.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45 -- sleep 10
    ```

    ```
    deployment.apps/is-signed created
    ```


**References**  


For more information about kritis-validation-hook, see [kritis-validation-hook introduction](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/kritis-validation-hook introduction.md).

For more information about the release notes for kritis-validation-hook, see [Release notes](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/Release notes.md).

  


