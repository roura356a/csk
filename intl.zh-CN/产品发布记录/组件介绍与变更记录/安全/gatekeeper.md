---
keyword: gatekeeper
---

# gatekeeper

gatekeeper组件可以帮助您方便地管理和应用集群内的Open Policy Agent（OPA）策略，实现命名空间标签管理等功能。本文为您介绍gatekeeper组件的功能、使用说明和变更记录。

## 组件介绍

Open Policy Agent（OPA）是一个开源的、通用的策略引擎，可以在整个堆栈中实现统一的、上下文感知的策略实施。gatekeeper组件可以管理和应用集群内的Open Policy Agent（OPA）策略，实现命名空间标签管理等功能。有关OPA的介绍，请参见[Open Policy Agent](https://www.openpolicyagent.org/)。gatekeeper组件架构如下图所示。

![组件架构](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0590488951/p143242.png)

## 使用说明

本示例将演示如何通过gatekeeper实现限制指定命名空间下创建的Pod必须包含一个名为gatekeeper-test-label的标签，借此展示getekeeper的基本用法。更多gatekeeper的用法，请参见[使用gatekeeper](https://open-policy-agent.github.io/gatekeeper/website/docs/howto)。

1.  执行以下命令，创建一个测试用的命名空间test-gatekeeper，同时给命名空间增加一个name=test-gatekeeper的标签。

    ```
    kubectl create ns test-gatekeeper
    kubectl label ns test-gatekeeper name=test-gatekeeper
    ```

2.  执行以下命令，创建一个检查标签的策略模板。

    ```
    kubectl apply -f - <<EOF
    apiVersion: templates.gatekeeper.sh/v1beta1
    kind: ConstraintTemplate
    metadata:
      name: k8srequiredlabels
    spec:
      crd:
        spec:
          names:
            kind: K8sRequiredLabels
          validation:
            openAPIV3Schema:
              properties:
                labels:
                  type: array
                  items:
                    type: string
      targets:
        - target: admission.k8s.gatekeeper.sh
          rego: |
            package k8srequiredlabels
            violation[{"msg": msg, "details": {"missing_labels": missing}}] {
              provided := {label | input.review.object.metadata.labels[label]}
              required := {label | label := input.parameters.labels[_]}
              missing := required - provided
              count(missing) > 0
              msg := sprintf("you must provide labels: %v", [missing])
            }
    EOF
    ```

    等待10秒左右，待gatekeeper完成策略模板初始化。

3.  执行以下命令，创建一个策略模板的约束。这个约束将限制包含标签name=test-gatekeeper的命名空间下创建的Pod必须包含名为gatekeeper-test-label的标签。

    ```
    kubectl apply -f - <<EOF
    apiVersion: constraints.gatekeeper.sh/v1beta1
    kind: K8sRequiredLabels
    metadata:
      name: pod-must-have-gatekeeper-test-label
    spec:
      match:
        kinds:
          - apiGroups: [""]
            kinds: ["Pod"]
        namespaceSelector:
          matchExpressions:
          - key: name
            operator: In
            values: ["test-gatekeeper"]
      parameters:
        labels: ["gatekeeper-test-label"]
    
    EOF
    ```

    等待10秒左右，待gatekeeper完成约束的初始化。

4.  验证命名空间的约束效果。

    -   执行以下命令，在包含标签`name=test-gatekeeper`的命名空间test-gatekeeper下创建一个不包含`gatekeeper-test-label`标签的Pod。

        ```
        kubectl -n test-gatekeeper run test-deny --image=nginx --restart=Never
        ```

        预期输出：

        ```
        Error from server ([denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}): admission webhook "validation.gatekeeper.sh" denied the request: [denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}
        ```

        可以看到，在包含标签`name=test-gatekeeper`的命名空间test-gatekeeper下创建一个不包含`gatekeeper-test-label`标签的Pod失败。

    -   执行以下命令，在包含标签`name=test-gatekeeper`的命名空间test-gatekeeper下创建一个包含`gatekeeper-test-label`标签的Pod。

        ```
        kubectl -n test-gatekeeper run test-pass -l gatekeeper-test-label=pass --image=nginx --restart=Never
        ```

        预期输出：

        ```
        pod/test-pass created
        ```

        可以看到，在包含标签`name=test-gatekeeper`的命名空间test-gatekeeper下创建一个包含`gatekeeper-test-label`标签的Pod成功。

    -   执行以下命令，在其他未配置约束的命名空间下创建一个不包含`name=test-gatekeeper`标签的Pod。

        ```
        kubectl -n default run test-deny --image=nginx --restart=Never
        ```

        预期输出：

        ```
        pod/test-deny created
        ```

        可以看到，在其他未配置约束的命名空间下创建一个不包含`name=test-gatekeeper`标签的Pod成功。

    根据以上验证，说明通过gatekeeper限制指定命名空间下创建的Pod必须包含一个名为gatekeeper-test-label的标签成功。


## 变更记录

**2021年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v3.6.0.62-g156146d-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/gatekeeper:v3.6.0.62-g156146d-aliyun|2021年09月20日|-   从v3.6.0.62版本开始，gatekeeper组件将只支持v1.16.9及以上版本的Kubernetes集群。
-   升级组件依赖的OPA gatekeeper软件版本至v3.6.0。关于v3.6.0的OPA gatekeeper更多信息，请参见[Releases v3.6.0](https://github.com/open-policy-agent/gatekeeper/releases/tag/v3.6.0)。

|组件升级异常可能会导致集群资源变更失败，建议在业务低谷期进行升级操作。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v3.3.0.24-8e68abc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/gatekeeper:v3.3.0.24-8e68abc-aliyun|2021年03月16日|-   支持注册集群安装gatekeeper组件。
-   升级组件依赖的OPA gatekeeper软件版本至v3.3.0。

|组件升级异常可能会导致集群资源变更失败，建议在业务低谷期进行升级操作。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v3.1.0.11-24bab09-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/gatekeeper:v3.1.0.11-24bab09-aliyun|2020年08月20日|升级依赖的OPA gatekeeper软件版本至v3.1.0-beta.12。**说明：** opa gatekeeper是gatekeeper组件使用的一个开源软件。

|组件升级异常可能会导致集群资源变更失败，建议在业务低谷期进行升级操作。|

