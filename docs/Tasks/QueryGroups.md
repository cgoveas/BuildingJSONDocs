# Managing dynamic device groups
The **Groups** service describes the APIs used to manage both static and dynamic device groups.  This section descibes the APIs that apply only to **query** (also called *dynamic*) **groups**. A query group is a group whose membership is dynamic.  Membership is determined by a *query* that is executed by OpenManage Enterprise periodically.  The query for a group is specified via the **Create Query Group** API.

In OpenManage Enterprise, query groups can be created and managed by users on the **All Devices** screen (right-click on **Query Groups**).  Query groups are also created for reports via the **Reports** screen. Creating a query group is a fairly complex task and requires that various other APIs be called before it is possible to create a query group.  These
APIs use IDs to represent the different tables and fields that can be used in a query.  Most of the APIs discussed below are used to get the set of IDs to use for the tables and fields.  

The general workflow for creating a query group is:
1. **GET** operator metadata from `/api/QuerySupportService/OperatorInfo`.
2. **GET** context info from `/api/QuerySupportService/QueryContexts`.
3. **GET** details for a specific query context from `/api/QuerySupportService/QueryContexts`.
4. **POST** to `/api/GroupService/Actions/GroupService.CreateGroup` to create a query group.

<!-- theme: info -->

## Get operator info
To get IDs for operators and field types that may be used in the query for a Query Group, send a **GET** request to `/api/QuerySupportService/OperatorInfo`.

**RBAC Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 


Example response:

```json

{
  "@odata.context": "/api/$metadata#QuerySupportService.OperatorInfo",
  "@odata.type": "#QuerySupportService.OperatorInfo",
  "@odata.id": "/api/QuerySupportService/OperatorInfo",
  "Operators": [
    {
      "Id": 1,
      "Name": "="
    },
    {
      "Id": 2,
      "Name": "!="
    },
    {
      "Id": 3,
      "Name": "<"
    },
    {
      "Id": 4,
      "Name": "<="
    },
    {
      "Id": 5,
      "Name": ">"
    },
    {
      "Id": 6,
      "Name": ">="
    },
    {
      "Id": 7,
      "Name": "contains"
    },
    {
      "Id": 8,
      "Name": "does not contain"
    },
    {
      "Id": 9,
      "Name": "is null"
    },
    {
      "Id": 10,
      "Name": "is not null"
    },
    {
      "Id": 11,
      "Name": "begins with"
    },
    {
      "Id": 12,
      "Name": "ends with"
    },
    {
      "Id": 13,
      "Name": "before"
    },
    {
      "Id": 14,
      "Name": "after"
    }
  ],
  "FieldTypes": [
    {
      "FieldTypeId": 1,
      "Name": "String",
      "OperatorIds": [
        1,
        2,
        3,
        4,
        5,
        6,
        11,
        12,
        7,
        8,
        9,
        10
      ]
    },
    {
      "FieldTypeId": 2,
      "Name": "Number",
      "OperatorIds": [
        1,
        2,
        3,
        4,
        5,
        6,
        9,
        10
      ]
    },
    {
      "FieldTypeId": 3,
      "Name": "Date-Time",
      "OperatorIds": [
        1,
        2,
        13,
        14,
        9,
        10
      ]
    },
    {
      "FieldTypeId": 4,
      "Name": "Enum",
      "OperatorIds": [
        1,
        2,
        9,
        10
      ]
    },
    {
      "FieldTypeId": 5,
      "Name": "Boolean",
      "OperatorIds": [
        1,
        2,
        9,
        10
      ]
    }
  ]
}
```


### The Operators object

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID for the operator |
| **Name** | `String` | Display text for the operator name |

### The FieldTypes object

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **FieldTypeId** | `Integer` | ID for the field type |
| **Name** | `String` | String name for this field type |
| **OperatorIds** | `Integer array` | The set of operators which can be used with this type of field |


## Get all query contexts
A query context is essentially a workspace for query development. It identifies a set of tables and fields 
which may be used to create the query for a query group.  Different query contexts are provided
for queries involving different related sets of tables (such as for developing device-related or job-related
 queries).  The **All Devices** screen deals only with the **Devices** query context.  When creating a
report, a desired query context can be selected.

To get general information for all query contexts, send a **GET** request to `/api/QuerySupportService/QueryContexts`.

**RBAC Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 

Example response:

