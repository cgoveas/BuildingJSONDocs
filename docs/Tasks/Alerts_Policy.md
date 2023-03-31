# Managing alert policies

The **AlertPolicies** service helps automatically trigger actions based on the input from an alert. You can send your alerts to email address, phone, and SNMP traps using `AlertPolicies`. `AlertPolicies` also perform device power control actions such as turning on or turning off a device when an alert of a predefined category is generated.


<!-- theme: info -->

## Get all alert policies
To display all alert policies, send a **GET** request to `/api/AlertService/AlertPolicies`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example response:

```json

 {
    "value": [
       {
            "@odata.type": "#AlertService.AlertPolicy",
            "@odata.id": "/api/AlertService/AlertPolicies(10006)",
            "Id": 10006,
            "Name": "Mobile Push Notification - Critical Alerts",
            "Description": "This policy is applicable to critical alerts. Associated actions will be taken when a critical alert is received.",
            "Enabled": true,
            "DefaultPolicy": true,
            "PolicyData": {
                "Catalogs": [],
                "Severities": [
                    16
                ],
                "Devices": [],
                "DeviceTypes": [],
                "Groups": [],
                "Schedule": {
                    "StartTime": null,
                    "EndTime": null,
                    "CronString": null,
                    "Interval": false
                },
                "Actions": [
                    {
                        "Id": 5,
                        "Name": "Mobile",
                        "ParameterDetails": [],
                        "TemplateId": 112
                    }
                ],
                "UndiscoveredTargets": []
            },
            "State": true,
            "Visible": true,
            "Owner": null,
            "PolicyActions@odata.navigationLink": "/api/AlertService/AlertPolicies(10006)/PolicyActions",
            "PolicyCriteria@odata.navigationLink": "/api/AlertService/AlertPolicies(10006)/PolicyCriteria"
        }
    ]
}
```

| Attribute name |        Type          |  Description |
| -------------: | :------------------: | :-----------  |
| **Id**| `Integer` |Event record ID. |
| **Name**| `String` | Name associated with the policy.|
| **Description** | `String` | Description associated with the policy.|
| **Enabled**| `Boolean` | Indicates if the policy is enabled or disabled.|
| **DefaultPolicy**| `Boolean` |Indicates if the policy is the default policy or pre-seeded. |
| **State**| `Boolean` | State associated with the policy.|
| **Visible**| `Boolean` | Indicate if the policy is visible or hidden.|
| **PolicyData** |  | |
| **CatalogName**| `String` |Name of the catalogs associated with the policy.|
| **Categories**| `List of integers` | Name of the categories associated with the policy.|
| **SubCategories**| `List of integers` | Name of the subcategories associated with the policy.|
| **Severities** | `List of integers` | List of severities associated to the policy.|
| **Devices**| `List of integers` | List of devices associated to the policy.|
| **DevicesTypes** | `List of integers` |List of devices types associated to the policy. |
| **Groups** |  `List of integers` |List of groups associated to the policy. |
| **Schedule**|  | |
| **StartTime**| `String` |Start event-time to verify. This value indicates the start date and time for policy validity. |
| **EndTime**| `String` |End event-time to verify. This value indicates the end date and time for policy validity. |
| **CronString**| `String` |CRON string associated to the policy. This field indicates a special CRON that can be applied to the policy to indicate values such as the days for which the policy is valid. |
| **Actions**|  | |
| **Id**| `Integer` |ID of action. |
| **Name** | `String` | Name associated with the action.|
| **ParameterDetails**|  | Extra parameters that can be used for the action.|
| **TemplateId** | `Integer` | ID of the template associated with the action.|
| **PolicyActions**| `List of strings` | Link to the list of actions that can be triggered for the policy|
| **PolicyCriteria** | `List of strings` |Link to the criteria for the evaluation of the alert contents with the policy contents. This evaluation determines if the policy is valid and if any actions are triggered. |

## Create an alert policy

To create an alert policy, send a **POST** request to `/api/AlertService/AlertPolicies`.

**Privilege**: MONITORING_SETUP

Response code | Description | 
---------|----------|
 **200** | Success | 

 
<!--
type: tab
title: Request
-->

```json
{
  "Name": "Sample test",
  "Enabled": true,
  "DefaultPolicy": false,
  "PolicyData": {
    "Catalogs": [
      {
        "CatalogName": "Application",
        "Categories": [
          0
        ],
        "SubCategories": [
          0
        ]
      }
    ],
    "Severities": [
      2
    ],
    "Devices": [
      10086,
      10087
    ],
    "DeviceTypes": [],
    "Groups": [],
    "UndiscoveredTargets": [],
    "Schedule": {
      "StartTime": "2021-05-11 05:00:00.019",
      "EndTime": "",
      "CronString": "* * * ? * sun *",
      "Interval": false
    },
    "Actions": [
      {
        "Name": "Ignore",
        "TemplateId": 100
      }
    ]
  },
  "State": true
}
```

