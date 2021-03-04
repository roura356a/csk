---
keyword: [service account token volume projection, ServiceAccount, enhanced protection]
---

# Enable service account token volume projection

When you create a cluster of Container Service for Kubernetes \(ACK\), you can enable **service account token volume projection** to enhance security when you use service accounts. This feature enables kubelet to request and store the token on behalf of the pod, and allows you to configure token properties such as the audience and validity period. As the token approaches expiration, kubelet proactively rotates the token if it is older than 80 percent of its time to live \(TTL\) or if it is older than 24 hours. This topic describes how to configure and use service account token volume projection for a cluster of Container Service for Kubernetes \(ACK\).

Service accounts are used to provide an identity for pods when they communicate with the API server. Traditionally, you may encounter the following security issues when you use service accounts:

-   The JSON Web Tokens \(JWTs\) used by service accounts are not audience bound. A user of a service account can masquerade as another user and launch masquerade attacks.
-   The service account token is stored in a Secret and delivered as a file to the corresponding node. A service account may be granted advanced permissions when a powerful system component is running. This results in a broad attack surface for the Kubernetes control plane. Attackers can obtain the service account used by this system component to launch privilege escalation attacks.
-   JWTs are not time bound. A JWT that is compromised in the aforementioned attacks stays valid for as long as the service account exists. You can mitigate the issue only through service account signing key rotation, which is not supported by client-go and not automated by the control plane. Therefore, a complex operations process is required.
-   A Kubernetes Secret must be created for each service account. This may put strains on elasticity and capacity in large-scale workload deployments.

ACK supports the BoundServiceAccountTokenVolume feature to enhance the security of service accounts. This feature enables pods to use a projected volume to mount the service account to a container, thus avoiding the dependency on Secrets.

## Step 1: Enable service account token volume projection

When you create an ACK cluster, select **Enable** to enable **service account token volume projection**. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

![Enable service account token volume projection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6255359951/p96425.png)

After service account token volume projection is enabled, the apiserver and controller-manager system components automatically enable the BoundServiceAccountTokenVolume feature gate and the following parameters are added to apiserver startup parameters.

|Parameter|Description|Default value|Configuration in the ACK console|
|---------|-----------|-------------|--------------------------------|
|service-account-issuer|The issuer of the service account token, which corresponds to the iss field in the token payload.|kubernetes.default.svc|Supported.|
|api-audiences|The identifiers of the API, which are used to validate the tokens at the API server side.|kubernetes.default.svc|Supported. You can set one or more audiences. Separate multiple audiences with commas \(`,`\).|
|service-account-signing-key-file|The file path of the private key that signs the token.|/etc/kubernetes/pki/sa.key|Not supported. Default value: /etc/kubernetes/pki/sa.key.|

## Step 2: Use service account token volume projection

To configure a token with an audience of vault and a validity period of two hours for a pod, you can run the following command and use the following PodSpec template.

```
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-robot
EOF
```

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /var/run/secrets/tokens
      name: vault-token
  serviceAccountName: build-robot
  volumes:
  - name: vault-token
    projected:
      sources:
      - serviceAccountToken:
          path: vault-token
          expirationSeconds: 7200
          audience: vault
```

**Note:**

-   Make sure that the pod periodically reloads the token when it rotates. This ensures that the pod can obtain the latest token in real time. We recommend that you reload the token once every five minutes. client-go 10.0.0 and later support automatic reloading to obtain the latest token.
-   The permissions of the token file that corresponds to a service account are no longer 644. When BoundServiceAccountTokenVolume is used, the permissions are 600. When fsGroup is used, the permissions are 640.

**Related topics**  


[Service Account Token Volume Projection](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)

[Configure Service Accounts for Pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)

