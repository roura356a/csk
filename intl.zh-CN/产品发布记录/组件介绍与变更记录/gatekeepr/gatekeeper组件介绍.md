---
keyword: [gatekeeper组件, 介绍, OPA]
---

# gatekeeper组件介绍

gatekeeper组件可以帮助您方便地管理和应用集群内的Open Policy Agent（OPA）策略。本文介绍gatekeeper组件的架构以及通过一个示例来演示具体的使用方法和工作效果。

有关OPA的介绍，请参见[Open Policy Agent](https://www.openpolicyagent.org/)。

## 组件架构

![组件架构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0590488951/p143242.png)

## 示例

本示例将演示如何通过gatekeeper实现限制指定命名空间下创建的Pod必须包含一个名为gatekeeper-test-label的标签，借此展示getekeeper的基本用法。

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

4.  按照以下内容测试约束的实际效果。

    -   在包含标签name=test-gatekeeper的命名空间test-gatekeeper下创建一个不包含gatekeeper-test-label标签的Pod，创建将会失败。

        ```
        kubectl -n test-gatekeeper run test-deny --image=nginx --restart=Never
        ```

        ```
        Error from server ([denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}): admission webhook "validation.gatekeeper.sh" denied the request: [denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}
        ```

    -   在包含标签name=test-gatekeeper 的命名空间test-gatekeeper下创建一个包含gatekeeper-test-label标签的Pod，创建将会成功。

        ```
        kubectl -n test-gatekeeper run test-pass -l gatekeeper-test-label=pass --image=nginx --restart=Never
        ```

        ```
        pod/test-pass created
        ```

    -   在其他未配置约束的命名空间下创建一个不包含gatekeeper-test-label标签的Pod，创建将会成功。

        ```
        kubectl -n default run test-deny --image=nginx --restart=Never
        ```

        ```
        pod/test-deny created
        ```