<!--
type: tab
title: Response
-->

```json
{
    "Id": 10183,
    "Name": "Sample test 2 12",
    "Description": null,
    "Enabled": true,
    "DefaultPolicy": false,
    "Editable": true,
    "Visible": true,
    "PolicyData": {
        "Catalogs": [
            {
                "CatalogName": "Application",
                "Categories": [
                    0
                ],
                "SubCategories": [
                    0
                ]
            }
        ],
        "Severities": [
            2
        ],
        "Devices": [
            10086,
            10087
        ],
        "DeviceTypes": [
            1000
        ],
        "Groups": [],
        "Schedule": {
            "StartTime": "2021-05-11 05:00:00.019",
            "EndTime": "",
            "CronString": "* * * ? * sun *",
            "Interval": false
        },
        "Actions": [
            {
                "Id": 11,
                "Name": "Ignore",
                "ParameterDetails": [],
                "TemplateId": 100
            }
        ],
        "UndiscoveredTargets": []
    },
    "State": true,
    "Owner": 10067
}
```

<!-- type: tab-end -->


## Enable alert policies

To enable alert policies, send a **POST** request to `/api/AlertService/Actions/AlertService.EnableAlertPolicies`.

**Privilege**: MONITORING_SETUP

Response code | Description | 
---------|----------|
 **204** | Success | 

Example request:

```json
{
 "AlertPolicyIds": [100,200,300]
}

or

{
 "AllPolicies": true
}

```

## Disable alert policies

To disable alert policies, send a **POST** request to `/api/AlertService/Actions/AlertService.DisableAlertPolicies`.

**Privilege**: MONITORING_SETUP

Response code | Description | 
---------|----------|
 **204** | Success | 

 
Example request:

```json
{
 "AlertPolicyIds": [100,200,300]
}

or

{
 "AllPolicies": true
}

```

## Remove alert policies

To remove alert policies, send a **POST** request to `/api/AlertService/Actions/AlertService.RemoveAlertPolicies`.

**Privilege**: MONITORING_SETUP

Response code | Description | 
---------|----------|
 **204** | Success | 

Example request:

```json
{
 "AlertPolicyIds": [100,200,300]
}

or

{
 "AllPolicies": true
}

```

## Update an alert policy

To update an alert policy, send a **PUT** request to `/api/AlertService/AlertPolicies(Id)`.

**Privilege**: MONITORING_SETUP
 
Response code | Description | 
---------|----------|
 **200** | Success | 
 

<!--
type: tab
title: Request
-->

```json
{
  "Id": 10183,
  "Name": "Sample test Rename",
  "Enabled": true,
  "DefaultPolicy": false,
  "PolicyData": {
    "Catalogs": [
      {
        "CatalogName": "Application",
        "Categories": [
          0
        ],
        "SubCategories": [
          0
        ]
      }
    ],
    "Severities": [
      2
    ],
    "Devices": [
      10086,
      10087
    ],
    "DeviceTypes": [],
    "Groups": [],
    "UndiscoveredTargets": [],
    "Schedule": {
      "StartTime": "2021-05-11 05:00:00.019",
      "EndTime": "",
      "CronString": "* * * ? * sun *",
      "Interval": false
    },
    "Actions": [
      {
        "Name": "Ignore",
        "TemplateId": 100
      }
    ]
  },
  "State": true
}
```

<!--
type: tab
title: Response
-->

```json
{
    "Id": 10183,
    "Name": "Sample test",
    "Description": null,
    "Enabled": true,
    "DefaultPolicy": false,
    "Editable": true,
    "Visible": true,
    "PolicyData": {
        "Catalogs": [
            {
                "CatalogName": "Application",
                "Categories": [
                    0
                ],
                "SubCategories": [
                    0
                ]
            }
        ],
        "Severities": [
            2
        ],
        "Devices": [
            10086,
            10087
        ],
        "DeviceTypes": [
            1000
        ],
        "Groups": [],
        "Schedule": {
            "StartTime": "2021-05-11 05:00:00.019",
            "EndTime": "",
            "CronString": "* * * ? * sun *",
            "Interval": false
        },
        "Actions": [
            {
                "Id": 9,
                "Name": "Ignore",
                "ParameterDetails": [],
                "TemplateId": 100
            }
        ],
        "UndiscoveredTargets": []
    },
    "State": true,
    "Owner": 10067
}
```
<!-- type: tab-end -->

## Get the policies of an alert

To display the policies of a specific alert, send a **GET** request to `/api/AlertService/AlertPolicies(Id)`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 