```json
{
  "@odata.context": "/api/$metadata#Collection(QuerySupportService.QueryContext)",
  "@odata.count": 5,
  "value": [
    {
      "@odata.type": "#QuerySupportService.QueryContext",
      "@odata.id": "/api/QuerySupportService/QueryContexts(4)",
      "Id": 4,
      "Name": "Jobs",
      "Description": null,
      "BaseCategoryId": 3,
      "Tables": [],
      "Fields": [],
      "ReportSettings": []
    },
    {
      "@odata.type": "#QuerySupportService.QueryContext",
      "@odata.id": "/api/QuerySupportService/QueryContexts(3)",
      "Id": 3,
      "Name": "Groups",
      "Description": null,
      "BaseCategoryId": 4,
      "Tables": [],
      "Fields": [],
      "ReportSettings": []
    },
    {
      "@odata.type": "#QuerySupportService.QueryContext",
      "@odata.id": "/api/QuerySupportService/QueryContexts(1)",
      "Id": 1,
      "Name": "Alerts",
      "Description": null,
      "BaseCategoryId": 2,
      "Tables": [],
      "Fields": [],
      "ReportSettings": []
    },
    {
      "@odata.type": "#QuerySupportService.QueryContext",
      "@odata.id": "/api/QuerySupportService/QueryContexts(2)",
      "Id": 2,
      "Name": "Devices",
      "Description": null,
      "BaseCategoryId": 1,
      "Tables": [],
      "Fields": [],
      "ReportSettings": []
    },
    {
      "@odata.type": "#QuerySupportService.QueryContext",
      "@odata.id": "/api/QuerySupportService/QueryContexts(5)",
      "Id": 5,
      "Name": "Servers",
      "Description": null,
      "BaseCategoryId": 1,
      "Tables": [],
      "Fields": [],
      "ReportSettings": []
    }
  ]
}

```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID for the query context |
| **Name** | `String` | The query context name |
| **Description** | `String` | (Not used for this API) |
| **BaseCategoryId** | `Integer` | ID of the base category for the query context (1=Devices, 2=Alerts, 3=Jobs, 4=Groups) |
| **Tables** | `Integer array` | (Not used for this API) |
| **Fields** | `Integer array` | (Not used for this API) |
| **ReportSettings** | `Integer array` | (Not used for this API) |

## Get a query context
To get table and field info for a specific query context, send a **GET** request to `/api/QuerySupportService/QueryContexts(id)`.

**RBAC Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 

Example response: 
> The response payload contains table and field details for the requested query context.

```json
{
  "@odata.context": "/api/$metadata#QuerySupportService.QueryContext",
  "@odata.type": "#QuerySupportService.QueryContext",
  "@odata.id": "/api/QuerySupportService/QueryContexts(2)",
  "Id": 2,
  "Name": "Devices",
  "Description": null,
  "BaseCategoryId": 1,
  "Tables": [
    {
      "Id": 14,
      "Name": "Device General Info",
      "Description": null,
      "ParentTableId": -1
    },
    {
      "Id": 15,
      "Name": "Device Sub-Types",
      "Description": null,
      "ParentTableId": 14
    }
  ],
  "Fields": [
    {
      "Id": 51,
      "Name": "Device Name",
      "TableId": 14,
      "Description": null,
      "FieldTypeId": 1,
      "StrMaxLen": 256,
      "EnumOpts": []
    },
    {
      "Id": 52,
      "Name": "Device Global Status",
      "TableId": 14,
      "Description": null,
      "FieldTypeId": 4,
      "StrMaxLen": 0,
      "EnumOpts": [
        {
          "Id": 1000,
          "Name": "Normal"
        },
        {
          "Id": 2000,
          "Name": "Unknown"
        },
        {
          "Id": 3000,
          "Name": "Warning"
        },
        {
          "Id": 4000,
          "Name": "Critical"
        },
        {
          "Id": 5000,
          "Name": "No Status"
        }
      ]
    }
  ],
  "ReportSettings": [
    {
      "Id": 1,
      "Name": "Report Duration",
      "Description": "Time Range to be used for all time-specific report fields.",
      "FieldTypeId": 4,
      "StrMaxLen": 0,
      "EnumOpts": [
        {
          "Id": 1,
          "Name": "1 Day"
        },
        {
          "Id": 7,
          "Name": "7 Days"
        },
        {
          "Id": 15,
          "Name": "15 Days"
        },
        {
          "Id": 30,
          "Name": "1 Month"
        },
        {
          "Id": 90,
          "Name": "3 Months"
        },
        {
          "Id": 180,
          "Name": "6 Months"
        },
        {
          "Id": 365,
          "Name": "1 Year"
        }
      ],
      "DefaultValue": 90,
      "Required": true,
      "FieldIds": [
        2,
        3,
        4,
        5,
        6,
        7,
        8,
        9
      ]
    },
    {
      "Id": 2,
      "Name": "Aggregation Period",
      "Description": "Time Interval to be used for all time-specific report fields.",
      "FieldTypeId": 4,
      "StrMaxLen": 0,
      "EnumOpts": [
        {
          "Id": 1,
          "Name": "Hourly"
        },
        {
          "Id": 2,
          "Name": "Daily"
        }
      ],
      "DefaultValue": 2,
      "Required": true,
      "FieldIds": [
        2,
        3,
        4,
        5,
        6,
        7,
        8,
        9
      ]
    }
  ]
}
```

