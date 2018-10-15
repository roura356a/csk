# How to use private images in Kubernetes clusters {#concept_yvc_fxb_wdb .concept}

```
kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc@aliyun.com --docker-password=xxxxxx --docker-email=abc@aliyun.com
```

where:

-   **regsecret**: Specifies the secret key name and the name is customizable.
-   **—docker-server**: Specifies the Docker repository address.
-   **—docker-username**: Specifies the user name of the Docker repository.
-   **—docker-password**: Specifies the logon password of the Docker repository.
-   **—docker-email**: Specifies the email address \(optional\).

Add secret key parameters in the YML file.

```
containers:
    - name: foo
      image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
imagePullSecrets:
    - name: regsecret
```

where:

-   `imagePullSecrets` declares that a secret key must be specified when you pull the image.
-   `regsecret` must be the same as the preceding secret key name.
-   The Docker repository name in`image` must be the same as that in `--docker-server`.

For more information, see the official documentation [Use private repository](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry).

## Implement keyless orchestration {#section_zqk_yhk_bfb .section}

To avoid referencing keys each time when using private images to deploy, you can add secret to the default service account of namespace. For more information, see [Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account).

First find the secret created to pull the private image.

```
# kubectl get secret regsecret
NAME        TYPE                             DATA      AGE
regsecret   kubernetes.io/dockerconfigjson   1         13m
```

In this example, manually configure the default service account default of the namespace to use this secret as the imagePullSecret.

Create a sa.yaml configuration file to export the configurations of the service account default to this file.

```
kubectl get serviceaccounts default -o yaml > ./sa.yaml

cat  sa.yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2015-08-07T22:02:39Z
  name: default
  namespace: default
  resourceVersion: "243024"             ##Pay attention to this item
  selfLink: /api/v1/namespaces/default/serviceaccounts/default
  uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
secrets:
- name: default-token-uudgeoken-uudge
```

Execute the `vim sa.yaml` command to delete resourceVersion and add the secret configuration item, imagePullSecrets which is used to pull images. The modified configuration is as follows:

```
 
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2015-08-07T22:02:39Z
  name: default
  namespace: default
  selfLink: /api/v1/namespaces/default/serviceaccounts/default
  uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
secrets:
- name: default-token-uudge
imagePullSecrets:                 ##Add this item
- name: regsecret


```

Use the sa.yaml configuration file to replace the service account configurations of default.

```
kubectl replace serviceaccount default -f ./sa.yaml
serviceaccount "default" replaced
```

Execute the kubectl create -f command to create a tomcat orchestration as an example.

```
apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: tomcat-deployment
  labels:
    app: tomcat
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tomcat
  template:
    metadata:
      labels:
        app: tomcat
    spec:
      containers:
      - name: tomcat
        image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0              #Replace this with your own private image address
        ports:
        - containerPort: 8080containerPort: 8080
```

If you have configured properly, the pod starts successfully. Execute the kubectl get pod tomcat-xxx -o yaml command. The following configuration items are displayed:

```
spec:
  imagePullSecrets:
  - nameregsecretey
```

