# ECI实例元数据

本文介绍ECI实例元数据并且如何在ECI实例内部获取元数据。

ECI实例元数据包含ECI实例在阿里云系统中的基本信息，例如实例ID、IP地址、所属地域、交换机ID等。

## 获取实例元数据

1.  您可以通过[ECI控制台](eci.console.aliyun.md)选择需要获取元数据的容器组，选定容器，点击连接。
2.  在容器内部执行以下命令访问元数据的根目录：

    ```
    curl http://100.100.100.200/latest/meta-data/
    					
    ```

3.  在URL中添加具体的元数据名称即可获取具体的元数据。例如：执行以下命令获取ECI实例ID

    ```
    curl http://100.100.100.200/latest/meta-data/instance-id
    					
    ```


## 实例元数据列表

下表包含了ECI实例目前能获取的基本实例元数据项：

|实例元数据项|说明|
|------|--|
|/dns-conf/nameservers|实例的DNS配置|
|/eipv4|实例的弹性公网IP（IPv4类型）。|
|/hostname|实例的主机名，对应到ContainerGroupName。|
|/instance-id|实例ID。|
|/mac|实例的MAC地址。|
|/network/interfaces/|macs网卡的MAC地址列表。|
|/network/interfaces/macs/\[mac\]/network-interface-id|网卡的标识ID，其中\[mac\]参数需要替换为实例MAC地址。|
|/network/interfaces/macs/\[mac\]/netmask|网卡对应的子网掩码。|
|/network/interfaces/macs/\[mac\]/vswitch-cidr-block|网卡所属的虚拟交换机IPv4 CIDR段。|
|/network/interfaces/macs/\[mac\]/vpc-cidr-block|网卡所属的VPC IPv4 CIDR段。|
|/network/interfaces/macs/\[mac\]/private-ipv4s|网卡分配的私网IPv4地址列表。|
|/network/interfaces/macs/\[mac\]/vpc-ipv6-cidr-blocks|网卡所属的VPC IPv6 CIDR段，仅支持已配置了IPv6的VPC类型实例。|
|/network/interfaces/macs/\[mac\]/vswitch-id|网卡所属安全组的虚拟交换机ID。|
|/network/interfaces/macs/\[mac\]/vpc-id|网卡所属安全组的VPC ID。|
|/network/interfaces/macs/\[mac\]/primary-ip-address|网卡主私有IP地址。|
|/network/interfaces/macs/\[mac\]/gateway|网卡对应的IPv4网关地址。|
|/instance/max-netbw-egress|实例规格的出方向内网最大带宽。单位：Kbit/s。|
|/instance/max-netbw-ingerss|实例规格的入方向内网最大带宽。单位：Kbit/s。|
|/network/interfaces/macs/\[mac\]/ipv6s|网卡分配的IPv6地址列表，仅支持已配置了IPv6的VPC类型实例。|
|/network/interfaces/macs/\[mac\]/ipv6-gateway|网卡所属的VPC的IPv6网关地址。|
|/network/interfaces/macs/\[mac\]/vswitch-ipv6-cidr-block|网卡所属的虚拟交换机IPv6 CIDR段，仅支持已配置了IPv6的VPC类型实例。|
|/private-ipv4|实例的私网IPv4地址。|
|/ntp-conf/ntp-servers|NTP服务器地址。|
|/owner-account-id|实例拥有者的阿里云账号ID。|
|/region-id|实例所属地域。|
|/serial-number|实例所对应的序列号。|
|/vpc-id|实例所属VPC ID。|
|/vpc-cidr-block|实例所属VPC CIDR段。|
|/vswitch-cidr-block|实例所属虚拟交换机CIDR段。|
|/vswitch-id|实例所属虚拟交换机ID。|
|/zone-id|实例所属可用区。|
|/ram/security-credentials/\[role-name\]|实例RAM角色策略所生成的STS临时凭证。只有在实例指定了RAM角色后，您才能获取STS临时凭证。其中\[role-name\]参数需要替换为实例RAM角色的名称。|

