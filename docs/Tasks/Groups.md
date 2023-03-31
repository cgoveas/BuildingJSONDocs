# Managing device groups 
The **Groups** service provides APIs for accessing and managing static and dynamic device groups.

In OpenManage Enterprise, groups are used for both device groups (on the **All Devices** screen) and for reports (on the **Reports** screen).  On the **All Devices** screen, users can create either static or dynamic (query) groups. Static groups have specifically-assigned member devices, whereas dynamic (query) group membership is determined by a query set for the group.

<!-- theme: info -->

## Get device groups
To get information for all (static and query) device groups, send a **GET** request to `/api/GroupService/Groups`. Optional filters may be specified with a request.
- Multiple filters may be specified. 
- All specified filter conditions must be satisfied to include a group in the result set.
- String comparisons are case-insensitive.

> SBAC filtering is applied, so only groups that are visible to the current user are returned.

**RBAC Privilege**: VIEW
 
 Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 


|  Filter  |  Data Type  |  Description  |
| -------: | :---------: | :------------ |
| **top** | `Integer` | The maximum number of detail rows to return; for paging use.  Defaults to no limit. |
| **skip** | `Integer` | The number of result rows to skip; for paging use.  Defaults to zero. |
| **Name** | `String` | Get groups whose Name contains a specified string |
| **Description** | `String` | Get groups whose Description contains a specified string |
| **TypeId** | `Integer` | Get groups whose TypeId matches a specified value |
| **MembershipTypId** | `Integer` | Get groups whose MembershipTypeId matches a specified value |
| **GlobalStatus** | `Integer` | Get groups whose GlobalStatus matches a specified value |
| **DefinitionId** | `Integer` | Get groups whose DefinitionId matches a specified value |
| **DefinitionDescription** | `String` | Get groups whose DefinitionDescription contains a specified string |
| **Visible** | `Boolean` | Get groups whose Visible attribute has a specified value |
| **CreatedBy** | `String` | Get groups whose CreatedBy contains a specified value |
| **UpdatedBy** | `String` | Get groups whose CreatedBy contains a specified value |
| **CreationTimeBegin** | `Date` | Get groups whose CreationTime is equal to or greater than a specified value |
| **CreationTimeEnd** | `Date` | Get groups whose CreationTime is less than or equal to a specified value |
| **UpdatedTimeBegin** | `Date` | Get groups whose UpdatedTime is equal to or greater than a specified value |
| **UpdatedTimeEnd** | `Date` | Get groups whose UpdatedTime is less than or equal to a specified value |
| **Tag** | `Integer` | Get groups whose Tag matches a specified value |

Example request with multiple filters:

    api/GroupService/Groups?$top=10&skip=0&Description=Custom&TypeId=2000&GlobalStatus=1000

Example response:

