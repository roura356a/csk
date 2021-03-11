---
keyword: [kritis-validation-hook, signature verification, container images]
---

# kritis-validation-hook introduction

kritis-validation-hook is a key component that is used to verify image signatures. You can use signature verification to ensure that only images signed by trusted authorities are deployed. This reduces the risk of malicious code execution. This topic provides examples on how kritis-validation-hook is used to verify signatures.

Based on the open source project [kritis](https://github.com/grafeas/kritis), kritis-validation-hook is integrated with[Alibaba Cloud Container Registry \(ACR\)](https://www.alibabacloud.com/zh/product/container-registry)to verify the signatures of images that are signed by [Key Management Service \(KMS\)](https://www.alibabacloud.com/zh/products/kms). kritis-validation-hook is integrated with[Security Center](https://www.alibabacloud.com/zh/products/threat-detection), KMS, and ACR to implement automated image signing and signature verification. This allows you to build a secure environment for clusters. For more information about how to enable signature verification for container images, see [Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md).

## Permissions

To use kritis-validation-hook in a managed Kubernetes cluster, make sure that the Resource Access Management \(RAM\) role of worker nodes in the cluster is granted the following permissions:

```
cr:ListInstance
cr:ListMetadataOccurrences
```

If you want to grant the RAM role the preceding permissions, perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Cluster Information page, click the **Cluster Resources** tab and find the Worker RAM Role field.

5.  Click the hyperlink in the field to go to the RAM Roles page in the RAM console.

6.  On the **Permissions** tab, click the hyperlink in the **Policy** column to go to the **Policy Document** tab of the policy.

7.  Click **Modify Policy Document**. In the Modify Policy Document panel, add the following Action policy, and click **OK** to save the modified policy.

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


## Examples

The following example demonstrates how kritis-validation-hook is used to enable image signature verification for the default namespace.

**Note:** This example does not include further details of image signing because this procedure does not involve kritis-validation-hook. The following signed image and KMS key are used in this example:

-   The address of the image that is signed by KMS is kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45.
-   The public key of the KMS key are stored in the publickey.txt file.
-   The ID of the KMS key is 4a2ef103-5aa3-4220-89ee-kms-key-id.

1.  Create an AttestationAuthority object to declare a trusted authority.

    The preceding public key is used in the following code block:

    ```
    cat <<EOF > AttestationAuthority.yaml
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AttestationAuthority
    metadata:
      name: demo-aa
    spec:
      noteReference: namespaces/demo-aa
      publicKeyData: $(cat publickey.txt | base64 | tr -d '\n')
      publicKeyId: 4a2ef103-5aa3-4220-89ee-kms-key-id
    EOF
    
    kubectl apply -f AttestationAuthority.yaml
    ```

2.  Create a GenericAttestationPolicy object to declare the attestation policy. Then, specify the trusted authority for signature verification.

    ```
    cat <<EOF > GenericAttestationPolicy.yaml
    apiVersion: kritis.grafeas.io/v1beta1
    kind: GenericAttestationPolicy
    metadata:
      name: demo-gap
    spec:
      attestationAuthorityNames:
      - demo-aa
    EOF
    
    kubectl apply -f GenericAttestationPolicy.yaml
    ```

3.  Verify that images are not allowed to be deployed if they are not signed by the trusted authority.

    ```
    kubectl create deployment test-denied --image=alpine:3.11
    ```

    The following output is returned:

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
    ```

    Run the following command:

    ```
    kubectl create deployment test-denied --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11
    ```

    The following output is returned:

    ```
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11 is not attested
    ```

4.  Verify that images are allowed to be deployed if they are signed by the trusted authority.

    Run the following command:

    ```
    kubectl create deployment test-allow --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45
    ```

    The following output is returned:

    ```
    deployment.apps/test-allow created
    ```


## Configure an image verification whitelist

If the middleware or third-party component automatically injects sidecar containers, the images of the sidecar containers may fail the image signature verification. This applies to service mesh scenarios and may cause issues when you attempt to deploy pods. To fix this issue, you can use kritis-validation-hook to configure an image verification whitelist. This way, kritis-validation-hook verifies only the signatures of the images that are not included in the whitelist. The signatures of the images that are included in the whitelist are not verified.

You can define the `admissionallowlists.kritis.grafeas.io` resource to configure the image verification whitelist. This resource is defined in the following CustomResourceDefinition \(CRD\):

```
apiVersion: kritis.grafeas.io/v1beta1   # The default value. You do not need to modify the value.
kind: AdmissionAllowlist                # The default value. You do not need to modify the value.
metadata:
  name: kritis-allowlist                # The name of the resource. This name is unique within a cluster.
spec:
  patterns:                             # The whitelist. You can specify one or more images in the whitelist.
  - namePattern: 'registry*. *.aliyuncs.com/acs/*' # The image name that you want to add to the whitelist. For more information, see the following descriptions:
  - namePattern: 'registry-vpc.cn-beijing.aliyuncs.com/arms-docker-repo/*'
    namespace: 'default'    # Optional The namespace to which the specified whitelist applies. If you do not set the value, the specified whitelist applies to all namespaces.
```

To add an image of ACK to the whitelist, perform the following steps:

1.  Define the whitelist in the following CRD and save the CRD as `kritis-admission-allowlist-acs.yaml`.

    ```
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AdmissionAllowlist
    metadata:
      name: allow-acs-images
    spec:
      patterns:
      - namePattern: 'registry*. *.aliyuncs.com/acs/*'
    ```

    The namePattern parameter supports exact matching and wildcard matching. Asterisks \(\*\) are used as wildcard characters.

    -   If the value of namePattern does not contain asterisks \(\*\), exact matching is used. For example, `nginx:v0.1.0` matches only `nginx:v0.1.0`.
    -   If asterisks \(\*\) are used in wildcard matching, the following requirements must be met:
        -   The asterisk \(\*\) that appears at the end of the value matches all characters except forward slashes \(/\). For example, `a.com/nginx*` matches `a.com/nginx:v0.1.0`, but does not match `a.com/nginx/test:v0.1.0`.
        -   The asterisk \(\*\) that does not appear at the end of the value matches letters, digits, hyphen \(-\), and underscores \(\_\). For example, `registry-vpc.cn-*.aliyuncs.com/acs/pause:3.2` matches both `registry-vpc.cn-hangzhou.aliyuncs.com/acs/pause:3.2` and `registry-vpc.cn-beijing.aliyuncs.com/acs/pause:3.2`.
    The following list shows the common images that are included in the whitelist. You can add more items to the whitelist based on your business requirements.

    ```
    # The images of Container Service for Kubernetes (ACK).
    - namePattern: 'registry*. *.aliyuncs.com/acs/*'
    
    # The images of ACK in the regions within China.
    - namePattern: 'registry*.cn-*.aliyuncs.com/acs/*'
    
    # The images of Application Real-Time Monitoring Service (ARMS).
    - namePattern: 'registry*. *.aliyuncs.com/arms-docker-repo/*'
    
    # The images of ARMS in the regions inside China.
    - namePattern: 'registry*.cn-*.aliyuncs.com/arms-docker-repo/*'
    ```

2.  To apply the defined whitelist, run the following command:

    ```
    kubectl apply -f kritis-admission-allowlist-acs.yaml 
    ```

    The following output is returned:

    ```
    admissionallowlist.kritis.grafeas.io/allow-acs-images created
    ```

3.  Run the `kubectl get admissionallowlists.kritis.grafeas.io` command to check the specified whitelist.

    The following output is returned:

    ```
    NAME               AGE
    allow-acs-images   2m22s
    ```


## Next up

kritis-validation-hook will be integrated with other Alibaba Cloud services to provide more advanced features. The features are not limited to:

-   Immutable image tags. You can use this feature to specify tags instead of image digests when you verify image signatures. This improves user experience. The latest version of kritis-validation-hook can verify the signatures of images with immutable image tags. For more information, see [Configure a repository to be immutable]().
-   Image vulnerability detection. After you use this feature, images that contain vulnerabilities of specified levels are not allowed to be deployed. This ensures the security of your environment.

**Related topics**  


[Release notes](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/Release notes.md)

[Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md)