Example response:

```json

 {
    "@odata.context": "/api/$metadata#AlertService.AlertPolicy",
    "@odata.type": "#AlertService.AlertPolicy",
    "@odata.id": "/api/AlertService/AlertPolicies(10184)",
    "Id": 10184,
    "Name": "Sample test 123",
    "Description": null,
    "Enabled": true,
    "DefaultPolicy": false,
    "PolicyData": {
        "Catalogs": [
            {
                "CatalogName": "Application",
                "Categories": [
                    0
                ],
                "SubCategories": [
                    0
                ]
            }
        ],
        "Severities": [
            2
        ],
        "Devices": [
            10086,
            10087
        ],
        "DeviceTypes": [
            1000
        ],
        "Groups": [],
        "Schedule": {
            "StartTime": "2021-05-11 05:00:00.019",
            "EndTime": "",
            "CronString": "* * * ? * sun *",
            "Interval": false
        },
        "Actions": [
            {
                "Id": 12,
                "Name": "Ignore",
                "ParameterDetails": [],
                "TemplateId": 100
            }
        ],
        "UndiscoveredTargets": []
    },
    "State": true,
    "Visible": true,
    "Owner": 10067,
    "PolicyActions@odata.navigationLink": "/api/AlertService/AlertPolicies(10184)/PolicyActions",
    "PolicyCriteria@odata.navigationLink": "/api/AlertService/AlertPolicies(10184)/PolicyCriteria"
}

```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id**| `Integer` |Event record ID. |
| **Name**| `String` | Name associated with the policy.|
| **Description** | `String` | Description associated with the policy.|
| **Enabled**| `Boolean` | Indicates if the policy is enabled or disabled.|
| **DefaultPolicy**| `Boolean` |Indicates if the policy is the default policy or pre-seeded. |
| **State**| `Boolean` | State associated with the policy.|
| **Visible**| `Boolean` | Indicate if the policy is visible or hidden.|
| **PolicyData** |  | |
| **CatalogName**| `String` | Name of the catalogs associated with the policy.|
| **Categories**| `List of integers` | Name of the categories associated with the policy.|
| **SubCategories**| `List of integers` | Name of the subcategories associated with the policy.|
| **Severities** | `List of integers` | List of severities associated to the policy.|
| **Devices**| `List of integers` | List of devices associated to the policy.|
| **DevicesTypes** | `List of integers` |List of devices types associated to the policy. |
| **Groups** |  `List of integers` |List of groups associated to the policy. |
| **Schedule**|  | |
| **StartTime**| `String` |Start event-time to verify. This value indicates the start date and time for policy validity. |
| **EndTime**| `String` |End event-time to verify. This value indicates the end date and time for policy validity. |
| **CronString**| `String` |CRON string associated to the policy. This field indicates a special CRON that can be applied to the policy to indicate values such as the days for which the policy is valid. |
| **Actions**|  | |
| **Id**| `Integer` |ID of the action. |
| **Name** | `String` | Name associated with the action.|
| **ParameterDetails**|  | Extra parameters that can be used for the action.|
| **TemplateId** | `Integer` | ID of the template associated with the action.|
| **PolicyActions**|  `String` | Link to the list of actions that can be triggered for the policy.|
| **PolicyCriteria** |  `String` |Link to the criteria for the evaluation of the alert contents with the policy contents. This evaluation determines if the policy is valid and if any actions are triggered. |



## Get all alert policy criteria
This URI represents the criteria used for matching the policy with incoming events. The criteria determine if the policy can trigger any actions.

To display all alert policies, send a **GET** request to `/api/AlertService/AlertPolicies(Id)/PolicyCriteria`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 

Example response:

```json
{
    "@odata.context": "/api/$metadata#Collection(AlertService.PolicyCriterion)",
    "@odata.count": 1,
    "value": [
        {
            "@odata.type": "#AlertService.PolicyCriterion",
            "@odata.id": "/api/AlertService/AlertPolicies(10184)/PolicyCriteria(16)",
            "Id": 16,
            "PolicyId": 10184,
            "Parameter": "",
            "Value": "(((catalogName == 'Application' )) AND (severityType==2) AND (#inSchedule('2021-05-11 05:00:00.019','','* * * ? * sun *','false')))",
            "Type": 3,
            "TypeName": "EXPRESSION",
            "ComparisonType": 3,
            "ComparisonTypeName": "Equal"
        }
    ]
}

```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id**| `Integer` |ID of the criteria. |
| **PolicyId** |`Integer`  |ID of policy with which the criteria is associated. |
| **Parameter** | `String` |Any extra parameter that can be used in the criteria. |
| **Value**| `String` | Expression that is used during policy evaluation with events.|
| **Type** | `Integer` | ID of the criteria type.|
| **TypeName** |`String`  | Name of the criteria type.|
| **ComparisonType** | `Integer` |ID of comparison type. |
| **ComparisonTypeName** | `String`| Name of comparison type.|



