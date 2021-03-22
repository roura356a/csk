---
keyword: [安装云原生AI套件, 配置访问Dashboard, 配置RAM应用]
---

# 安装云原生AI套件及访问AI Dashbaord

AI Dashboard提供三种访问方式（Sshuttle，SSL VPN及Ingress）。云原生AI套件只支持安装在ACK Pro版集群中。本文介绍如何在ACK Pro集群中安装云原生AI套件，以及如何配置和访问AI Dashbaord。

-   已创建ACK Pro版集群，且在组件配置页面，需要选中**监控插件**和**日志服务**。具体操作，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   ACK Pro托管版集群的Kubernetes版本不低于1.18。
-   配置Sshuttle访问方式需要满足以下条件：
    -   集群有一台启动SSH，且公网可访问的跳板机。具体操作，请参见[如何为已有集群开启SNAT](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/如何为已有集群开启SNAT？.md)。
    -   跳板机的Python版本不低于3.6。
    -   已获取跳板机的用户名和密码。
    -   Sshuttle是最低成本VPN方案，是基于SSH搭建的。建议在集群安全组中开启IP白名单的安全规则，限制可以访问的IP范围。具体操作，请参见[只允许实例访问外部特定IP地址](https://help.aliyun.com/document_detail/25475.html?spm=5176.2020520101securityGroupDetail.0.dexternal.2d004df5d6w9mV#specifyIpAccess)。

## 部署云原生AI套件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **AI工程加速**。

5.  在**云原生AI套件**页面，单击**一键部署**。

6.  在一键部署云原生AI套件页面，根据需要选中相应的组件后，单击页面下方的**部署云原生AI套件**，开始检查环境和依赖项，检查通过后，自动部署选择的组件。

    **说明：**

    -   **交互方式**下的**Arena命名行（必选）**为默认必选组件。
    -   选中**AI-Dashboard控制台**时，会弹出**提示**话框。具体操作，请参见步骤[1](#step_twc_y20_m4t)。
    组件安装成功后，在组件列表页面：

    -   您能看到当前集群中已经安装的组件名称、版本等信息，并能对组件进行**部署**、**卸载**操作。
    -   如果已安装的组件有新版本的话，还可以对组件进行**升级**操作。
    -   安装了AI-Dashboard组件后，您可在页面左上方看到AI-Dashboard访问地址，通过该地址可以访问AI-Dashboard控制台页面。

## 安装配置AI-Dashboard控制台

1.  在云原生AI套件一键部署页面选中**AI-Dashboard控制台**时，会弹出下图所示的**提示**对话框。

    ![K-AI-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4064306161/p237448.png)

2.  单击**提示**对话框中的授权策略链接。

    1.  在**RAM角色管理**页面中，选择**权限管理**页签，单击目标授权策略名称。

    2.  **策略内容**页签中，单击**修改策略内容**，在**修改策略内容**面板的**策略内容**区域，将以下策略代码添加至`Action`字段中。

        ```
        "ecs:DescribeInstances",
        "ecs:DescribeSpotPriceHistory",
        "ecs:DescribePrice",
        "eci:DescribeContainerGroups",
        "eci:DescribeContainerGroupPrice",
        "log:GetLogStoreLogs",
        "ims:CreateApplication",
        "ims:GetApplication",
        "ims:ListApplications",
        "ims:DeleteApplication",
        "ims:CreateAppSecret",
        "ims:GetAppSecret",
        "ims:ListAppSecretIds"
        ```

    3.  添加完成后，单击**确定**。

3.  在一键部署**云原生AI套件**页面选中**监控组件**。

    ![K-AI-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1931772161/p237487.png)

4.  单击**部署云原生AI套件**。


## 访问AI-Dashboard

您可以通过四种方式访问AI-Dashboard，这四种访问方式优缺点对比如下所示：

|访问方式|优点|缺点|
|----|--|--|
|Sshuttle|配置简单|访问稳定性一般|
|SSL VPN|稳定|对集群网段有要求，配置略复杂|
|私网Ingress|配置简单|需要您打通局域网和集群VPC网络|
|公网Ingress|配置简单|暴露集群，存在安全风险|

**方式一：使用Sshuttle访问AI-Dashboard**

1.  执行以下命令，在OS X操作系统中安装Sshuttle。

    指定Sshuttle的安装版本为1.0.5，以Mac系统为例安装Sshuttle。

    ```
    brew install sshuttle@1.0.5
    ```

    在其他操作系统中安装Sshuttle的方法，请参见[Sshuttle官方文档](https://github.com/sshuttle/sshuttle)。

2.  执行以下命令，启动Sshuttle代理功能，且指定要访问的SSH跳板机，以及待访问集群节点的IP地址。

    -   集群节点IP地址：192.168.100.70。
    -   跳板机的公网IP访问地址：39.96.XX.XX。
    ```
    sudo sshuttle -r jack@39.96.XX.XX 192.168.100.0/24 -vv
    ```

3.  在地址栏中输入AI-Dashboard访问地址，例如：`http://192.168.100.70:31101/`。


**方式二：使用SSL VPN访问AI-Dashboard**

-   使用VPN网关的SSL-VPN功能从客户端远程访问AI-Dashboard。具体操作，请参见[在经典网络中使用SSL VPN](/cn.zh-CN/最佳实践/在经典网络中使用SSL VPN/Mac客户端.md)。
-   -   如果您使用Linux操作系统访问AI-Dashboard，具体操作，请参见[步骤四：配置客户端](/cn.zh-CN/最佳实践/在经典网络中使用SSL VPN/Linux客户端.md)。
-   如果您使用OS X操作系统访问AI-Dashboard，具体操作，请参见[步骤四：客户端配置](/cn.zh-CN/最佳实践/在经典网络中使用SSL VPN/Mac客户端.mdsection_qdz_j4g_xdb)。
-   如果您使用Windows操作系统访问AI-Dashboard，具体操作，请参见[步骤四：客户端配置](/cn.zh-CN/最佳实践/在经典网络中使用SSL VPN/Windows客户端.md)。

**方式三：使用私网Ingress访问AI-Dashboard**

为了控制AI-Dashboard的访问范围，可以利用阿里云增强版Ingress插件，配置只能由VPC内部访问的Ingress。更多关于Ingress的内容，请参考[Ingress概述](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress概述.md)。

1.  配置私网（集群VPC网络）Ingress的AI-Dashboard访问地址。

    **说明：** 创建ACK Pro版集群时，控制台默认会在**kube-system**命名空间下安装一个公网类型的**ack-ingress-nginx**组件。为了开启私网Ingress，您需要单独部署私网访问的Ingress Controller。

    1.  在**kube-ai**命名空间下，部署**ack-ingress-nginx**组件，且配置`privateService.enable=true`和`publicService:enabled=false`。具体操作，请参见[部署多个Ingress Controller](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/部署多个Ingress Controller.md)。

        相关参数配置如下所示：

        -   `privateService.enable=true`
        -   `publicService:enabled=false`
        -   controller.image.tag：设置该Ingress Controller可监听的目标Ingress Class标识。

            **说明：** 同一个集群中不同Ingress Controller监听的Ingress Class标识必须唯一，且不能设置为nginx关键字（nginx是集群默认Ingress Controller的监听标识）。

    2.  使用以下YAML文件样例创建AI-Dashboard的私网Ingress。

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: admin-ui-ingress
          namespace: kube-ai
          annotations:
            #注意这里要设置为您前面配置的Ingress Class。
            kubernetes.io/ingress.class: "intranet-nginx"
        spec:
          rules:
          - host: foo.bar.com
            http:
              paths:
              - path: /
                backend:
                  serviceName: ack-ai-dashboard-admin-ui
                  servicePort: 80
        ```

        **说明：** `kubernetes.io/ingress.class: "intranet-nginx"`需与步骤a中设置的Ingress Class一致。

    3.  执行以下命令获取私网Ingress的AI-Dashboard访问地址。

        ```
        kubectl get ingress admin-ui-ingress -n kube-ai
        ```

        系统输出类似以下结果：

        ```
        NAME               CLASS    HOSTS         ADDRESS       PORTS   AGE
        admin-ui-ingress   <none>   foo.bar.com   172.16.XX.XX   80      18m
        ```

    4.  执行以下命令访问私网Ingress的AI-Dashboard访问地址：

        ```
        curl -v -H "Host: foo.bar.com" "http://172.16.0.46"
        ```

        系统输出类似如下结果：

        ```
        <!DOCTYPE html><html><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1"><meta name=viewport content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no"><link rel=icon href=/favicon.ico><title>AI Dashboard</title><link href=/static/css/app.fdd5f060.css rel=preload as=style><link href=/static/css/chunk-elementUI.68c70ad5.css rel=preload as=style><link href=/static/css/chunk-libs.cd8e3f29.css rel=preload as=style><link href=/static/js/app.51cc1e83.js rel=preload as=script><link href=/static/js/chunk-elementUI.3ce6472c.js rel=preload as=script><link href=/static/js/chunk-libs.0ba38446.js rel=preload as=script><link href=/static/css/chunk-elementUI.68c70ad5.css rel=stylesheet><link href=/static/css/chunk-libs.cd8e3f29.css rel=stylesheet><link href=/static/css/app.fdd5f060.css rel=stylesheet></head><body><noscript><strong>We're sorry but AI Dashboard doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script>(function(e){function n(n){for(var r,c,o=n[0],f=n[1],i=n[2],l=0,h=[];l<o.length;l++)c=o[l],Object.prototype.hasOwnProperty.call(a,c)&&a[c]&&h.push(a[c][0]),a[c]=0;for(r in f)Object.prototype.hasOwnProperty.call(f,r)&&(e[r]=f[r]);s&&s(n);while(h.length)h.shift()();return u.push.apply(u,i||[]),t()}function t(){for(var e,n=0;n<u.length;n++){for(var t=u[n],r=!0,c=1;c<t.length;c++){var o=t[c];0!==a[o]&&(r=!1)}r&&(u.splice(n--,1),e=f(f.s=t[0]))}return e}var r={},c={runtime:0},a={runtime:0},u=[];function o(e){return f.p+"static/js/"+({}[e]||e)+"."+{"chunk-17bb17be":"c9b35848","chunk-238c903c":"d185a3d3","chunk-4f275acc":"6af93bd3","chunk-25bb57a7":"9b26638f","chunk-5db32ce3":"0996363f","chunk-05347400":"3c60ab36","chunk-763a2e84":"c4af4017","chunk-1c7a5e41":"b9658c79","chunk-8644997e":"4ba2b652","chunk-a9f4b31a":"e2cabf7c","chunk-fa5e36be":"7ef41eba"}[e]+".js"}function f(n){if(r[n])return r[n].exports;var t=r[n]={i:n,l:!1,exports:{}};return e[n].call(t.exports,t,t.exports,f),t.l=!0,t.exports}f.e=function(e){var n=[],t={"chunk-17bb17be":1,"chunk-238c903c":1,"chunk-25bb57a7":1,"chunk-05347400":1,"chunk-1c7a5e41":1,"chunk-8644997e":1,"chunk-a9f4b31a":1,"chunk-fa5e36be":1};c[e]?n.push(c[e]):0!==c[e]&&t[e]&&n.push(c[e]=new Promise((function(n,t){for(var r="static/css/"+({}[e]||e)+"."+{"chunk-17bb17be":"77391137","chunk-238c903c":"3c7f5ad9","chunk-4f275acc":"31d6cfe0","chunk-25bb57a7":"e8fd7aee","chunk-5db32ce3":"31d6cfe0","chunk-05347400":"8075f389","chunk-763a2e84":"31d6cfe0","chunk-1c7a5e41":"0da2af62","chunk-8644997e":"977a2651","chunk-a9f4b31a":"2e58b0de","chunk-fa5e36be":"b17e519a"}[e]+".css",a=f.p+r,u=document.getElementsByTagName("link"),o=0;o<u.length;o++){var i=u[o],l=i.getAttribute("data-href")||i.getAttribute("href");if("stylesheet"===i.rel&&(l===r||l===a))return n()}var h=document.getElementsByTagName("style");for(o=0;o<h.length;o++){i=h[o],l=i.getAttribute("data-href");if(l===r||l===a)return n()}var s=document.createElement("link");s.rel="stylesheet",s.type="text/css",s.onload=n,s.onerror=function(n){var r=n&&n.target&&n.target.src||a,u=new Error("Loading CSS chunk "+e+" failed.\n("+r+")");u.code="CSS_CHUNK_LOAD_FAILED",u.request=r,delete c[e],s.parentNode.removeChild(s),t(u)},s.href=a;var d=document.getElementsByTagName("head")[0];d.appendChild(s)})).then((function(){c[e]=0})));var r=a[e];if(0!==r)if(r)n.push(r[2]);else{var u=new Promise((function(n,t){r=a[e]=[n,t]}));n.push(r[2]=u);var i,l=document.createElement("script");l.charset="utf-8",l.timeout=120,f.nc&&l.setAttribute("nonce",f.nc),l.src=o(e);var h=new Error;i=function(n){l.onerror=l.onload=null,clearTimeout(s);var t=a[e];if(0!==t){if(t){var r=n&&("load"===n.type?"missing":n.type),c=n&&n.target&&n.target.src;h.message="Loading chunk "+e+" failed.\n("+r+": "+c+")",h.name="ChunkLoadError",h.type=r,h.request=c,t[1](h)}a[e]=void 0}};var s=setTimeout((function(){i({type:"timeout",target:l})}),12e4);l.onerror=l.onload=i,document.head.appendChild(l)}return Promise.all(n)},f.m=e,f.c=r,f.d=function(e,n,t){f.o(e,n)||Object.defineProperty(e,n,{enumerable:!0,get:t})},f.r=function(e){"undefined"!==typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},f.t=function(e,n){if(1&n&&(e=f(e)),8&n)return e;if(4&n&&"object"===typeof e&&e&&e.__esModule)return e;var t=Object.create(null);if(f.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:e}),2&n&&"string"!=typeof e)for(var r in e)f.d(t,r,function(n){return e[n]}.bind(null,r));return t},f.n=function(e){var n=e&&e.__esModule?function(){return e["default"]}:function(){return e};return f.d(n,"a",n),n},f.o=function(e,n){return Object.prototype.hasOwnProperty.call(e,n)},f.p="/",f.oe=function(e){throw console.error(e),e};var i=window["webpackJsonp"]=window["webpackJsonp"]||[],l=i.push.bind(i);i.push=n,i=i.slice();for(var h=0;h<i.length;h++)n(i[h]);var s=l;t()})([]);</script><script src=/static/js/chunk-elementUI.3ce6472c.js></script><script src=/static/js/chunk-libs.0ba38446.js></script><script src=/static/js/app.51cc1e83.js></script></body></html>
        ```

        从以上输出的信息，可以知道私网Ingress的AI-Dashboard访问地址创建成功。

2.  配置局域网通过私网Ingress访问AI-Dashboard。

    **说明：** 该步骤需要根据您当前局域网的要求及规范自行配置，例如，阿里巴巴的局域网通过配置VPN访问私网。本文只介绍如何在集群配置私网Ingress的AI-Dashboard访问地址。

3.  通过curl命令在局域网通过私网Ingress访问AI-Dashboard。

    ```
    curl -v -H "Host: foo.bar.com" "http://172.16.0.46"
    ```

    系统输出类似如下结果：

    ```
    <!DOCTYPE html><html><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1"><meta name=viewport content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no"><link rel=icon href=/favicon.ico><title>AI Dashboard</title><link href=/static/css/app.fdd5f060.css rel=preload as=style><link href=/static/css/chunk-elementUI.68c70ad5.css rel=preload as=style><link href=/static/css/chunk-libs.cd8e3f29.css rel=preload as=style><link href=/static/js/app.51cc1e83.js rel=preload as=script><link href=/static/js/chunk-elementUI.3ce6472c.js rel=preload as=script><link href=/static/js/chunk-libs.0ba38446.js rel=preload as=script><link href=/static/css/chunk-elementUI.68c70ad5.css rel=stylesheet><link href=/static/css/chunk-libs.cd8e3f29.css rel=stylesheet><link href=/static/css/app.fdd5f060.css rel=stylesheet></head><body><noscript><strong>We're sorry but AI Dashboard doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script>(function(e){function n(n){for(var r,c,o=n[0],f=n[1],i=n[2],l=0,h=[];l<o.length;l++)c=o[l],Object.prototype.hasOwnProperty.call(a,c)&&a[c]&&h.push(a[c][0]),a[c]=0;for(r in f)Object.prototype.hasOwnProperty.call(f,r)&&(e[r]=f[r]);s&&s(n);while(h.length)h.shift()();return u.push.apply(u,i||[]),t()}function t(){for(var e,n=0;n<u.length;n++){for(var t=u[n],r=!0,c=1;c<t.length;c++){var o=t[c];0!==a[o]&&(r=!1)}r&&(u.splice(n--,1),e=f(f.s=t[0]))}return e}var r={},c={runtime:0},a={runtime:0},u=[];function o(e){return f.p+"static/js/"+({}[e]||e)+"."+{"chunk-17bb17be":"c9b35848","chunk-238c903c":"d185a3d3","chunk-4f275acc":"6af93bd3","chunk-25bb57a7":"9b26638f","chunk-5db32ce3":"0996363f","chunk-05347400":"3c60ab36","chunk-763a2e84":"c4af4017","chunk-1c7a5e41":"b9658c79","chunk-8644997e":"4ba2b652","chunk-a9f4b31a":"e2cabf7c","chunk-fa5e36be":"7ef41eba"}[e]+".js"}function f(n){if(r[n])return r[n].exports;var t=r[n]={i:n,l:!1,exports:{}};return e[n].call(t.exports,t,t.exports,f),t.l=!0,t.exports}f.e=function(e){var n=[],t={"chunk-17bb17be":1,"chunk-238c903c":1,"chunk-25bb57a7":1,"chunk-05347400":1,"chunk-1c7a5e41":1,"chunk-8644997e":1,"chunk-a9f4b31a":1,"chunk-fa5e36be":1};c[e]?n.push(c[e]):0!==c[e]&&t[e]&&n.push(c[e]=new Promise((function(n,t){for(var r="static/css/"+({}[e]||e)+"."+{"chunk-17bb17be":"77391137","chunk-238c903c":"3c7f5ad9","chunk-4f275acc":"31d6cfe0","chunk-25bb57a7":"e8fd7aee","chunk-5db32ce3":"31d6cfe0","chunk-05347400":"8075f389","chunk-763a2e84":"31d6cfe0","chunk-1c7a5e41":"0da2af62","chunk-8644997e":"977a2651","chunk-a9f4b31a":"2e58b0de","chunk-fa5e36be":"b17e519a"}[e]+".css",a=f.p+r,u=document.getElementsByTagName("link"),o=0;o<u.length;o++){var i=u[o],l=i.getAttribute("data-href")||i.getAttribute("href");if("stylesheet"===i.rel&&(l===r||l===a))return n()}var h=document.getElementsByTagName("style");for(o=0;o<h.length;o++){i=h[o],l=i.getAttribute("data-href");if(l===r||l===a)return n()}var s=document.createElement("link");s.rel="stylesheet",s.type="text/css",s.onload=n,s.onerror=function(n){var r=n&&n.target&&n.target.src||a,u=new Error("Loading CSS chunk "+e+" failed.\n("+r+")");u.code="CSS_CHUNK_LOAD_FAILED",u.request=r,delete c[e],s.parentNode.removeChild(s),t(u)},s.href=a;var d=document.getElementsByTagName("head")[0];d.appendChild(s)})).then((function(){c[e]=0})));var r=a[e];if(0!==r)if(r)n.push(r[2]);else{var u=new Promise((function(n,t){r=a[e]=[n,t]}));n.push(r[2]=u);var i,l=document.createElement("script");l.charset="utf-8",l.timeout=120,f.nc&&l.setAttribute("nonce",f.nc),l.src=o(e);var h=new Error;i=function(n){l.onerror=l.onload=null,clearTimeout(s);var t=a[e];if(0!==t){if(t){var r=n&&("load"===n.type?"missing":n.type),c=n&&n.target&&n.target.src;h.message="Loading chunk "+e+" failed.\n("+r+": "+c+")",h.name="ChunkLoadError",h.type=r,h.request=c,t[1](h)}a[e]=void 0}};var s=setTimeout((function(){i({type:"timeout",target:l})}),12e4);l.onerror=l.onload=i,document.head.appendChild(l)}return Promise.all(n)},f.m=e,f.c=r,f.d=function(e,n,t){f.o(e,n)||Object.defineProperty(e,n,{enumerable:!0,get:t})},f.r=function(e){"undefined"!==typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},f.t=function(e,n){if(1&n&&(e=f(e)),8&n)return e;if(4&n&&"object"===typeof e&&e&&e.__esModule)return e;var t=Object.create(null);if(f.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:e}),2&n&&"string"!=typeof e)for(var r in e)f.d(t,r,function(n){return e[n]}.bind(null,r));return t},f.n=function(e){var n=e&&e.__esModule?function(){return e["default"]}:function(){return e};return f.d(n,"a",n),n},f.o=function(e,n){return Object.prototype.hasOwnProperty.call(e,n)},f.p="/",f.oe=function(e){throw console.error(e),e};var i=window["webpackJsonp"]=window["webpackJsonp"]||[],l=i.push.bind(i);i.push=n,i=i.slice();for(var h=0;h<i.length;h++)n(i[h]);var s=l;t()})([]);</script><script src=/static/js/chunk-elementUI.3ce6472c.js></script><script src=/static/js/chunk-libs.0ba38446.js></script><script src=/static/js/app.51cc1e83.js></script></body></html>
    ```

    从以上输出的信息，可以知道局域网访问集群私网Ingress的AI-Dashboard成功。


**方式四：使用公网Ingress访问AI-Dashboard**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

5.  命名空间选择**kube-ai**。

    ![K-AI-4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6896484161/p237961.png)

6.  单击**创建**，在弹出的**创建**对话框中填写**名称**，**域名**等信息，**服务**选择**ai-dashboard-admin-ui**，单击**创建**。

    路由创建成功后，在路由页面的**规则**列可获取到已创建路由的访问地址。

    ![22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4064306161/p251383.png)

7.  修改被绑定RAM应用的回调地址。

    **说明：** 回调地址为目标路由**规则**列下链接，地址模板为：http://<域名\>/login/aliyun。

    1.  云账号登录[RAM控制台](https://ram.console.aliyun.com/)。

    2.  在左侧导航栏，单击**OAuth应用管理（公测中）**。

    3.  选择**企业应用**页签，单击包含目标集群ID的应用名称链接。

    4.  单击**编辑基本信息**。

    5.  在**创建应用**面板的**回调地址**文本框中修改RAM应用的回调地址。

8.  在集群管理页的**路由**页面，单击目标路由**规则**列下链接即可访问AI-Dashboard。