This API has the same response payload as the **GetAllQueryContexts** API.
The only difference is that the **Tables** and **Fields** arrays are populated.

### The Tables object

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID for the table |
| **Name** | `String` | Displayable table name |
| **Description** | `String` | (Not used for this API) |
| **ParentTableId** | `Integer` | ID of the 'parent' table of this table (-1 if not applicable) |

### The Fields object

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID for the Field |
| **Name** | `String` | Displayable field name |
| **TableId** | `Integer` | ID of the table that this field is in |
| **Description** | `String` | (Not used for this API) |
| **FieldTypeId** | `Integer` | ID for field type for this field |
| **StrMaxLen** | `Integer` | Maximum length for this field (applicable only to text fields) |
| **EnumOpts** | `Integer array` | Set of option IDs for this field (applicable only when field value must be in a specific set) |

### The ReportSettings object

Report settings apply only to reports and are only used by some plugins.
They are values which apply to all report fields.
In most cases, this element is an empty array.

For example, the Power Management plugin supports different possible interval
durations and aggregation periods. If the Power Management plugin is installed, 
reports that use the "Power Manager Devices" context and have columns in the
**Device Power and Thermal Metrics** group will display the **Report Duration** and **Aggregation Period** settings. The values selected for those settings will be used when the report is run (and apply to all fields in the report, where applicable).


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID for the report setting |
| **Name** | `String` | Displayable name for the report setting |
| **Description** | `String` | Displayable description text for the report setting |
| **FieldTypeId** | `Integer` | ID of the field type for this setting |
| **StrMaxLen** | `Integer` | Maximum length for this report setting (applicable only to text fields) |
| **EnumOpts** | `Integer array` | Set of option IDs for this report setting (applicable only when field value must be in a specific set); each option has an `Id` field and a `Name` field |


## Create a query group
This API uses the IDs and info obtained through the previous API calls to create a query group. To create a query group, make a **POST** request to `/api/GroupService/Actions/GroupService.CreateGroup`.

**RBAC Privilege**: GROUP_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 


Example request:

```json
{
  "GroupModel" : {
    "Name" : "My Dynamic Test Group",
    "Description" : "This is a dynamic test group.",
    "MembershipTypeId" : 24,
    "ParentId" : 1022
  },
  "GroupModelExtension" : {
    "ContextId" : 2,
    "Conditions": [
      {
        "LogicalOperatorId": 1,
        "LeftParen": false,
        "FieldId": 56,
        "OperatorId": 1,
        "Value": "1000",
        "RightParen": false
      },
      {
        "LogicalOperatorId": 1,
        "LeftParen": false,
        "FieldId": 55,
        "OperatorId": 7,
        "Value": "poweredge",
        "RightParen": false
      }
    ]
  }
}
```

### The GroupModel object

The GroupModel object provides group-specific values.

|  Filter  |  Data Type  |  Usage  |  Description |
| -------: | :---------: | :-------: | :---- |
| **Name** | `String` | Required | Group name |
| **Description** | `String` | Optional | Description text for the group |
| **MembershipTypeId** | `Integer` | Required | Membership type for the group (24=Query Group) |
| **ParentId** | `Integer` | Required | ID for this group's parent group (must be 1022 for "Query Groups") |

### The GroupModelExtension object

The **GroupModelExtension** object provides details for the query for the group.  A query may specify zero or more conditions.  If no conditions are specified, the query gets all elements for the specified query context (e.g., all devices).

|  Filter  |  Data Type  |  Usage  |  Description |
| -------: | :---------: | :-------: | :---- |
| **ContextId** | `Integer` | Required | The Context ID for the query |
| **LogicalOperatorId** | `Integer` | Required | The operator to use between this condition and the prior condition; ignored for first condition (1=AND, 2=OR) |
| **LeftParen** | `Boolean` | Required | Whether to include a left parentheses before this condition; can be used to group multiple conditions to form more complex queries |
| **FieldId** | `Integer` | Required | ID for the field for this condition |
| **OperatorId** | `Integer` | Required | ID for the operator for this condition |
| **Value** | `String` | Required | The value for this condition |
| **RightParen** | `Boolean` | Required | Whether to include a right parentheses after this condition |

## Verify query group creation

If the query group is successfully created, it returns the new group's `ID`.



