---
keyword: [gatekeeper, introduction, OPA]
---

# Introduction

The gatekeeper component facilitates the management and enforcement of policies executed by Open Policy Agent \(OPA\) in Kubernetes clusters. This topic describes the architecture of gatekeeper and provides an example of how to use gatekeeper.

For more information about OPA, see [Open Policy Agent](https://www.openpolicyagent.org/).

## Architecture

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6334479951/p165176.png)

## Examples

You can use gatekeeper to constrain pod deployments in specified namespaces based on labels. In this example, a Constraint is defined to declare that all pods created in a specified namespace must be labeled with gatekeeper-test-label.

1.  Run the following commands to create a test-gatekeeper namespace and attach the name=test-gatekeeper label to the namespace:

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

    It takes about 10 seconds to initialize the Constraint template.

3.  Run the following command to create a Constraint from the preceding Constraint template. This Constraint declares that all pods created in a namespace that is attached with the name=test-gatekeeper label must be labeled with gatekeeper-test-label.

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

4.  Perform the following steps to verify the enforcement of the Constraint:

    -   Run the following command to create a pod that is not labeled with gatekeeper-test-label in the test-gatekeeper namespace. The namespace is not attached with name=test-gatekeeper. Therefore, the creation fails.

        ```
        kubectl -n test-gatekeeper run test-deny --image=nginx --restart=Never
        ```

        ```
        Error from server ([denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}): admission webhook "validation.gatekeeper.sh" denied the request: [denied by pod-must-have-gatekeeper-test-label] you must provide labels: {"gatekeeper-test-label"}
        ```

    -   Run the following command to create a pod that is labeled with gatekeeper-test-label in the test-gatekeeper namespace. The namespace is attached with name=test-gatekeeper. Therefore, the creation succeeds.

        ```
        kubectl -n test-gatekeeper run test-pass -l gatekeeper-test-label=pass --image=nginx --restart=Never
        ```

        ```
        pod/test-pass created
        ```

    -   Run the following command to create a pod that is not labeled with gatekeeper-test-label in the default namespace. The namespace is not subject to the Constraint. Therefore, the creation succeeds.

        ```
        kubectl -n default run test-deny --image=nginx --restart=Never
        ```

        ```
        pod/test-deny created
        ```


