Authorization management 
=============================================

This topic describes how to perform authorization management on Container Service for Kubernetes (ACK) clusters by using examples. The examples include how to query and update the permissions of a Resource Access Management (RAM) user to manage ACK clusters. 
**Note**

You must install ACK SDK before you can use it to perform authorization management. For more information, see [Install ACK SDK](/intl.en-US/SDK examples (new)/Install ACK SDK.md).

Sample code 
--------------------------------

Python

    import os
    
    from alibabacloud_cs20151215.client import Client
    from alibabacloud_cs20151215.models import (
        DescribeUserPermissionResponseBody,
        GrantPermissionsRequest, GrantPermissionsRequestBody
    )
    from alibabacloud_tea_openapi.models import Config
    
    
    def convert_describe_permissions_to_grant_permissions_request_body(perms):
        permReqs = []
        for p in perms:
            req = GrantPermissionsRequestBody(
                cluster='',
                is_custom=False,
                role_name='',
                role_type='cluster',
                namespace='',
                is_ram_role=False,
            )
            resource_type = p.resource_type
            req.is_ram_role = bool(p.is_ram_role)
            if p.role_type == 'custom':
                req.is_custom = True
                req.role_name = p.role_name
            else:
                req.role_name = p.role_type
            resource_id = p.resource_id
            if '/' in resource_id:
                parts = resource_id.split('/')
                req.cluster = parts[0]
                req.namespace = parts[1]
                req.role_type = 'namespace'
            elif resource_type == 'cluster':
                req.cluster = resource_id
                req.role_type = 'cluster'
            if resource_type == 'console' and resource_id == 'all-clusters':
                req.role_type = 'all-clusters'
    
            permReqs.append(req)
    
        return permReqs
    
    
    def pretty_perms(perms):
        converted_perms = convert_describe_permissions_to_grant_permissions_request_body(perms)
        converted_perms.sort(key=lambda x: x.cluster)
    
        for p in converted_perms:
            cluster = p.cluster
            namespace = '* (All Namespaces)'
            permission = p.role_name
            is_custom = p.is_custom
            role_type = p.role_type
            if role_type == 'all-clusters':
                cluster = '* (All Clusters)'
            elif role_type == 'namespace':
                namespace = p.namespace
            if is_custom:
                permission = '{} (Custom)'.format(p.role_name)
    
            print('Cluster: {}\tNamespace: {}\tPermission: {}\t'
                  .format(cluster, namespace, permission))
    
    
    def describe_user_permission(client, uid):
        response = client.describe_user_permission(uid)
        return response.body
    
    
    def grant_permissions(client, uid, permissions):
        if permissions is None:
            permissions = []
        request = GrantPermissionsRequest(body=permissions)
        response = client.grant_permissions(uid, request)
        return response
    
    
    def grant_permissions_for_add_perm(client, uid, permissions):
        exist_perms = describe_user_permission(client, uid)
        perms = convert_describe_permissions_to_grant_permissions_request_body(exist_perms)
        perms.extend(permissions)
    
        request = GrantPermissionsRequest(body=perms)
        response = client.grant_permissions(uid, request)
        return response
    
    
    def grant_permissions_for_update_some_cluster_perms(client, uid, permissions):
        exist_perms = convert_describe_permissions_to_grant_permissions_request_body(
            describe_user_permission(client, uid))
        perms = []
        to_update_perm_map = {}
        for p in permissions:
            cluster = p.cluster
            if cluster not in to_update_perm_map:
                to_update_perm_map[cluster] = []
            to_update_perm_map[cluster].append(p)
        for p in exist_perms:
            cluster = p.cluster
            if cluster in to_update_perm_map:
                perms.extend(to_update_perm_map[cluster])
                del to_update_perm_map[cluster]
            else:
                perms.append(p)
        for _, p in to_update_perm_map.items():
            perms.extend(p)
    
        request = GrantPermissionsRequest(body=perms)
        response = client.grant_permissions(uid, request)
        return response
    
    
    def grant_permissions_for_delete_some_cluster_perms(client, uid, clusters):
        exist_perms = convert_describe_permissions_to_grant_permissions_request_body(
            describe_user_permission(client, uid))
        new_perms = []
        for p in exist_perms:
            cluster = p.cluster
            if cluster not in clusters:
                new_perms.append(p)
    
        request = GrantPermissionsRequest(body=new_perms)
        response = client.grant_permissions(uid, request)
        return response
    
    
    def main():
        config = Config()
        # The ALICLOUD_ACCESS_KEY_ID environment variable specifies the AccessKey ID that is used by an API request.
        config.access_key_id = os.environ['ALICLOUD_ACCESS_KEY_ID']
        # The ALICLOUD_ACCESS_KEY_SECRET environment variable specifies the AccessKey secret that is used by an API request.
        config.access_key_secret = os.environ['ALICLOUD_ACCESS_KEY_SECRET']
        config.region_id = 'cn-hangzhou'
        config.endpoint = 'cs.aliyuncs.com'
        client = Client(config)
    
        # The ALICLOUD_UID environment variable specifies the ID of the Resource Access Management (RAM) user that is used to perform operations.
        uid = os.environ['ALICLOUD_UID']
        print('=== DescribeUserPermission ===')
        perms = describe_user_permission(client, uid)
        pretty_perms(perms)
    
        print('=== GrantPermissions: updates the permissions of the RAM user to manage a specified ACK cluster. The permissions that have already been granted to the RAM user to manage this cluster are retained. ===')
        grant_permissions_for_add_perm(client, uid, [
            GrantPermissionsRequestBody(
                cluster='cf3185a1b11d7***',
                role_name='dev',
                role_type='cluster',
            ),
            GrantPermissionsRequestBody(
                cluster='c4be08afa6386***',
                role_name='restricted',
                role_type='cluster',
            ),
            # namespace
            GrantPermissionsRequestBody(
                cluster='ca336cf022ae14***',
                role_name='dev',
                role_type='namespace',
                namespace='default',
            ),
            # custom
            GrantPermissionsRequestBody(
                cluster='cf449e329a8784***',
                role_name='view',
                role_type='cluster',
                is_custom=True,
            ),
        ])
        pretty_perms(describe_user_permission(client, uid))
    
        print('== GrantPermissions: updates the permissions of the RAM user to manage a specified ACK cluster. The permissions that have already been granted to the RAM user to manage this cluster are overwritten. The permissions that have already been granted to the RAM user to manage other clusters are retained. ==')
        grant_permissions_for_update_some_cluster_perms(client, uid, [
            GrantPermissionsRequestBody(
                cluster='cf3185a1b11d7***',
                role_name='restricted',
                role_type='cluster',
            ),
            GrantPermissionsRequestBody(
                cluster='c0817aaa2c7cb***',
                role_name='admin',
                role_type='cluster',
            ),
        ])
        pretty_perms(describe_user_permission(client, uid))
    
        print('== GrantPermissions: updates the permissions of the RAM user to manage all ACK clusters. ==')
        grant_permissions_for_update_some_cluster_perms(client, uid, [
            GrantPermissionsRequestBody(
                cluster='',
                role_name='admin',
                role_type='all-clusters',
            ),
        ])
        pretty_perms(describe_user_permission(client, uid))
    
        print('== GrantPermissions: removes the permissions of the RAM user to manage a specified ACK cluster. ==')
        grant_permissions_for_delete_some_cluster_perms(client, uid, [
            'cf3185a1b11d7***',
            'c4be08afa6386***',
            # Removes the permissions of the RAM user to manage all ACK clusters.
            '',
        ])
        pretty_perms(describe_user_permission(client, uid))
    
        print('== GrantPermissions: removes all the permissions of the RAM user to manage ACK clusters. Permissions to manage the ACK clusters created by this RAM user are retained. ==')
        grant_permissions(client, uid, [])
        pretty_perms(describe_user_permission(client, uid))
    
    
    if __name__ == '__main__':
        main()


