---
keyword: [kritis-validation-hook, Signature verification, Container images]
---

# kritis-validation-hook introduction

kritis-validation-hook is a key component for verifying image signatures. You can use signature verification to ensure that only images signed by trusted authorities are deployed. This reduces the risks of malicious code execution. This topic provides examples about how kritis-validation-hook is used to verify signatures.

Based on the open-source project [kritis](https://github.com/grafeas/kritis), kritis-validation-hook is integrated with [Alibaba Cloud Container Registry \(ACR\)](https://www.alibabacloud.com/zh/product/container-registry) to support signature verification for images that are signed by [Key Management Service \(KMS\)](https://www.alibabacloud.com/zh/products/kms). kritis-validation-hook is integrated with [Security Center](https://www.alibabacloud.com/zh/products/threat-detection), KMS, and ACR to implement fully automated image signing and signature verification. This allows you to build a secure environment for clusters. For more information about how to enable signature verification for container images, see [Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md).

## Example

The following example demonstrates how kritis-validation-hook is used to enable image signature verification for the default namespace.

**Note:** In this example, the procedure of image signing is not described because this procedure does not involve kritis-validation-hook. The following signed image and KMS key is used in this example:

-   The address of the image signed by KMS is kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45.
-   The public key of the KMS key is stored in the publickey.txt file
-   The ID of the KMS key is 4a2ef103-5aa3-4220-89ee-kms-key-id.

1.  Create an AttestationAuthority object to declare a trusted authority.

    The preceding public key is used in the following code block:

    ```
    $ cat <<EOF > AttestationAuthority.yaml
    apiVersion: kritis.grafeas.io/v1beta1
    kind: AttestationAuthority
    metadata:
      name: demo-aa
    spec:
      noteReference: namespaces/demo-aa
      publicKeyData: $(cat publickey.txt | base64 | tr -d '\n')
      publicKeyId: 4a2ef103-5aa3-4220-89ee-kms-key-id
    EOF
    
    $ kubectl apply -f AttestationAuthority.yaml
    ```

2.  Create a GenericAttestationPolicy object to declare the attestation policy and specify the trusted authority for signature verification.

    ```
    $ cat <<EOF > GenericAttestationPolicy.yaml
    apiVersion: kritis.grafeas.io/v1beta1
    kind: GenericAttestationPolicy
    metadata:
      name: demo-gap
    spec:
      attestationAuthorityNames:
      - demo-aa
    EOF
    
    $ kubectl apply -f GenericAttestationPolicy.yaml
    ```

3.  Verify that images are not allowed to be deployed if they are not signed by the trusted authority.

    ```
    $ kubectl create deployment test-denied --image=alpine:3.11
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image alpine:3.11 is not attested
    
    $ kubectl create deployment test-denied --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11
    Error from server: admission webhook "kritis-validation-hook-deployments.grafeas.io" denied the request: image kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine:3.11 is not attested
    ```

4.  Verify that images are allowed to be deployed if they are signed by the trusted authority.

    ```
    $ kubectl create deployment test-allow --image=kritis-demo-registry.cn-hangzhou.cr.aliyuncs.com/kritis-demo/alpine@sha256:ddba4d27a7ffc3f86dd6c2f92041af252a1f23a8e742c90e6e1297bfa1bc0c45
    deployment.apps/test-allow created
    ```


## Next up

kritis-validation-hook will be integrated with other Alibaba Cloud services to provide more advanced features, including but not limited to:

-   Immutable image tags. With immutable image tags, you can specify tags instead of image digests when you verify image signatures. This improves user experience
-   Image vulnerability detection. With image vulnerability detection, you can deny requests for deploying images that contain vulnerabilities of specified levels. This reinforces the security of your environment.

**Related topics**  


[Release notes](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/Release notes.md)

[Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md)

