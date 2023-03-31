# Managing static device groups
The **Groups** service provides APIs for accessing and managing both static and dynamic device groups. A static group is a group whose members are added or removed via API calls. In OpenManage Enterprise, static groups can be created and managed by users on the **All Devices** page (right-click on **Static Groups**). 

> This section contains the APIs relevant only to static device groups.

<!-- theme: info -->

## Create a static group

To create a static group, make a **POST** request to `/api/GroupService/Actions/GroupService.CreateGroup`.

**RBAC Privilege**: GROUP_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 

**Response payload**: `GroupId` of the new group (if succeeds)

|  Filter  |  Data Type  |  Usage  |  Description  |
| -------: | :-----------: | :-------: | :---- |
| **Name** | `String` | Required | The name of the new group; it must be unique |
| **Description** | `String` | Optional | An optional description string for the group |
| **MembershipTypeId** | `Integer` | Required | Must be 12 to specify that the is to have static membership |
| **ParentId** | `Integer` | Required  | The parent group for the new group.  Must the Group ID of the "Static Groups" group (which is 1021) or a sub-group of that group |

Example request:

```json
{
  "GroupModel" : {
    "Name" : "My Static Test Group",
    "Description" : "This is a static test group.",
    "MembershipTypeId" : 12,
    "ParentId" : 1021
  }
}
```

**Response payload**: `GroupId` of the new group (if succeeds)

## Add devices to a static group

To add devices, make a **POST** request to `/api/GroupService/Actions/GroupService.AddMemberDevices`.

**RBAC privilege**: GROUP_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 
 

|  Filter  |  Data Type  |  Usage  |  Description  |
| ---------: | :-----------: | :-------: | :----        |
| **GroupId** | `Integer` | Required | Must be the ID of an existing group |
| **MemberDeviceIds** | `Integer array` | Required  | Must supply one or more IDs of existing devices |

Example request:

```json
{
  "GroupId" : 10119,
  "MemberDeviceIds" : [ 10084, 10085 ]
}
```
Example response:

    If the devices are successfully removed from the group, it returns 1.


## Remove devices from a static group
To remove devices, make a **POST** request to `/api/GroupService/Actions/GroupService.RemoveMemberDevices`.

**RBAC privilege**: GROUP_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 


|  Filter  |  Data Type  |  Usage  |  Description |
| ---------: | :-----------: | :-------: | :---- |
| **GroupId** | `Integer` | Required | Must be the ID of an existing group |
| **MemberDeviceIds** | `Integer array` | Required  | Must supply one or more IDs of devices in group GroupId |


Example request:

```json
{
  "GroupId" : 10119,
  "MemberDeviceIds" : [ 10084 ]
}
```

Example response:

    If the devices are successfully removed from the group, it returns 1.