Go

    package main
    
    import (
            "fmt"
            "os"
            "sort"
            "strings"
    
            cs "github.com/alibabacloud-go/cs-20151215/v2/client"
            openapi "github.com/alibabacloud-go/darabonba-openapi/client"
            "github.com/alibabacloud-go/tea/tea"
    )
    
    func convertDescribePermissionsToGrantPermissionsRequestBody(perms []*cs.DescribeUserPermissionResponseBody) []*cs.GrantPermissionsRequestBody {
            permReqs := []*cs.GrantPermissionsRequestBody{}
            for _, p := range perms {
                    p := p
                    req := &cs.GrantPermissionsRequestBody{
                            Cluster:   nil,
                            IsCustom:  nil,
                            RoleName:  nil,
                            RoleType:  tea.String("cluster"),
                            Namespace: nil,
                            IsRamRole: nil,
                    }
                    resourceId := ""
                    resourceType := tea.StringValue(p.ResourceType)
    
                    req.IsRamRole = tea.Bool(tea.Int64Value(p.IsRamRole) == 1)
                    if tea.StringValue(p.RoleType) == "custom" {
                            req.IsCustom = tea.Bool(true)
                            req.RoleName = tea.String(tea.StringValue(p.RoleName))
                    } else {
                            req.RoleName = tea.String(tea.StringValue(p.RoleType))
                    }
                    resourceId = tea.StringValue(p.ResourceId)
                    if strings.Contains(resourceId, "/") {
                            parts := strings.Split(resourceId, "/")
                            cluster := parts[0]
                            namespace := parts[1]
                            req.Cluster = tea.String(cluster)
                            req.Namespace = tea.String(namespace)
                            req.RoleType = tea.String("namespace")
                    } else if resourceType == "cluster" {
                            cluster := resourceId
                            req.Cluster = tea.String(cluster)
                            req.RoleType = tea.String("cluster")
                    }
                    if resourceType == "console" && resourceId == "all-clusters" {
                            req.RoleType = tea.String("all-clusters")
                    }
    
                    permReqs = append(permReqs, req)
            }
    
            return permReqs
    }
    
    func prettyPerms(perms []*cs.DescribeUserPermissionResponseBody) {
            convertedPerms := convertDescribePermissionsToGrantPermissionsRequestBody(perms)
            sort.Slice(convertedPerms, func(i, j int) bool {
                    return tea.StringValue(convertedPerms[i].Cluster) < tea.StringValue(convertedPerms[j].Cluster)
            })
            for _, p := range convertedPerms {
                    cluster := tea.StringValue(p.Cluster)
                    namespace := "* (All Namespaces)"
                    permission := tea.StringValue(p.RoleName)
                    isCustom := tea.BoolValue(p.IsCustom)
                    roleType := tea.StringValue(p.RoleType)
                    switch roleType {
                    case "all-clusters":
                            cluster = "* (All Clusters)"
                            break
                    case "namespace":
                            namespace = tea.StringValue(p.Namespace)
                            break
                    }
                    if isCustom {
                            permission = fmt.Sprintf("%s (Custom)", tea.StringValue(p.RoleName))
                    }
    
                    fmt.Printf("Cluster: %s\tNamespace: %s\tPermission: %s\t\n", cluster, namespace, permission)
            }
    }
    
    func describeUserPermission(client *cs.Client, uid string) []*cs.DescribeUserPermissionResponseBody {
            resp, err := client.DescribeUserPermission(tea.String(uid))
            if err != nil {
                    panic(err)
            }
            return resp.Body
    }
    
    func grantPermissions(client *cs.Client, uid string, body []*cs.GrantPermissionsRequestBody) {
            if body == nil {
                    body = []*cs.GrantPermissionsRequestBody{}
            }
            req := &cs.GrantPermissionsRequest{
                    Body: body,
            }
            _, err := client.GrantPermissions(tea.String(uid), req)
            if err != nil {
                    panic(err)
            }
    }
    
    func grantPermissionsForAddPerm(client *cs.Client, uid string, body []*cs.GrantPermissionsRequestBody) {
            existPerms := describeUserPermission(client, uid)
            perms := convertDescribePermissionsToGrantPermissionsRequestBody(existPerms)
            perms = append(perms, body...)
            req := &cs.GrantPermissionsRequest{
                    Body: perms,
            }
            _, err := client.GrantPermissions(tea.String(uid), req)
            if err != nil {
                    panic(err)
            }
    }
    
    func grantPermissionsForUpdateSomeClusterPerms(client *cs.Client, uid string, body []*cs.GrantPermissionsRequestBody) {
            existPerms := convertDescribePermissionsToGrantPermissionsRequestBody(describeUserPermission(client, uid))
            newPerms := []*cs.GrantPermissionsRequestBody{}
            toUpdatePermMap := map[string][]*cs.GrantPermissionsRequestBody{}
            for _, p := range body {
                    p := p
                    cluster := tea.StringValue(p.Cluster)
                    if _, ok := toUpdatePermMap[cluster]; !ok {
                            toUpdatePermMap[cluster] = []*cs.GrantPermissionsRequestBody{}
                    }
                    toUpdatePermMap[cluster] = append(toUpdatePermMap[cluster], p)
            }
            for _, p := range existPerms {
                    p := p
                    cluster := tea.StringValue(p.Cluster)
                    if v, ok := toUpdatePermMap[cluster]; ok {
                            newPerms = append(newPerms, v...)
                            delete(toUpdatePermMap, cluster)
                    } else {
                            newPerms = append(newPerms, p)
                    }
            }
            for _, p := range toUpdatePermMap {
                    newPerms = append(newPerms, p...)
            }
    
            req := &cs.GrantPermissionsRequest{
                    Body: newPerms,
            }
            _, err := client.GrantPermissions(tea.String(uid), req)
            if err != nil {
                    panic(err)
            }
    }
    
    func grantPermissionsForDeleteSomeClusterPerms(client *cs.Client, uid string, clusters []string) {
            existPerms := convertDescribePermissionsToGrantPermissionsRequestBody(describeUserPermission(client, uid))
            newPerms := []*cs.GrantPermissionsRequestBody{}
            toDeleteClusterMap := map[string]bool{}
            for _, c := range clusters {
                    toDeleteClusterMap[c] = true
            }
            for _, p := range existPerms {
                    p := p
                    cluster := tea.StringValue(p.Cluster)
                    if !toDeleteClusterMap[cluster] {
                            newPerms = append(newPerms, p)
                    }
            }
    
            req := &cs.GrantPermissionsRequest{
                    Body: newPerms,
            }
            _, err := client.GrantPermissions(tea.String(uid), req)
            if err != nil {
                    panic(err)
            }
    }
    
    func main() {
            config := new(openapi.Config)
            # The ALICLOUD_ACCESS_KEY_ID environment variable specifies the AccessKey ID that is used by an API request.
            # The ALICLOUD_ACCESS_KEY_SECRET environment variable specifies the AccessKey secret that is used by an API request.
            config.SetAccessKeyId(os.Getenv("ALICLOUD_ACCESS_KEY_ID")).
                    SetAccessKeySecret(os.Getenv("ALICLOUD_ACCESS_KEY_SECRET")).
                    SetRegionId("cn-hangzhou").
                    SetEndpoint("cs.aliyuncs.com")
            client, err := cs.NewClient(config)
            if err != nil {
                    panic(err)
            }
    
            # The ALICLOUD_UID environment variable specifies the ID of the RAM user that is used to perform operations.
            uid := os.Getenv("ALICLOUD_UID")
            fmt.Println("=== DescribeUserPermission ===")
            perms := describeUserPermission(client, uid)
            prettyPerms(perms)
    
            print('=== GrantPermissions: updates the RAM user the permissions to manage a specified ACK cluster. The permissions that have already been granted to the RAM user to manage this cluster are retained. ===")
            grantPermissionsForAddPerm(client, uid, []*cs.GrantPermissionsRequestBody{
                    {
                            Cluster:  tea.String("cf3185a1b1***"),
                            RoleName: tea.String("dev"),
                            RoleType: tea.String("cluster"),
                    },
                    {
                            Cluster:  tea.String("c4be08afa6***"),
                            RoleName: tea.String("restricted"),
                            RoleType: tea.String("cluster"),
                    },
                    // namespace
                    {
                            Cluster:   tea.String("ca336cf022***"),
                            RoleName:  tea.String("dev"),
                            RoleType:  tea.String("namespace"),
                            Namespace: tea.String("default"),
                    },
                    // custom
                    {
                            Cluster:  tea.String("cf449e329a87***"),
                            RoleName: tea.String("view"),
                            RoleType: tea.String("cluster"),
                            IsCustom: tea.Bool(true),
                    },
            })
            prettyPerms(describeUserPermission(client, uid))
    
            print('== GrantPermissions: updates the permissions of the RAM user to manage a specified ACK cluster. The permissions that have already been granted to the RAM user to manage this cluster are overwritten. The permissions that have already been granted to the RAM user to manage other clusters are retained. ==")
            grantPermissionsForUpdateSomeClusterPerms(client, uid, []*cs.GrantPermissionsRequestBody{
                    {
                            Cluster:  tea.String("cf3185a1b11d7***"),
                            RoleName: tea.String("restricted"),
                            RoleType: tea.String("cluster"),
                    },
                    {
                            Cluster:  tea.String("c0817aaa2c7cb**"),
                            RoleName: tea.String("admin"),
                            RoleType: tea.String("cluster"),
                    },
            })
            prettyPerms(describeUserPermission(client, uid))
    
            print('== GrantPermissions: updates the permissions of the RAM user to manage all ACK clusters. ==")
            grantPermissionsForUpdateSomeClusterPerms(client, uid, []*cs.GrantPermissionsRequestBody{
                    {
                            Cluster:  tea.String(""),
                            RoleName: tea.String("admin"),
                            RoleType: tea.String("all-clusters"),
                    },
            })
            prettyPerms(describeUserPermission(client, uid))
    
            print('== GrantPermissions: removes the permissions of the RAM user to manage a specified ACK cluster. ==")
            grantPermissionsForDeleteSomeClusterPerms(client, uid, []string{
                    "cf3185a1b11d74f7**",
                    "c4be08afa638644a**",
                    # removes the permissions of the RAM user to manage all ACK clusters.
                    "",
            })
            prettyPerms(describeUserPermission(client, uid))
    
            print('== GrantPermissions: removes all the permissions of the RAM user to manage ACK clusters. Permissions to manage the ACK clusters created by this RAM user are retained. ==")
            grantPermissions(client, uid, []*cs.GrantPermissionsRequestBody{})
            prettyPerms(describeUserPermission(client, uid))
    }







