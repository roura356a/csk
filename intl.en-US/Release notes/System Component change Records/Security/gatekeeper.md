---
keyword: [gatekeeper, introduction, OPA]
---

# gatekeeper

The gatekeeper component facilitates the management and enforcement of policies executed by Open Policy Agent \(OPA\) in Kubernetes clusters. This allows you to manage the labels of namespaces. This topic describes the features and usage notes of gatekeeper. It also lists the latest changes to gatekeeper.

## Introduction

OPA is an open source policy engine that is commonly used to implement policies in stacks in a standardized and context-aware manner. gatekeeper is used to manage and implement OPA policies, and manage labels of namespaces in ACK cluster For more information about OPA, see [Open Policy Agent](https://www.openpolicyagent.org/). The following figure shows the architecture of gatekeeper.

![Component architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2406336261/p143242.png)

## Usage notes

You can use gatekeeper to constrain pod deployments in specified namespaces based on labels. In this example, a Constraint is defined to declare that all pods created in a specified namespace must be labeled with gatekeeper-test-label. For more information about how to use gatekeeper, see [Use gatekeeper](https://open-policy-agent.github.io/gatekeeper/website/docs/howto).

1.  Run the following commands to create a test-gatekeeper namespace and add the name=test-gatekeeper label to the namespace:

    ```
    kubectl create ns test-gatekeeper
    kubectl label ns test-gatekeeper name=test-gatekeeper
    ```

2.  Run the following command to create a Constraint template that can be used to define Constraints on pod labels:

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

    It requires about 10 seconds to initialize the Constraint template.

3.  Run the following command to create a Constraint from the preceding Constraint template. This Constraint declares that all pods created in a namespace that is added with the name=test-gatekeeper label must be labeled with gatekeeper-test-label.

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

    It takes about 10 seconds to initialize the Constraint.

4.  Check whether the namespace is constrained.

    -   Run the following command to create a pod that is not labeled with `gatekeeper-test-label` in the test-gatekeeper namespace. The test-gatekeeper namespace is added with `name=test-gatekeeper`.

        ```
        kubectl -n test-gatekeeper run test-deny --image=nginx --restart=Never
        ```

        Expected output:

        ```
        Error from server ([denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}): admission webhook "validation.gatekeeper.sh" denied the request: [denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}
        ```

        The test-gatekeeper namespace is added with `name=test-gatekeeper`. The pod is to be created without the `gatekeeper-test-label` label. Therefore, the creation fails.

    -   Run the following command to create a pod that is labeled with `gatekeeper-test-label` in the test-gatekeeper namespace. The test-gatekeeper namespace is added with `name=test-gatekeeper`.

        ```
        kubectl -n test-gatekeeper run test-pass -l gatekeeper-test-label=pass --image=nginx --restart=Never
        ```

        Expected output:

        ```
        pod/test-pass created
        ```

        The test-gatekeeper namespace is added with `name=test-gatekeeper`. The pod is to be created with the `gatekeeper-test-label` label. Therefore, the creation succeeds.

    -   Run the following command to create a pod that is not labeled with `name=test-gatekeeper` in a namespace that is not subject to the Constraint:

        ```
        kubectl -n default run test-deny --image=nginx --restart=Never
        ```

        Expected output:

        ```
        pod/test-deny created
        ```

        The namespace is not subject to the Constraint. Therefore, the creation succeeds. The created pod is added with `name=test-gatekeeper`

    The preceding steps show that gatekeeper can be used to constrain pod creations in a specific namespace. In this example, the pod to be created in the namespace must be added with the gatekeeper-test-label label.


## Release notes

**March 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v3.3.0.24-8e68abc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/gatekeeper:v3.3.0.24-8e68abc-aliyun|2021-03-16|-   gatekeeper can be installed in registered Kubernetes clusters.
-   OPA Gatekeeper is upgraded to V3.3.0. The gatekeeper component is dependent on OPA Gatekeeper. |

**August 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v3.1.0.11-24bab09-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/gatekeeper:v3.1.0.11-24bab09-aliyun|2020-08-20|OPA Gatekeeper is upgraded to V3.1.0-beta.12. The gatekeeper component is dependent on OPA Gatekeeper.|

