# ECI metadata

This topic describes the items of Elastic Container Instance \(ECI\) metadata and how to obtain the metadata in an ECI.

ECI metadata is the basic information about an ECI in Alibaba Cloud, such as the ID, IP address, and region of the ECI, and the ID of the VSwitch.

## Obtain the ECI metadata

1.  Log on to the [ECI console](eci.console.aliyun.md). On the Container Group page, click the ID of the target ECI whose metadata you want to obtain. On the page that appears, click the Terminal tab. On the Terminal tab, select the target container from the Container Name drop-down list and click Terminal.
2.  Run the following command in the container to access the root directory of metadata:

    ```
    curl http://100.100.100.200/latest/meta-data/
    					
    ```

3.  Append the name of a specific metadata item to the command to obtain the corresponding information. For example, run the following command to obtain the ID of the ECI:

    ```
    curl http://100.100.100.200/latest/meta-data/instance-id
    					
    ```


## List of metadata items

The following table lists the metadata items that you can obtain in an ECI currently.

|Metadata item|Description|
|-------------|-----------|
|/dns-conf/nameservers|The Domain Name System \(DNS\) configuration of the ECI.|
|/eipv4|The Elastic IP Address \(EIP\) of the ECI. The value is an IPv4 address.|
|/hostname|The hostname of the ECI, which is the value of the ContainerGroupName field.|
|/instance-id|The ID of the ECI.|
|/mac|The media access control \(MAC\) address of the ECI.|
|/network/interfaces/|The MAC addresses of the network interface controllers \(NICs\).|
|/network/interfaces/macs/\[mac\]/network-interface-id|The ID of the NIC. Replace \[mac\] with the MAC address of the ECI.|
|/network/interfaces/macs/\[mac\]/netmask|The subnet mask of the NIC.|
|/network/interfaces/macs/\[mac\]/vswitch-cidr-block|The IPv4 classless inter-domain routing \(CIDR\) block of the VSwitch associated with the NIC.|
|/network/interfaces/macs/\[mac\]/vpc-cidr-block|The IPv4 CIDR block of the Virtual Private Cloud \(VPC\) where the NIC resides.|
|/network/interfaces/macs/\[mac\]/private-ipv4s|The private IPv4 addresses assigned to the NIC.|
|/network/interfaces/macs/\[mac\]/vpc-ipv6-cidr-blocks|The IPv6 CIDR block of the VPC where the NIC resides. This item is applicable to ECIs that reside in VPCs and are configured with IPv6 addresses.|
|/network/interfaces/macs/\[mac\]/vswitch-id|The VSwitch ID of the security group to which the NIC belongs.|
|/network/interfaces/macs/\[mac\]/vpc-id|The ID of the VPC where the security group resides.|
|/network/interfaces/macs/\[mac\]/primary-ip-address|The primary private IP address of the NIC.|
|/network/interfaces/macs/\[mac\]/gateway|The IPv4 gateway of the VPC where the NIC resides.|
|/instance/max-netbw-egress|The maximum internal outbound bandwidth of the ECI. Unit: Kbit/s.|
|/instance/max-netbw-ingerss|The maximum internal inbound bandwidth of the ECI. Unit: Kbit/s.|
|/network/interfaces/macs/\[mac\]/ipv6s|The IPv6 addresses assigned to the NIC. This item is applicable to ECIs that reside in VPCs and are configured with IPv6 addresses.|
|/network/interfaces/macs/\[mac\]/ipv6-gateway|The IPv6 gateway of the VPC where the NIC resides.|
|/network/interfaces/macs/\[mac\]/vswitch-ipv6-cidr-block|The IPv6 CIDR block of the VSwitch associated with the NIC. This item is applicable to ECIs that reside in VPCs and are configured with IPv6 addresses.|
|/private-ipv4|The private IPv4 address of the ECI.|
|/ntp-conf/ntp-servers|The IP address of the Network Time Protocol \(NTP\) server.|
|/owner-account-id|The Alibaba Cloud account ID of the ECI owner.|
|/region-id|The region where the ECI resides.|
|/serial-number|The serial number of the ECI.|
|/vpc-id|The ID of the VPC where the ECI resides.|
|/vpc-cidr-block|The CIDR block of the VPC where the ECI resides.|
|/vswitch-cidr-block|The CIDR block of the VSwitch associated with the ECI.|
|/vswitch-id|The ID of the VSwitch associated with the ECI.|
|/zone-id|The zone ID of the ECI.|
|/ram/security-credentials/\[role-name\]|The temporary Security Token Service \(STS\) credentials generated for the RAM role assumed by the ECI. You can obtain the STS credentials only after the ECI assumes a RAM role. Replace \[role-name\] with the actual name of the RAM role.|

