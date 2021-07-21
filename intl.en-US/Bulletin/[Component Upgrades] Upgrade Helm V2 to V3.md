# \[Component Upgrades\] Upgrade Helm V2 to V3

Container Service for Kubernetes \(ACK\) has upgraded Helm to V3 for newly created clusters. The Tiller server component for Helm V2 has known security issues among community users. Attackers can use Tiller to install unauthorized applications in the cluster. We recommend that you upgrade to Helm V3 at your earliest opportunity.

## Impact scope

Run the kubectl get deploy -n kube-system tiller-deploy command to check whether a tiller Deployment exists. If a tiller Deployment exists, check the following conditions:

-   Whether the kubeconfig file of your ACK cluster is provided to external users.
-   Whether external access to the ACK console is allowed.
-   Whether your ACK cluster is used in a multi-tenancy scenario and whether privilege isolation is enabled among users.

If one of the preceding conditions is met, we recommend that you upgrade to Helm V3.

## Unaffected scenarios or scenarios where upgrade to Helm V3 is unavailable

If the scenario does not meet the preceding conditions or you cannot upgrade to Helm V3, we recommend that you manually upgrade Tiller of Helm V2 to the latest version for higher security. Perform the following steps to upgrade Tiller of Helm V2 to the latest version:

1.  Run the following command:

    ```
    helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.16.3 --upgrade
    ```

2.  After Tiller passes the health check, you can run the helm version command to query the upgrade result.

    The preceding command upgrades only the server component of Helm. To download the client components for different operating systems, click the [download link](https://github.com/helm/helm/releases/tag/v2.16.3).


**Note:** If the scenario does not meet the preceding conditions or you cannot upgrade to Helm V3, you can upgrade Tiller to the latest version and skip the following steps. You can upgrade to Helm V3 later.

## Precheck

Before you upgrade Helm V2, perform the following steps for a precheck.

1.  Check whether Tiller is installed in your ACK cluster. Run the kubectl get deploy -n kube-system tiller-deploy command to check whether a tiller Deployment exists.

2.  If a tiller Deployment exists, run the helm ls -a command to check whether applications are installed.

3.  If applications are installed, you must first delete these applications due to the data incompatibility between Helm V2 and V3.

    **Note:** The Helm community provides a plug-in to migrate Helm V2 configurations and releases to Helm V3. To prevent data loss, proceed with caution when you use the plug-in. For more information about the plug-in, see [helm-2to3](https://github.com/helm/helm-2to3).


## Upgrade procedure

1.  Make sure that you have passed the [Precheck](#section_wxa_b2o_q4p).

2.  Run the kubectl delete deploy tiller-deploy -n kube-system command.

3.  Download the [Helm V3 client component](http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/helm/helm-v3.0.3-linux-amd64.tar.gz) to install new applications.

    **Note:**

    Before you install new applications, take note of the following items:

    -   You must use Helm V3 to reinstall the applications that were installed by using Helm V2. Evaluate the impacts on your workloads.
    -   When you reinstall an application, the original data will be lost. Back up your data in advance.

**Related topics**  


[Helm v3 Change log](https://github.com/helm/helm/releases/tag/v3.0.0)

[Differences between Helm V3 and Helm V2](https://v3.helm.sh/docs/faq/#changes-since-helm-2)

[Migrate Helm V2 to V3](https://helm.sh/docs/topics/v2_v3_migration/)