```json

 {
   "value": {
     "@odata.context": "/api/$metadata#Collection(GroupService.Group)",
     "@odata.count": 1,
     "value": [
       {
         "@odata.type": "#GroupService.Group",
         "@odata.id": "/api/GroupService/Groups(1031)",
         "Id": 1031,
         "Name": "All Devices",
         "Description": "Top-level root group for all groups",
         "GlobalStatus": 4000,
         "ParentId": 0,
         "IdOwner": 30,
         "CreationTime": "2021-05-10 22:14:07.256975",
         "UpdatedTime": "2021-05-11 14:17:19.739114",
         "CreatedBy": "system",
         "UpdatedBy": null,
         "Visible": true,
         "DefinitionId": 1000,
         "DefinitionDescription": "Top-level group for all groups",
         "TypeId": 2000,
         "MembershipTypeId": 24,
         "HasAttributes": false,
         "IsAccessAllowed": true,
         "GroupQuery@odata.navigationLink": "/api/GroupService/Groups(1031)/GroupQuery",
         "DeviceStatusSummary@odata.navigationLink": "/api/GroupService/Groups(1031)/DeviceStatusSummary",
         "EventStatusSummary@odata.navigationLink": "/api/GroupService/Groups(1031)/EventStatusSummary",
         "EventSeveritySummary@odata.navigationLink": "/api/GroupService/Groups(1031)/EventSeveritySummary",
         "Devices@odata.navigationLink": "/api/GroupService/Groups(1031)/Devices",
         "AllLeafDevices@odata.navigationLink": "/api/GroupService/Groups(1031)/AllLeafDevices",
         "AllLeafDeviceSummaries@odata.navigationLink": "/api/GroupService/Groups(1031)/AllLeafDeviceSummaries",
         "GroupHierarchy@odata.navigationLink": "/api/GroupService/Groups(1031)/GroupHierarchy",
         "SubGroups@odata.navigationLink": "/api/GroupService/Groups(1031)/SubGroups",
         "GroupDevicesSummary@odata.navigationLink": "/api/GroupService/Groups(1031)/GroupDevicesSummary",
         "GroupTags@odata.navigationLink": "/api/GroupService/Groups(1031)/GroupTags",
         "GroupActions@odata.navigationLink": "/api/GroupService/Groups(1031)/GroupActions",
         "GroupAttributes@odata.navigationLink": "/api/GroupService/Groups(1031)/GroupAttributes"
       }]
   }
}
```
| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID of the group |
| **Name** | `String` | Name of the group |
| **Description** | `String` |	Description of the group |
| **GlobalStatus** | `Integer` | Indicates the health of the group |
| **ParentId** | `Integer` | The ID of the group's parent group (zero if a root group) |
| **IdOwner** | `Integer` | Indicates the program/plugin that created the group and if it was created as a static (built-in) group or generated by a user.  Examples: 30=OME static, 31=OME generated, 40=Power Manager Plugin static, 41=Power Manager Plugin generated |
| **CreationTime**  | `Date` | Date and time when the group was created |
| **UpdatedTime**| `Integer` |	Date and time when the group was last updated |
| **CreatedBy** | `Date`	| The name of the user that created the group |
| **UpdatedBy** | `String` | The name of the user that last updated the group |
| **Visible** |	`Boolean` | Whether the group should be displayed to users |
| **DefinitionId** | `Integer` | (Used by OME-M) Indicates the type of devices held in a group (e.g., 1000=Sleds, 2000=PowerEdge modular devices) |
| **DefinitionDescription** | `String` | (Used by OME-M) Description for DefinitionId |
| **TypeId** | `Integer` |	Group type (1000=Internal, 2000=Predefined, 3000=User-Defined, 4000=Discovery-Defined, 5000=MCM Group) |
| **MembershipTypeId** | `Integer` | Membership type (12=Static, 24=Dynamic, 36=Virtual Static for VMs) |
| **HasAttributes** | `Boolean` | Indicates whether the group has any attributes associated with it |
| **IsAccessAllowed** | `Boolean` | Indicates whether the group can be managed by the current user |


|  Link  |  Description  |
| --------------: | :---- |
| **GroupQuery** | Get the query conditions for a group |
| **DeviceStatusSummary** | Get status summary info for the devices in a group |
| **EventStatusSummary** | Get event status summary info for the devices in a group |
| **EventSeveritySummary** | Get event severity summary info for the devices in a group |
| **Devices** | Get info for the devices in a group (but not its subgroups) |
| **AllLeafDevices** | Get info for the devices in a group and its subgroups |
| **AllLeafDeviceSummaries** | Get summary info for the devices in a group and its subgroups |
| **GroupHierarchy** | Get group hierarchical relationships |
| **SubGroups** | Get info for the groups hierarchically below a specific group |
| **GroupDevicesSummary** | Get summary info for the devices in a group (but not its subgroups) |
| **GroupTags** | Get info for tags associated with a group |
| **GroupActions** | Get actions for the tags on a group |
| **GroupAttributes** | Get info for attributes associated with a group |



## Delete device groups
To delete groups, send a **POST** request to `/api/GroupService/Actions/GroupService.DeleteGroup`.

**RBAC Privilege**: GROUP_MANAGEMENT

 Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 


|  Filter  |  Data Type  |  Usage  |  Description  |
| --------------: | :-----------: | :-------: | :---- |
|**GroupIds** | `Integer array` | Required  | Must supply one or more IDs of existing groups to delete |

Example request:

```json
{
  "GroupIds" : [ 10084 ]
}
```

Example response:

    If the groups are successfully deleted, it returns 1.



