---
keyword: cloud-native AI Dashboard
---

# Access cloud-native AI Dashboard

You can access AI Dashboard by using sshuttle, SSL VPNs, internal-facing Ingresses, and Internet-facing Ingresses. This topic describes how to access AI Dashboard from a professional Kubernetes cluster.

-   A professional Kubernetes cluster is created. Make sure that **Monitoring Agents** and **Log Service** are enabled on the Component Configurations wizard page when you create the cluster. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The Kubernetes version of the professional managed Kubernetes cluster is V1.18 or later.
-   The following conditions are met before you configure access to AI Dashboard by using sshuttle:
    -   A jump server that uses SSH and can access the Internet is added to the cluster. For more information, see [Configure SNAT entries for existing ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Configure SNAT entries for existing ACK clusters.md).
    -   Python 3.6 or later is installed on the jump server.
    -   The username and password that you can use to connect to the jump server are obtained.
    -   sshuttle is the most cost-effective solution to set up a VPN over SSH. We recommend that you enable an IP whitelist for the security group to which the cluster belongs. The IP whitelist specifies the IP addresses that the cluster is allowed to access over the Internet. For more information, see [Allow your instance to connect to only specified public IP addresses](https://help.aliyun.com/document_detail/25475.html?spm=5176.2020520101securityGroupDetail.0.dexternal.2d004df5d6w9mV#specifyIpAccess).

## Access AI Dashboard

You can access AI Dashboard by using four methods. The following table compares the advantages and disadvantages of these methods.

|Access method|Network|Advantage|Disadvantage|
|-------------|-------|---------|------------|
|Internet-facing Ingress|Internet|The configuration is simple.|Exposes the cluster to security risks.|
|Sshuttle|Internal network|The configuration is simple and cost-effective.|The connection may be unstable.|
|SSL VPN|Internal network|The connection is stable.|Has requirements for the CIDR block of the cluster. The configuration is complicated.|
|Internal-facing Ingress|Internal network|The configuration is simple.|Requires you to connect the LAN and the virtual private cloud \(VPC\) where the cluster is deployed.|

**Method 1: Use an Internet-facing Ingress to access AI Dashboard**

1.  Select **AI Dashboard Console** on the Cloud-native AI Component Set page. In the **Note** dialog box, select **Public Domain \(you must install an Ingress controller for access from the Internet\)**. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Install Cloud-native AI Component Set and access AI Dashboard.md).

    ![ai15](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6981579161/p261809.png)

2.  After the **cloud-native AI component set** is deployed. The address of AI Dashboard is displayed on the Cloud-native AI Component Set page. You can access AI Dashboard through this address.

    ![ai16](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6981579161/p261830.png)


**Method 2: Use sshuttle to access AI Dashboard**

1.  Run the following command to install sshuttle in the macOS operating system.

    In this example, sshuttle 1.0.5 is installed in the macOS operating system.

    ```
    brew install sshuttle@1.0.5
    ```

    For more information about how to install sshuttle in other operating systems, see [sshuttle documentation](https://github.com/sshuttle/sshuttle).

2.  Run the following command to run sshuttle as a proxy server. You must specify the IP address of the jump server that allows SSH access and the IP address of the cluster node that you want to access.

    -   The IP address of the node in the cluster: 192.168.100.70.
    -   The public IP address of the jump server: 39.96.XX.XX.
    ```
    sudo sshuttle -r jack@39.96.XX.XX 192.168.100.0/24 -vv
    ```

3.  In the address bar of your browser, enter the address of AI Dashboard, for example, `http://192.168.100.70:31101/`.


**Method 3: Use an SSL VPN to access AI Dashboard**

-   You can configure SSL-VPN on a VPN gateway to access AI Dashboard from a remote client. For more information, see [Use SSL-VPN in the classic network](/intl.en-US/Best Practices/Use SSL-VPN in the classic network/Access cloud resources in a classic network from a Mac client.md).
-   -   For more information about how to access AI Dashboard by using the Linux operating system, see [Step 4: Configure the client](/intl.en-US/Best Practices/Use SSL-VPN in the classic network/Access cloud resources in a classic network from a Linux client.md).
-   For more information about how to access AI Dashboard by using the macOS operating system, see [Step 4: Configure the client](/intl.en-US/Best Practices/Use SSL-VPN in the classic network/Access cloud resources in a classic network from a Mac client.md).
-   For more information about how to access AI Dashboard by using the Windows operating system, see [Step 4: Configure the client](/intl.en-US/Best Practices/Use SSL-VPN in the classic network/Access cloud resources in a classic network from a Windows client.md).

**Method 4: Use an Internal-facing Ingress to access AI Dashboard**

To control access to AI Dashboard, we recommend that you use the Ingress controllers developed by Alibaba Cloud. You can configure Ingresses that allow only access from within the VPC. For more information about Ingresses, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Overview.md).

