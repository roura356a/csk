---
keyword: [kritis-validation-hook, verify signatures, container images]
---

# Introduction to kritis-validation-hook

kritis-validation-hook is a key component for verifying image signatures. You can use signature verification to ensure that only images signed by trusted authorities are deployed. This reduces the risk of malicious code execution. This topic provides examples on how kritis-validation-hook is used to verify signatures.

A managed or dedicated Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

kritis-validation-hook is based on the open source project [kritis](https://github.com/grafeas/kritis) and is integrated with [Container Registry](https://www.alibabacloud.com/zh/product/container-registry).This enables signature verification for images that are signed by [Key Management Service \(KMS\)](https://www.alibabacloud.com/zh/products/kms). kritis-validation-hook is integrated with [Security Center](https://www.alibabacloud.com/zh/products/threat-detection), KMS, and Container Registry to implement automated image signing and signature verification. This allows you to build a secure environment for clusters. For more information about how to enable automatic signature verification for container images, see [Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use kritis-validation-hook to automatically verify the signatures of container images.md).

## Authorize the cluster to access resources

To ensure that kritis-validation-hook works as normal, you must grant the following permissions to the Resource Access Management \(RAM\) role of the cluster to access resources:

**Note:**

-   If you use a managed Kubernetes cluster, you must grant the following permissions to the worker RAM role of the cluster.
-   If you use a dedicated Kubernetes cluster, you must grant the following permissions to the master RAM role and the worker RAM role of the cluster.

```
cr:ListInstance
cr:ListMetadataOccurrences
```

If you want to grant the preceding permissions to the RAM role of your cluster, perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Cluster Information page, click the **Cluster Resources** tab and find the RAM Role of the cluster.

    -   If the cluster is a managed Kubernetes cluster, click the RAM role name to the right side of the **Worker RAM Role** field.
    -   If the cluster is a dedicated Kubernetes cluster, you must click the RAM role name to the right side of **Master RAM Role** and also the RAM role name to the right side of **Worker RAM Role**.
5.  On the RAM role details page, click the Permissions tab. Click **k8s\*\*\*\*\*\*RolePolicy-\*\*\*\*** in the **Policy** column.

6.  On the policy details page, click the Policy Document tab and click **Modify Policy Document**.

7.  In the Modify Policy Document panel, add the following policy content to the Action section and click **OK**:

    ```
    {
        "Action": [
            "cr:ListInstance",
            "cr:ListMetadataOccurrences"
        ],
        "Resource": [
            "*"
        ],
        "Effect": "Allow"
    }
    ```


## Example of enabling image signature verification

The following example demonstrates how kritis-validation-hook is used to enable image signature verification for the **default** namespace.

**Note:** This example does not include further details of image signing because this procedure does not involve kritis-validation-hook. The following signed image and KMS key are used in this example:

-   The address of the image that is signed by KMS is kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45.
-   The public key of the KMS key is stored in the publickey.txt file.
-   The ID of the KMS key is `4a2ef103-5aa3-4220-****`.

1.  Create an AttestationAuthority object to declare a trusted authority.

    1.  Use the following template to create an AttestationAuthority.yaml file:

        ```
        apiVersion: kritis.grafeas.io/v1beta1
        kind: AttestationAuthority
        metadata:
          name: demo-aa
        spec:
          noteReference: namespaces/demo-aa
          publicKeyData: $(cat publickey.txt | base64 | tr -d '\n')
          publicKeyId: 4a2ef103-5aa3-4220-****
        ```

    2.  Run the following command to configure a KMS key:

        ```
        kubectl apply -f AttestationAuthority.yaml
        ```

2.  Create a GenericAttestationPolicy object to declare a signature verification policy. Then, specify the trusted authority for signature verification.

    1.  Use the following template to create a GenericAttestationPolicy.yaml file:

        ```
        apiVersion: kritis.grafeas.io/v1beta1
        kind: GenericAttestationPolicy
        metadata:
          name: demo-gap
        spec:
          attestationAuthorityNames:
          - demo-aa
        ```

    2.  Run the following command to configure a signature verification policy:

        ```
        kubectl apply -f GenericAttestationPolicy.yaml
        ```

3.  Test image singnature verification by deploying images not singed by the trusted authority.

    -   Run the following command to test image signature verification:

        ```
        kubectl create deployment test-denied --image=alpine:3.11
        ```

        Expected output:

        ```
        Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
        ```

    -   Run the following command to test image signature verification:

        ```
        kubectl create deployment test-denied --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11
        ```

        Expected output:

        ```
        Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11 is not attested
        ```

    The output indicates that the image signature verification feature denies the deployment of images not signed by the trusted authority.

4.  Test signature verification by deploying an image singed by the trusted authority.

    Run the following command to test image signature verification:

    ```
    kubectl create deployment test-allow --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45
    ```

    Expected output:

    ```
    deployment.apps/test-allow created
    ```

    The output indicates that the image signature verification feature allows the deployment of images signed by the trusted authority.


## Configure an image signature verification whitelist

In middleware or service mesh scenarios, pods may contain sidecar containers injected by third-party components. These sidecar container images may fail to pass signature verification. kritis-validation-hook allows you to use image signature verification whitelists to enable the deployment of these sidecar containers and these pods. After you configure an image signature verification whitelist, kritis-validation-hook verifies only the signatures of images that are not included in the whitelist. The signatures of images that are included in the whitelist are not verified.

You can define the `admissionallowlists.kritis.grafeas.io` resource to configure the image verification whitelist. This resource is defined in the following CustomResourceDefinition \(CRD\):

```
apiVersion: kritis.grafeas.io/v1beta1   # This is the default value. Use the default value. 
kind: AdmissionAllowlist                # This is the default value. Use the default value. 
metadata:
  name: kritis-allowlist                # The name of the resource. This name must be unique within the cluster. 
spec:
  patterns:                             # The configuration of the whitelist. You can configure multiple whitelists. 
  - namePattern: 'registry*.*.aliyuncs.com/acs/*' # The image name that you want to add to the whitelist. For more information, see the following descriptions: 
  - namePattern: 'registry-vpc.cn-beijing.aliyuncs.com/arms-docker-repo/*'
    namespace: 'default'    # The namespace to which the specified whitelist applies. This parameter is optional. If you do not set this parameter, the specified whitelist applies to all namespaces. 
```

To add a system image of ACK to the whitelist, perform the following steps:

1.  Use the following template to create a `kritis-admission-allowlist-acs.yaml` file:

    ```
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AdmissionAllowlist
    metadata:
      name: allow-acs-images
    spec:
      patterns:
      - namePattern: 'registry*.*.aliyuncs.com/acs/*'
    ```

    The namePattern parameter supports exact matching and wildcard matching based on asterisks \(\*\).

    -   If the value of namePattern does not contain asterisks \(\*\), exact matching is used. For example, a value of `nginx:v0.1.0` matches only `nginx:v0.1.0`.
    -   If asterisks are used in wildcard matching, the following requirements must be met:
        -   If an asterisk \(\*\) appears at the end of the value, all characters except forward slashes \(/\) are matched. For example, `a.com/nginx*` matches `a.com/nginx:v0.1.0` but does not match `a.com/nginx/test:v0.1.0`.
        -   If asterisks \(\*\) do not appear at the end of the value, letters, digits, hyphens \(-\), and underscores \(\_\) are matched. For example, `registry-vpc.cn-*.aliyuncs.com/acs/pause:3.2` matches both `registry-vpc.cn-hangzhou.aliyuncs.com/acs/pause:3.2` and `registry-vpc.cn-beijing.aliyuncs.com/acs/pause:3.2`.
    The following list shows the images that are commonly included in the whitelist. You can add more items to the whitelist based on your business requirements.

    ```
    # Images used by Container Service for Kubernetes (ACK).
    - namePattern: 'registry*.*.aliyuncs.com/acs/*'
    
    # Images used by ACK in the regions within China.
    - namePattern: 'registry*.cn-*.aliyuncs.com/acs/*'
    
    # Images used by Application Real-Time Monitoring Service (ARMS).
    - namePattern: 'registry*.*.aliyuncs.com/arms-docker-repo/*'
    
    # Images used by ARMS in the regions within China.
    - namePattern: 'registry*.cn-*.aliyuncs.com/arms-docker-repo/*'
    ```

2.  Run the following command to configure the whitelist:

    ```
    kubectl apply -f kritis-admission-allowlist-acs.yaml 
    ```

    Expected output:

    ```
    admissionallowlist.kritis.grafeas.io/allow-acs-images created
    ```

3.  Run the following command to check whether the whitelist is configured:

    ```
    kubectl get admissionallowlists.kritis.grafeas.io
    ```

    Expected output:

    ```
    NAME               AGE
    allow-acs-images   2m22s
    ```


**Related topics**  


[kritis-validation-hook](/intl.en-US/Release notes/System Component change Records/Security/kritis-validation-hook.md)

[Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use kritis-validation-hook to automatically verify the signatures of container images.md)

