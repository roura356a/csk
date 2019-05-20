# Upgrade Istio components {#concept_k54_vxj_ggb .concept}

This topic describes how to upgrade Istio components.

## Background information {#section_sl2_cyj_ggb .section}

-   The Istio upgrade may install new binaries, and change configurations and API schemas.
-   The upgrade process may cause service downtime.
-   To minimize downtime, use multiple replicas to ensure that your Istio control plane components and your applications remain highly available.

**Note:** In the following example, assume that the Istio components are installed and upgraded in the istio-system namespace.

## Procedure {#section_dxz_1zj_ggb .section}

To complete the upgrade process, you need to upgrade CRD files, the control plane, and the data plane sidecar.

****Upgrade CRD files****

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Releases**. Then, click the **Helm** tab, find the istio-init release, and delete it.

    **Note:** If no release named istio-init is displayed on the Helm tab page, you can directly perform the next step.

4.  In the left-side navigation pane under Container Service-Kubernetes, choose **Store** \> **App Catalog**. Then, click **ack-istio-init**.
5.  In the Deploy area, select the target cluster. Then, click **DEPLOY**.

    **Note:** By default, the istio-system namespace is selected, and the release name is set to istio-init.


****Upgrade the control plane****

The Istio control plane components include the Citadel, Pilot, Policy, Telemetry, and Sidecar injector.

1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Releases**.
2.  Select the target cluster, select the target **Release Name**, and click **Upgrade** in the **Action** column.
3.  In the configurations of the deployed Istio, specify the version number of the Istio to be deployed.

    **Note:** On this page, you can also modify other parameters.

    ``` {#codeblock_34y_f1a_ytl}
    global:
          tag: <enter the version number>
    ```

4.  Click **Update**.

****Upgrade the data plane sidecar****

Note that after you upgrade the control plane, the applications that have already run Istio will still use the sidecar of an earlier version. To upgrade the sidecar, you need to re-inject it.

****Automatic sidecar injection****

If you use automatic sidecar injection, you can upgrade the sidecar by performing a rolling update for all pods. Then, the sidecar of the new version will be automatically re-injected.

You can use the following script to trigger the rolling update by patching the termination grace period.

```
NAMESPACE=$1
     DEPLOYMENT_LIST=$(kubectl -n $NAMESPACE get deployment -o jsonpath='{.items[*].metadata.name}')
     echo "Refreshing pods in all Deployments: $DEPLOYMENT_LIST"
     for deployment_name in $DEPLOYMENT_LIST ; do
     #echo "get TERMINATION_GRACE_PERIOD_SECONDS from deployment: $deployment_name"
     TERMINATION_GRACE_PERIOD_SECONDS=$(kubectl -n $NAMESPACE get deployment "$deployment_name" -o jsonpath='{.spec.template.spec.terminationGracePeriodSeconds}')
     if [ "$TERMINATION_GRACE_PERIOD_SECONDS" -eq 30 ]; then
     TERMINATION_GRACE_PERIOD_SECONDS='31'
     else
     TERMINATION_GRACE_PERIOD_SECONDS='30'
     fi
     patch_string="{\"spec\":{\"template\":{\"spec\":{\"terminationGracePeriodSeconds\":$TERMINATION_GRACE_PERIOD_SECONDS}}}}"
     #echo $patch_string
     kubectl -n $NAMESPACE patch deployment $deployment_name -p $patch_string
     done
     echo "done."
```

**Manual sidecar injection**

Run the following command to manually upgrade the sidecar:

```
kubectl apply -f <(istioctl kube-inject -f $ORIGINAL_DEPLOYMENT_YAML)
```

If the sidecar was previously injected with some customized injection configuration files, run the following command to manually upgrade the sidecar:

```
kubectl apply -f <(istioctl kube-inject --injectConfigFile inject-config.yaml --filename $ORIGINAL_DEPLOYMENT_YAML)
```

## Impacts caused by the Istio upgrade {#section_b3x_wbk_ggb .section}

**Impacts caused by the CRD file upgrade**

The upgrade process does not impact the calls between services within the cluster or the calls from the gateway to services.

Calls between services within the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/155833217035273_en-US.png)

**Impacts caused by the control plane upgrade**

If HA is enabled, that is, the replicas of Pilot is 2, the HPA setting of `istio-pilot/istio-policy/istio-telemetry` is `minReplicas: 2`.

If you have changed the Istio version multiple times by upgrading or rolling back the component version, testing results will indicate that the QPS of calls between services remains unchanged and the calls proceed normally.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/155833217035274_en-US.png)

**Impacts caused by the control plane sidecar upgrade**

No obvious change occurs to both the QPS of the calls between services within the cluster and the QPS of the calls from the gateway to services. But these calls will terminate temporarily. We recommend that you use multiple replicas to upgrade the sidecar to reduce the impacts.