1.  Configure an internal-facing Ingress that allows access to AI Dashboard from within the VPC where the cluster is deployed.

    **Note:** When the system creates the cluster, an Internet-facing Ingress controller named **ack-ingress-nginx** is automatically installed in the **kube-system** namespace. If you want to enable internal-facing Ingresses, you must deploy an internal-facing Ingress controller.

    1.  Deploy **ack-ingress-nginx** in the **kube-ai** namespace. Add `privateService.enable=true` and `publicService:enabled=false` to the component configurations. For more information, see [Deploy multiple ingress controllers on a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Deploy multiple ingress controllers on a cluster.md).

        You must set the following configurations:

        -   `privateService.enable=true`
        -   `publicService:enabled=false`
        -   controller.image.tag: the identifier of the IngressClass that is listened on by the Ingress controller.

            **Note:** The IngressClass identifier that is listened on by an Ingress controller must be unique and cannot be set to nginx. This is because nginx is the identifier listened on by the default Ingress controller of the cluster.

    2.  Create an internal-facing Ingress that is used to access AI Dashboard by using the following YAML template:

        ```
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: admin-ui-ingress
          namespace: kube-ai
          annotations:
            # Specify the IngressClass that is listened on by the Ingress controller. 
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

        **Note:** `kubernetes.io/ingress.class: "intranet-nginx"`: Set the value to the IngressClass that is listened on by the Ingress controller.

    3.  Run the following command to query the address of the created internal-facing Ingress that is used to access AI Dashboard:

        ```
        kubectl get ingress admin-ui-ingress -n kube-ai
        ```

        Sample output:

        ```
        NAME               CLASS    HOSTS         ADDRESS       PORTS   AGE
        admin-ui-ingress   <none>   foo.bar.com   172.16.XX.XX   80      18m
        ```

    4.  Run the following command to connect to the address of the internal-facing Ingress that is used to access AI Dashboard:

        ```
        curl -v -H "Host: foo.bar.com" "http://172.16.0.46"
        ```

        Sample output:

        ```
        <!DOCTYPE html><html><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1"><meta name=viewport content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no"><link rel=icon href=/favicon.ico><title>AI Dashboard</title><link href=/static/css/app.fdd5f060.css rel=preload as=style><link href=/static/css/chunk-elementUI.68c70ad5.css rel=preload as=style><link href=/static/css/chunk-libs.cd8e3f29.css rel=preload as=style><link href=/static/js/app.51cc1e83.js rel=preload as=script><link href=/static/js/chunk-elementUI.3ce6472c.js rel=preload as=script><link href=/static/js/chunk-libs.0ba38446.js rel=preload as=script><link href=/static/css/chunk-elementUI.68c70ad5.css rel=stylesheet><link href=/static/css/chunk-libs.cd8e3f29.css rel=stylesheet><link href=/static/css/app.fdd5f060.css rel=stylesheet></head><body><noscript><strong>We're sorry but AI Dashboard doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script>(function(e){function n(n){for(var r,c,o=n[0],f=n[1],i=n[2],l=0,h=[];l<o.length;l++)c=o[l],Object.prototype.hasOwnProperty.call(a,c)&&a[c]&&h.push(a[c][0]),a[c]=0;for(r in f)Object.prototype.hasOwnProperty.call(f,r)&&(e[r]=f[r]);s&&s(n);while(h.length)h.shift()();return u.push.apply(u,i||[]),t()}function t(){for(var e,n=0;n<u.length;n++){for(var t=u[n],r=!0,c=1;c<t.length;c++){var o=t[c];0!==a[o]&&(r=!1)}r&&(u.splice(n--,1),e=f(f.s=t[0]))}return e}var r={},c={runtime:0},a={runtime:0},u=[];function o(e){return f.p+"static/js/"+({}[e]||e)+"."+{"chunk-17bb17be":"c9b35848","chunk-238c903c":"d185a3d3","chunk-4f275acc":"6af93bd3","chunk-25bb57a7":"9b26638f","chunk-5db32ce3":"0996363f","chunk-05347400":"3c60ab36","chunk-763a2e84":"c4af4017","chunk-1c7a5e41":"b9658c79","chunk-8644997e":"4ba2b652","chunk-a9f4b31a":"e2cabf7c","chunk-fa5e36be":"7ef41eba"}[e]+".js"}function f(n){if(r[n])return r[n].exports;var t=r[n]={i:n,l:!1,exports:{}};return e[n].call(t.exports,t,t.exports,f),t.l=!0,t.exports}f.e=function(e){var n=[],t={"chunk-17bb17be":1,"chunk-238c903c":1,"chunk-25bb57a7":1,"chunk-05347400":1,"chunk-1c7a5e41":1,"chunk-8644997e":1,"chunk-a9f4b31a":1,"chunk-fa5e36be":1};c[e]?n.push(c[e]):0!==c[e]&&t[e]&&n.push(c[e]=new Promise((function(n,t){for(var r="static/css/"+({}[e]||e)+"."+{"chunk-17bb17be":"77391137","chunk-238c903c":"3c7f5ad9","chunk-4f275acc":"31d6cfe0","chunk-25bb57a7":"e8fd7aee","chunk-5db32ce3":"31d6cfe0","chunk-05347400":"8075f389","chunk-763a2e84":"31d6cfe0","chunk-1c7a5e41":"0da2af62","chunk-8644997e":"977a2651","chunk-a9f4b31a":"2e58b0de","chunk-fa5e36be":"b17e519a"}[e]+".css",a=f.p+r,u=document.getElementsByTagName("link"),o=0;o<u.length;o++){var i=u[o],l=i.getAttribute("data-href")||i.getAttribute("href");if("stylesheet"===i.rel&&(l===r||l===a))return n()}var h=document.getElementsByTagName("style");for(o=0;o<h.length;o++){i=h[o],l=i.getAttribute("data-href");if(l===r||l===a)return n()}var s=document.createElement("link");s.rel="stylesheet",s.type="text/css",s.onload=n,s.onerror=function(n){var r=n&&n.target&&n.target.src||a,u=new Error("Loading CSS chunk "+e+" failed.\n("+r+")");u.code="CSS_CHUNK_LOAD_FAILED",u.request=r,delete c[e],s.parentNode.removeChild(s),t(u)},s.href=a;var d=document.getElementsByTagName("head")[0];d.appendChild(s)})).then((function(){c[e]=0})));var r=a[e];if(0!==r)if(r)n.push(r[2]);else{var u=new Promise((function(n,t){r=a[e]=[n,t]}));n.push(r[2]=u);var i,l=document.createElement("script");l.charset="utf-8",l.timeout=120,f.nc&&l.setAttribute("nonce",f.nc),l.src=o(e);var h=new Error;i=function(n){l.onerror=l.onload=null,clearTimeout(s);var t=a[e];if(0!==t){if(t){var r=n&&("load"===n.type?"missing":n.type),c=n&&n.target&&n.target.src;h.message="Loading chunk "+e+" failed.\n("+r+": "+c+")",h.name="ChunkLoadError",h.type=r,h.request=c,t[1](h)}a[e]=void 0}};var s=setTimeout((function(){i({type:"timeout",target:l})}),12e4);l.onerror=l.onload=i,document.head.appendChild(l)}return Promise.all(n)},f.m=e,f.c=r,f.d=function(e,n,t){f.o(e,n)||Object.defineProperty(e,n,{enumerable:!0,get:t})},f.r=function(e){"undefined"!==typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},f.t=function(e,n){if(1&n&&(e=f(e)),8&n)return e;if(4&n&&"object"===typeof e&&e&&e.__esModule)return e;var t=Object.create(null);if(f.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:e}),2&n&&"string"!=typeof e)for(var r in e)f.d(t,r,function(n){return e[n]}.bind(null,r));return t},f.n=function(e){var n=e&&e.__esModule?function(){return e["default"]}:function(){return e};return f.d(n,"a",n),n},f.o=function(e,n){return Object.prototype.hasOwnProperty.call(e,n)},f.p="/",f.oe=function(e){throw console.error(e),e};var i=window["webpackJsonp"]=window["webpackJsonp"]||[],l=i.push.bind(i);i.push=n,i=i.slice();for(var h=0;h<i.length;h++)n(i[h]);var s=l;t()})([]);</script><script src=/static/js/chunk-elementUI.3ce6472c.js></script><script src=/static/js/chunk-libs.0ba38446.js></script><script src=/static/js/app.51cc1e83.js></script></body></html>
        ```

        The output indicates that the address of the internal-facing Ingress used to access AI Dashboard is created.

2.  Configure a LAN to allow access to AI Dashboard through the internal-facing Ingress.

    **Note:** In this step, you need to configure a LAN based on its specifications and your requirements. For example, you need to configure a VPN gateway for a LAN of Alibaba Cloud to enable access from this LAN to AI Dashboard. This topic only describes how to configure an internal-facing Ingress to enable access to AI Dashboard.

3.  Run the following curl command to access AI Dashboard from a LAN by using the internal-facing Ingress:

    ```
    curl -v -H "Host: foo.bar.com" "http://172.16.0.46"
    ```

    Sample output:

    ```
    <!DOCTYPE html><html><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1"><meta name=viewport content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no"><link rel=icon href=/favicon.ico><title>AI Dashboard</title><link href=/static/css/app.fdd5f060.css rel=preload as=style><link href=/static/css/chunk-elementUI.68c70ad5.css rel=preload as=style><link href=/static/css/chunk-libs.cd8e3f29.css rel=preload as=style><link href=/static/js/app.51cc1e83.js rel=preload as=script><link href=/static/js/chunk-elementUI.3ce6472c.js rel=preload as=script><link href=/static/js/chunk-libs.0ba38446.js rel=preload as=script><link href=/static/css/chunk-elementUI.68c70ad5.css rel=stylesheet><link href=/static/css/chunk-libs.cd8e3f29.css rel=stylesheet><link href=/static/css/app.fdd5f060.css rel=stylesheet></head><body><noscript><strong>We're sorry but AI Dashboard doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script>(function(e){function n(n){for(var r,c,o=n[0],f=n[1],i=n[2],l=0,h=[];l<o.length;l++)c=o[l],Object.prototype.hasOwnProperty.call(a,c)&&a[c]&&h.push(a[c][0]),a[c]=0;for(r in f)Object.prototype.hasOwnProperty.call(f,r)&&(e[r]=f[r]);s&&s(n);while(h.length)h.shift()();return u.push.apply(u,i||[]),t()}function t(){for(var e,n=0;n<u.length;n++){for(var t=u[n],r=!0,c=1;c<t.length;c++){var o=t[c];0!==a[o]&&(r=!1)}r&&(u.splice(n--,1),e=f(f.s=t[0]))}return e}var r={},c={runtime:0},a={runtime:0},u=[];function o(e){return f.p+"static/js/"+({}[e]||e)+"."+{"chunk-17bb17be":"c9b35848","chunk-238c903c":"d185a3d3","chunk-4f275acc":"6af93bd3","chunk-25bb57a7":"9b26638f","chunk-5db32ce3":"0996363f","chunk-05347400":"3c60ab36","chunk-763a2e84":"c4af4017","chunk-1c7a5e41":"b9658c79","chunk-8644997e":"4ba2b652","chunk-a9f4b31a":"e2cabf7c","chunk-fa5e36be":"7ef41eba"}[e]+".js"}function f(n){if(r[n])return r[n].exports;var t=r[n]={i:n,l:!1,exports:{}};return e[n].call(t.exports,t,t.exports,f),t.l=!0,t.exports}f.e=function(e){var n=[],t={"chunk-17bb17be":1,"chunk-238c903c":1,"chunk-25bb57a7":1,"chunk-05347400":1,"chunk-1c7a5e41":1,"chunk-8644997e":1,"chunk-a9f4b31a":1,"chunk-fa5e36be":1};c[e]?n.push(c[e]):0!==c[e]&&t[e]&&n.push(c[e]=new Promise((function(n,t){for(var r="static/css/"+({}[e]||e)+"."+{"chunk-17bb17be":"77391137","chunk-238c903c":"3c7f5ad9","chunk-4f275acc":"31d6cfe0","chunk-25bb57a7":"e8fd7aee","chunk-5db32ce3":"31d6cfe0","chunk-05347400":"8075f389","chunk-763a2e84":"31d6cfe0","chunk-1c7a5e41":"0da2af62","chunk-8644997e":"977a2651","chunk-a9f4b31a":"2e58b0de","chunk-fa5e36be":"b17e519a"}[e]+".css",a=f.p+r,u=document.getElementsByTagName("link"),o=0;o<u.length;o++){var i=u[o],l=i.getAttribute("data-href")||i.getAttribute("href");if("stylesheet"===i.rel&&(l===r||l===a))return n()}var h=document.getElementsByTagName("style");for(o=0;o<h.length;o++){i=h[o],l=i.getAttribute("data-href");if(l===r||l===a)return n()}var s=document.createElement("link");s.rel="stylesheet",s.type="text/css",s.onload=n,s.onerror=function(n){var r=n&&n.target&&n.target.src||a,u=new Error("Loading CSS chunk "+e+" failed.\n("+r+")");u.code="CSS_CHUNK_LOAD_FAILED",u.request=r,delete c[e],s.parentNode.removeChild(s),t(u)},s.href=a;var d=document.getElementsByTagName("head")[0];d.appendChild(s)})).then((function(){c[e]=0})));var r=a[e];if(0!==r)if(r)n.push(r[2]);else{var u=new Promise((function(n,t){r=a[e]=[n,t]}));n.push(r[2]=u);var i,l=document.createElement("script");l.charset="utf-8",l.timeout=120,f.nc&&l.setAttribute("nonce",f.nc),l.src=o(e);var h=new Error;i=function(n){l.onerror=l.onload=null,clearTimeout(s);var t=a[e];if(0!==t){if(t){var r=n&&("load"===n.type?"missing":n.type),c=n&&n.target&&n.target.src;h.message="Loading chunk "+e+" failed.\n("+r+": "+c+")",h.name="ChunkLoadError",h.type=r,h.request=c,t[1](h)}a[e]=void 0}};var s=setTimeout((function(){i({type:"timeout",target:l})}),12e4);l.onerror=l.onload=i,document.head.appendChild(l)}return Promise.all(n)},f.m=e,f.c=r,f.d=function(e,n,t){f.o(e,n)||Object.defineProperty(e,n,{enumerable:!0,get:t})},f.r=function(e){"undefined"!==typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},f.t=function(e,n){if(1&n&&(e=f(e)),8&n)return e;if(4&n&&"object"===typeof e&&e&&e.__esModule)return e;var t=Object.create(null);if(f.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:e}),2&n&&"string"!=typeof e)for(var r in e)f.d(t,r,function(n){return e[n]}.bind(null,r));return t},f.n=function(e){var n=e&&e.__esModule?function(){return e["default"]}:function(){return e};return f.d(n,"a",n),n},f.o=function(e,n){return Object.prototype.hasOwnProperty.call(e,n)},f.p="/",f.oe=function(e){throw console.error(e),e};var i=window["webpackJsonp"]=window["webpackJsonp"]||[],l=i.push.bind(i);i.push=n,i=i.slice();for(var h=0;h<i.length;h++)n(i[h]);var s=l;t()})([]);</script><script src=/static/js/chunk-elementUI.3ce6472c.js></script><script src=/static/js/chunk-libs.0ba38446.js></script><script src=/static/js/app.51cc1e83.js></script></body></html>
    ```

    The output indicates that AI Dashboard can be accessed from the LAN by using the internal-facing Ingress.