## Get the policy criteria of a specific alert

To display the policy crietria of a specific alert, send a **GET** request to `/api/AlertService/AlertPolicies(Id)/PolicyCriteria(Id)`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 

Example response:

```json
{
    "@odata.context": "/api/$metadata#AlertService.PolicyCriterion",
    "@odata.type": "#AlertService.PolicyCriterion",
    "@odata.id": "/api/AlertService/AlertPolicies(10184)/PolicyCriteria(16)",
    "Id": 16,
    "PolicyId": 10184,
    "Parameter": "",
    "Value": "(((catalogName == 'Application' )) AND (severityType==2) AND (#inSchedule('2021-05-11 05:00:00.019','','* * * ? * sun *','false')))",
    "Type": 3,
    "TypeName": "EXPRESSION",
    "ComparisonType": 3,
    "ComparisonTypeName": "Equal"
}
```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id**| `Integer` |ID of the criteria. |
| **PolicyId** |`Integer`  |ID of policy with which the criteria is associated. |
| **Parameter** | `String` |Any extra parameter that can be used in the criteria. |
| **Value**| `String` | Expression that is used during policy evaluation with events.|
| **Type** | `Integer` | ID of the criteria type.|
| **TypeName** |`String`  | Name of the criteria type.|
| **ComparisonType** | `Integer` |ID of comparison type. |
| **ComparisonTypeName** |  `String`| Name of comparison type.|


## Get all alert policy actions
> Returns the actions that a policy can trigger.

To display all alert policy actions, send a **GET** request to `/api/AlertService/AlertPolicies(Id)/PolicyActions`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 

Example response:

```json
{
    "@odata.context": "/api/$metadata#Collection(AlertService.PolicyAction)",
    "@odata.count": 1,
    "value": [
        {
            "@odata.type": "#AlertService.PolicyAction",
            "@odata.id": "/api/AlertService/AlertPolicies(10184)/PolicyActions(12)",
            "Id": 12,
            "PolicyId": 10184,
            "Name": "Ignore",
            "Parameters": "",
            "TemplateId": 100,
            "Template": {
                "@odata.type": "#AlertService.AlertActionTemplate",
                "Id": 100,
                "Name": "Ignore",
                "Description": "Ignore",
                "Disabled": false,
                "ParameterDetails": []
            }
        }
    ]
}

```


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id**| `Integer` |ID of the criteria. |
| **PolicyId** |`Integer`  |ID of the policy with which the action is associated. |
| **Name**  |`String`   |Name of the action. |
| **Parameter** | `String` |Extra parameter that can be added to the action.|
| **TemplateId**| `Integer` | ID of the action template associated with the action.|
| **Template** |  | |
| **Id** | `Integer` | ID of the template.|
| **Name** |`String`  | Name of the template.|
| **Description** | `String` |Additional description of the template. |
| **Disabled** |  `Boolean`|Indicates if template is enabled or disabled.|
| **ParameterDetails** | `List of strings` |List of other parameter that the template can carry and apply. |


## Get an alert's policy actions
To display the actions that a policy can trigger for a specific alert, send a **GET** request to `/api/AlertService/AlertPolicies(Id)/PolicyActions(Id)`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 

Example response:

```json
{
    "@odata.context": "/api/$metadata#AlertService.PolicyAction",
    "@odata.type": "#AlertService.PolicyAction",
    "@odata.id": "/api/AlertService/AlertPolicies(10184)/PolicyActions(12)",
    "Id": 12,
    "PolicyId": 10184,
    "Name": "Ignore",
    "Parameters": "",
    "TemplateId": 100,
    "Template": {
        "@odata.type": "#AlertService.AlertActionTemplate",
        "Id": 100,
        "Name": "Ignore",
        "Description": "Ignore",
        "Disabled": false,
        "ParameterDetails": []
    }
}
```


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id**| `Integer` |ID of the criteria. |
| **PolicyId** |`Integer`  |ID of the policy with which the action is associated. |
| **Name**  |`String`    |Name of the action. |
| **Parameter** | `String`  |Extra parameter that can be added to the action.|
| **TemplateId**| `Integer` | ID of the action template associated with the action.|
| **Template** |  | |
| **Id** | `Integer` | ID of the template.|
| **Name** |`String`  | Name of the template.|
| **Description** | `String`  |Additional description of the template. |
| **Disabled** | `Boolean`|Indicates if template is enabled or disabled.|
| **ParameterDetails** | `List of strings` |List of other parameter that the template can carry and apply. |






