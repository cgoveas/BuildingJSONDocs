# Managing alerts 
The **Alerts** service displays the list of alerts for events occurring in the devices. Alerts can be customized using filters.

> By default, only unacknowledged alerts are displayed. 

## Get all alerts
To display all alerts, send a **GET** request to `/api/AlertService/Alerts`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

 
Example response:

<!-- 
type: tab
title: JSON
-->
```json

 {
    "value": [
        {
            "@odata.type": "#AlertService.Alert",
            "@odata.id": "/api/AlertService/Alerts(15473)",
            "Id": 15473,
            "SeverityType": 2,
            "SeverityName": "Info",
            "AlertEntityId": 10161,
            "AlertEntityName": "10.35.0.232",
            "AlertEntityType": "Device",
            "AlertDeviceId": 10161,
            "AlertDeviceName": "10.35.0.232",
            "AlertDeviceType": 1000,
            "AlertDeviceIpAddress": "10.35.0.232",
            "AlertDeviceMacAddress": "5c:f9:dd:fc:a6:f4",
            "AlertDeviceIdentifier": "1234XYZ",
            "AlertDeviceAssetTag": "",
            "DefinitionId": 400022,
            "CatalogName": "Application",
            "CategoryId": 1,
            "CategoryName": "System Health",
            "SubCategoryId": 90,
            "SubCategoryName": "Devices",
            "StatusType": 2000,
            "StatusName": "Not-Acknowledged",
            "TimeStamp": "2021-05-03 13:01:57.134",
            "Message": "Device is online. ",
            "EemiMessage": "The device health status has changed.",
            "RecommendedAction": "Check the device subsystems for components that require immediate attention. For information about device health statuses, see the Online Help by clicking the help icon. Also see the User's Guide available on the support site.",
            "AlertMessageId": "CDEV6129",
            "AlertVarBindDetails": "",
            "AlertMessageType": "Internal",
            "MessageArgs": "",
            "AlertDeviceGroup": 0,
            "UserName": ""
        }
    ]
}
```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | Filters by event ID. Default value is 0|
|**SeverityType** | `Integer` | Filters by severity type. Default value is 0 |
|**SeverityName** | `String` |	Severity Name 1 - Unknown, 2 - Info, 4 - Normal, 8 - Warning, 16 - Critical|
|**AlertEntityId** | `Integer` | The base entity ID. |
|**AlertEntityName** | `String` | The base entity name. |
|**AlertEntityType** | `String` | The base entity type. |
|**AlertDeviceName**  | `String` | The device name.|
|**AlertDeviceType**| `Integer` |	The device type. |
|**AlertDeviceIpAddress** | `String`	| The device IP address.|
|**AlertDeviceMacAddress** | `String` | The device MAC address.|
|**AlertDeviceIdentifier** |	`String` | The device identifier. |
|**AlertDeviceAssetTag**| `String` | The device asset tag. |
|**DefinitionId** | `Integer` | The ID of the definition associated with the alert. |
|**CatalogName** | `String` |	The catalog name associated with the alert. |
|**CategoryId** | `Integer` |	The category ID associated with the alert.|
|**CategoryName** | `String` | The category name associated with the alert.|
|**SubCategoryId** | `Integer` | The sub-category ID associated with the alert.|
|**SubCategoryName** | `String` |	The sub-category name associated with the alert.|
|**StatusType** | `Integer` |	The ID of the status type. |
|**StatusName** | `String` | The name of status type.|
|**Message** | `String` |	The message describing the issue.|
|**EemiMessage** | `String` |The EemiMessage describing the issue.|
|**RecommendedAction** | `String` |	The recommended action associated with the alert.|
|**AlertMessageId** | `String` |	The alert message ID.|
|**AlertVarBindDetails** | `String` |	The list of variable bindings of the alert, if applicable.|
|**AlertMessageType** | `String` | The type of message�Internal, SNMP, Redfish, and so on.|
|**AlertDeviceGroup** | `Integer` | The device group ID associated with device generating the alert.|
|**UserName** | `String` | The name of the user.|


## Get all acknowledged alerts
To get all aknowledged alerts by using a filter, send a **GET** request to `/api/AlertService/Alerts?$filter=StatusType eq 1000`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 


Example response:


<!-- 
type: tab
title: JSON
-->
```json
 {
    "value": [
        {
            "@odata.type": "#AlertService.Alert",
            "@odata.id": "/api/AlertService/Alerts(15473)",
            "Id": 15473,
            "SeverityType": 2,
            "SeverityName": "Info",
            "AlertEntityId": 10161,
            "AlertEntityName": "10.35.0.232",
            "AlertEntityType": "Device",
            "AlertDeviceId": 10161,
            "AlertDeviceName": "10.35.0.232",
            "AlertDeviceType": 1000,
            "AlertDeviceIpAddress": "10.35.0.232",
            "AlertDeviceMacAddress": "5c:f9:dd:fc:a6:f4",
            "AlertDeviceIdentifier": "1234XYZ",
            "AlertDeviceAssetTag": "",
            "DefinitionId": 400022,
            "CatalogName": "Application",
            "CategoryId": 1,
            "CategoryName": "System Health",
            "SubCategoryId": 90,
            "SubCategoryName": "Devices",
            "StatusType": 1000,
            "StatusName": "Acknowledged",
            "TimeStamp": "2021-05-03 13:01:57.134",
            "Message": "Device is online. ",
            "EemiMessage": "The device health status has changed.",
            "RecommendedAction": "Check the device subsystems for components that require immediate attention. For information about device health statuses, see the Online Help by clicking the help icon. Also see the User's Guide available on the support site.",
            "AlertMessageId": "CDEV6129",
            "AlertVarBindDetails": "",
            "AlertMessageType": "Internal",
            "MessageArgs": "",
            "AlertDeviceGroup": 0,
            "UserName": ""
        }
    ]
}
```
<!-- type: tab-end -->


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | Filter by event ID. Default value is 0|
|**SeverityType** | `Integer` | Filter by severity type. Default value is 0 |
|**SeverityName** | `String` |	Severity Name 1 - Unknown, 2 - Info, 4 - Normal, 8 - Warning, 16 - Critical|
|**AlertEntityId** | `Integer` | The base entity ID. |
|**AlertEntityName** | `String` |Base entity name. |
|**AlertEntityType** | `String` | Base entity type. |
|**AlertDeviceName**  | `String` |Device Name.|
|**AlertDeviceType**| `Integer` |	Device Type. |
|**AlertDeviceIpAddress** | `String`	| Device ip address.|
|**AlertDeviceMacAddress** | `String` | Devie mac address.|
|**AlertDeviceIdentifier** |	`String` | Device identifier. |
|**AlertDeviceAssetTag**| `String` | Device asset tag. |
|**DefinitionId** | `Integer` |ID of the definition associated to the alert. |
|**CatalogName** | `String` |	Catalog name associated to alert. |
|**CategoryId** | `Integer` |	Category ID associated to alert.|
|**CategoryName** | `String` |	Category name associated to alert.|
|**SubCategoryId** | `Integer` |Sub category ID associated to alert.|
|**SubCategoryName** | `String` |	Sub category name associated to alert.|
|**StatusType** | `Integer` |	ID of status type. |
|**StatusName** | `String` |Name of status type.|
|**Message** | `String` |	Message describing the issue.|
|**EemiMessage** | `String` |EemiMessage describing the issue.|
|**RecommendedAction** | `String` |	Recommended action associcated with alert.|
|**AlertMessageId** | `String` |	Alert message ID.|
|**AlertVarBindDetails** | `String` |	List of variable bindings of the alert, if applicable.|
|**AlertMessageType** | `String` |Type of message�Internal, SNMP, Redfish, and so on.|
|**AlertDeviceGroup** | `Integer` |Device group ID associated with device generating the alert.|
|**UserName** | `String` ||


## Get alerts by ID
To get alerts by their ID, send a **GET** request to `/api/AlertService/Alerts(Id)`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example response:

<!-- 
type: tab
title: JSON
-->
```json

   {
            "@odata.type": "#AlertService.Alert",
            "@odata.id": "/api/AlertService/Alerts(15473)",
            "Id": 15473,
            "SeverityType": 2,
            "SeverityName": "Info",
            "AlertEntityId": 10161,
            "AlertEntityName": "10.35.0.232",
            "AlertEntityType": "Device",
            "AlertDeviceId": 10161,
            "AlertDeviceName": "10.35.0.232",
            "AlertDeviceType": 1000,
            "AlertDeviceIpAddress": "10.35.0.232",
            "AlertDeviceMacAddress": "5c:f9:dd:fc:a6:f4",
            "AlertDeviceIdentifier": "1234XYZ",
            "AlertDeviceAssetTag": "",
            "DefinitionId": 400022,
            "CatalogName": "Application",
            "CategoryId": 1,
            "CategoryName": "System Health",
            "SubCategoryId": 90,
            "SubCategoryName": "Devices",
            "StatusType": 1000,
            "StatusName": "Acknowledged",
            "TimeStamp": "2021-05-03 13:01:57.134",
            "Message": "Device is online. ",
            "EemiMessage": "The device health status has changed.",
            "RecommendedAction": "Check the device subsystems for components that require immediate attention. For information about device health statuses, see the Online Help by clicking the help icon. Also see the User's Guide available on the support site.",
            "AlertMessageId": "CDEV6129",
            "AlertVarBindDetails": "",
            "AlertMessageType": "Internal",
            "MessageArgs": "",
            "AlertDeviceGroup": 0,
            "UserName": ""
        }
```
<!-- type: tab-end -->


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | Filter by event ID. Default value is 0|
|**SeverityType** | `Integer` | Filter by severity type. Default value is 0 |
|**SeverityName** | `String` |	Severity Name 1 - Unknown, 2 - Info, 4 - Normal, 8 - Warning, 16 - Critical|
|**AlertEntityId** | `Integer` | The base entity ID. |
|**AlertEntityName** | `String` | The base entity name. |
|**AlertEntityType** | `String` | The base entity type. |
|**AlertDeviceName**  | `String` | The device name.|
|**AlertDeviceType**| `Integer` |	The device type. |
|**AlertDeviceIpAddress** | `String`	| The device IP address.|
|**AlertDeviceMacAddress** | `String` | The device MAC address.|
|**AlertDeviceIdentifier** |	`String` | The device identifier. |
|**AlertDeviceAssetTag**| `String` | The device asset tag. |
|**DefinitionId** | `Integer` | The ID of the definition associated with the alert. |
|**CatalogName** | `String` |	The catalog name associated with the alert. |
|**CategoryId** | `Integer` |	The category ID associated with the alert.|
|**CategoryName** | `String` | The category name associated with the alert.|
|**SubCategoryId** | `Integer` | The sub-category ID associated with the alert.|
|**SubCategoryName** | `String` |	The sub-category name associated with the alert.|
|**StatusType** | `Integer` |	The ID of the status type. |
|**StatusName** | `String` | The name of the status type.|
|**Message** | `String` |	The message describing the issue.|
|**EemiMessage** | `String` | The EemiMessage describing the issue.|
|**RecommendedAction** | `String` |	The recommended action associated with the alert.|
|**AlertMessageId** | `String` |	The alert message ID.|
|**AlertVarBindDetails** | `String` |	The list of variable bindings of the alert, if applicable.|
|**AlertMessageType** | `String` | The type of message�Internal, SNMP, Redfish, and so on.|
|**AlertDeviceGroup** | `Integer` | The device group ID associated with device generating the alert.|
|**UserName** | `String` | The name of the user.|


## Get all alert categories
To get all alert categories, send a **GET** request to `/api/AlertService/AlertCategories`

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example response:

<!-- 
type: tab
title: JSON
-->
```json

   {
           "CategoriesDetails": [
                {
                    "@odata.type": "#AlertService.AlertCategory",
                    "Id": 4,
                    "Name": "Audit",
                    "CatalogName": "Application",
                    "SubCategoryDetails": [
                        {
                            "@odata.type": "#AlertService.AlertSubCategory",
                            "Id": 90,
                            "Name": "Devices",
                            "Description": "Devices"
                        },
                        {
                            "@odata.type": "#AlertService.AlertSubCategory",
                            "Id": 10,
                            "Name": "Generic",
                            "Description": "Generic"
                        },
                        {
                            "@odata.type": "#AlertService.AlertSubCategory",
                            "Id": 151,
                            "Name": "Power Configuration",
                            "Description": "Power Configuration"
                        },
                        {
                            "@odata.type": "#AlertService.AlertSubCategory",
                            "Id": 35,
                            "Name": "Users",
                            "Description": "Users"
                        }
                    ]
                }
           ]
        }
```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | ID of the category associated on the catalog.|
|**Name** | `String` | Name of category. |
|**CatalogName**| `String` |	Name of the catalog for which the category is associated.|
|**SubCategoryDetails** |  | |
|**Id** | `Integer` |ID of subcategory. |
|**Name**  | `String` |Name of subcategory.|
|**Description**| `String` |Description of subcategory|


## Get alert category for catalog
To get and alert category for a specific catalog within the collection, send a **GET** request to `/api/AlertService/AlertCategories('Name')`

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example call to `api/AlertService/AlertCategories('Application')` that returns a category list associated with catalog `Application`:

<!-- 
type: tab
title: JSON
-->
```json

   {
      "CategoriesDetails": [
        {
            "@odata.type": "#AlertService.AlertCategory",
            "Id": 4,
            "Name": "Audit",
            "CatalogName": "Application",
            "SubCategoryDetails": [
                {
                    "@odata.type": "#AlertService.AlertSubCategory",
                    "Id": 90,
                    "Name": "Devices",
                    "Description": "Devices"
                },
                {
                    "@odata.type": "#AlertService.AlertSubCategory",
                    "Id": 10,
                    "Name": "Generic",
                    "Description": "Generic"
                },
                {
                    "@odata.type": "#AlertService.AlertSubCategory",
                    "Id": 151,
                    "Name": "Power Configuration",
                    "Description": "Power Configuration"
                },
                {
                    "@odata.type": "#AlertService.AlertSubCategory",
                    "Id": 35,
                    "Name": "Users",
                    "Description": "Users"
                }
            ]
        }
      ]
  }

```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | ID of the category associated on the catalog.|
|**Name** | `String` | Name of category. |
|**CatalogName** | `String` |	Name of the catalog for which the category is associated.|
|**SubCategoryDetails** |  | |
|**Id** | `Integer` |ID of subcategory. |
|**Name**  | `String` |Name of subcategory.|
|**Description**| `String` |Description of subcategory.|


## Get all alert severities
To get all of the alerts with a specific alert severity, send a **GET** request to `/api/AlertService/AlertSeverities`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 


Example response | Description | 
---------|----------|
 **1** | Unknown | 
 **2** | Info | 
 **4** | Normal | 
 **8** | Warning | 
 **16** | Critical | 


<!-- 
type: tab
title: JSON
-->
```json

   {
       "value": [
        {
            "@odata.type": "#AlertService.AlertSeverity",
            "@odata.id": "/api/AlertService/AlertSeverities(1)",
            "Id": 1,
            "Name": "Unknown",
            "Description": "Unknown"
        },
        {
            "@odata.type": "#AlertService.AlertSeverity",
            "@odata.id": "/api/AlertService/AlertSeverities(2)",
            "Id": 2,
            "Name": "Info",
            "Description": "Info"
        }
      ]
  }

```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | ID of the severity.|
|**Name** | `String` | Name of the severity. |
|**Description** | `String` |	Description of the severity.|


## Get alert severity by ID
To display the information of a specific alert serverity according to ID, send a **GET** request to `/api/AlertService/AlertSeverities(Id)`

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example response | Description | 
---------|----------|
 **1** | Unknown | 
 **2** | Info | 
 **4** | Normal | 
 **8** | Warning | 
 **16** | Critical | 

**Example response** for severity type `info`:

```json
/api/AlertService/AlertSeverities(2)
```

<!-- 
type: tab
title: JSON
-->
```json
  {
    "@odata.context": "/api/$metadata#AlertService.AlertSeverity",
    "@odata.type": "#AlertService.AlertSeverity",
    "@odata.id": "/api/AlertService/AlertSeverities(2)",
    "Id": 2,
    "Name": "Info",
    "Description": "Info"
  }

```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | ID of the severity.|
|**Name** | `String` | Name of the severity. |
|**Description** | `String` |	Description of the severity.|


## Get all alert statuses
To get the statuses of all alerts, psend a **GET** request to `/api/AlertService/AlertStatuses`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

 Example response | Description | 
---------|----------|
 **1000** | Acknowledged | 
 **2000** | Not acknowledged | 
    

<!-- 
type: tab
title: JSON
-->
```json

   {
      "value": [
        {
            "@odata.type": "#AlertService.AlertStatus",
            "@odata.id": "/api/AlertService/AlertStatuses(1000)",
            "Id": 1000,
            "Name": "Acknowledged",
            "Description": "The event was acknowledged"
        },
        {
            "@odata.type": "#AlertService.AlertStatus",
            "@odata.id": "/api/AlertService/AlertStatuses(2000)",
            "Id": 2000,
            "Name": "Not-Acknowledged",
            "Description": "The event was not acknowledged"
        }
    ]
  }

```
<!-- type: tab-end -->


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` | ID of the alert status.|
|**Name** | `String` | Name of the alert status. |
|**Description** | `String` |	Description of the alert status.|




## Get alert status by ID
To get the status of an an alert by providing the alert ID, send a **GET** request to `/api/AlertService/AlertStatuses(id)`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

 Example response | Description | 
---------|----------|
 **1000** | Acknowledged | 
 **2000** | Not acknowledged | 

<!-- 
type: tab
title: JSON
-->
```json

  {
    "@odata.context": "/api/$metadata#AlertService.AlertStatus",
    "@odata.type": "#AlertService.AlertStatus",
    "@odata.id": "/api/AlertService/AlertStatuses(1000)",
    "Id": 1000,
    "Name": "Acknowledged",
    "Description": "The event was acknowledged"
  }

```
<!-- type: tab-end -->


| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **Id** | `Integer` | ID of the alert status.|
| **Name** | `String` | Name of the alert status. |
| **Description** | `String` |	Description of the alert status.|


## Get all alert catalogs
To get all alert catalogs, send a **GET** request to `/api/AlertService/AlertCatalogs`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example response:


<!-- 
type: tab
title: JSON
-->
```json
   {
      "value": [
          {
            "@odata.type": "#AlertService.AlertCatalog",
            "@odata.id": "/api/AlertService/AlertCatalogs(1)",
            "Id": 1,
            "CatalogName": "OMSA",
            "InternalName": "MIB-Dell-10892",
            "ReleaseVersion": "7.4.0",
            "IsBuiltIn": true,
            "IsVisible": true
        },
       {
            "@odata.type": "#AlertService.AlertCatalog",
            "@odata.id": "/api/AlertService/AlertCatalogs(2)",
            "Id": 2,
            "CatalogName": "iDRAC",
            "InternalName": "IDRAC-MIB-SMIv2",
            "ReleaseVersion": "v3.5",
            "IsBuiltIn": true,
            "IsVisible": true
        }
    ]
  }

```
<!-- type: tab-end -->



| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**Id** | `Integer` |Catalog ID.|
|**CatalogName** | `String` | Name of the catalog. |
|**InternalName** | `String` |Internal name associated with the catalog.|
|**ReleaseVersion** | `String` | Catalog version.|
|**IsBuiltIn** | `Boolean` |Indicates if the catalog in built-in or is manually included.|
|**IsVisible** | `Boolean` |Indicates if the catalog is visible or hidden.|



## Get alert catalog by ID
To get an alert catalog by providing the catalog ID, send a **GET** request to `/api/AlertService/AlertCatalogs(id)`.

**Privilege**: VIEW

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 

Example response:

<!-- 
type: tab
title: JSON
-->
```json
   {
     
    "@odata.context": "/api/$metadata#AlertService.AlertCatalog",
    "@odata.type": "#AlertService.AlertCatalog",
    "@odata.id": "/api/AlertService/AlertCatalogs(1)",
    "Id": 1,
    "CatalogName": "OMSA",
    "InternalName": "MIB-Dell-10892",
    "ReleaseVersion": "7.4.0",
    "IsBuiltIn": true,
    "IsVisible": true
  
  }

```
<!-- type: tab-end -->

| Attribute name |      Type     |  Description |
| -------------- | :-----------: | ----:        |
|**Id** | `Integer` |Catalog ID.|
|**CatalogName** | `String` | Name of the catalog. |
|**InternalName** | `String` |Internal name associated with the catalog.|
|**ReleaseVersion** | `String` |Catalog version.|
|**IsBuiltIn** | `Boolean` |Indicates if the catalog in built-in or is manually included.|
|**IsVisible** | `Boolean` |Indicates if the catalog is visible or hidden.|


## Acknowledge an alert
After you view an alert and understand its contents, you can acknowledge that you have read through the alert message. In one request, you can acknowledge only one of the following: 
- A specific alert.
- All alerts.
- A group of alerts based on the filters that you provide.

To acknowledge an alert, send a **POST** request to `/api/AlertService/Actions/AlertService.Acknowledge`.

**Privilege**: ALERT_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 

Example response: 

<!-- 
type: tab
title: JSON
-->
```json
{
 "AlertIds": [100,200,300],
 "AllAlerts": true,
 "Filters": {
    "DeviceIds": [0],
    "DeviceType": 0,
    "Severity": 0,
    "StatusType": 1000,
    "StartDate": "",
    "EndDate": "",
    "SourceName": "",
    "Category": 0,
    "SubCategory": 0,
    "Message": ""
 }
}

```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**AlertIds** | `Integer array` |List of alert IDs. This value is an empty list when AllAlerts is True|
|**AllAlerts** | `Boolean` |True or False. Determines if action should be taken for all alerts. |
|**Filters** |  |Optional filters to specify when AllAlerts is True.|
|**DeviceIds** | `Integer array` |List of IDs of devices.|
|**DeviceType**  | `Integer` |Device type ID.|
|**Severity** | `Integer` |ID of severity.|
|**StatusType** | `Integer` |ID of status type.|
|**StartDate** | `String` |Start date and time when the alerts were raised.|
|**EndDate** | `String` |End date and time when the alerts were raised.|
|**SourceName** | `String` |Source of the alert.|
|**Category** | `Integer` |Category ID associated to the alert.|
|**SubCategory** | `Integer` |Subcategory ID associated to the alert.|
|**Message** | `String` |Description of the issue.|


## Unacknowledge an alert
This method removes the acknowledgment from the alerts. Removes the acknowledgment from the alerts. In one request, you can remove the acknowledgment for only one of the following: a specific alert, all alerts, or a group of alerts based on the filters that you provide.

To unacknowledge an alert, send a **POST** request to `/api/AlertService/Actions/AlertService.Unacknowledge`.

**Privilege**: ALERT_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 

Example response:

<!-- 
type: tab
title: JSON
-->
```json
{
 "AlertIds": [100,200,300],
 "AllAlerts": true,
 "Filters": {
    "DeviceIds": [0],
    "DeviceType": 0,
    "Severity": 0,
    "StatusType": 1000,
    "StartDate": "",
    "EndDate": "",
    "SourceName": "",
    "Category": 0,
    "SubCategory": 0,
    "Message": ""
 }
}

```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**AlertIds** | `Integer array` |List of alert IDs. This value is an empty list when AllAlerts is True|
|**AllAlerts** | `Boolean` |True or False. Determines if action should be taken for all alerts. |
|**Filters** |  |Optional filters to specify when AllAlerts is True.|
|**DeviceIds** | `Integer array` |List of IDs of devices.|
|**DeviceType**  | `Integer` |Device type ID.|
|**Severity** | `Integer` |ID of severity.|
|**StatusType** | `Integer` |ID of status type.|
|**StartDate** | `String` |Start date and time when the alerts were raised.|
|**EndDate** | `String` |End date and time when the alerts were raised.|
|**SourceName** | `String` |Source of the alert.|
|**Category** | `Integer` |Category ID associated to the alert.|
|**SubCategory** | `Integer` |Subcategory ID associated to the alert.|
|**Message** | `String` |Description of the issue.|

## Remove an alert
In each request, you can remove only one of the following:
- A specific alert.
- All alerts.
- A group of alerts based on the filters you provide.

To remove an alert, send a **POST** request to `/api/AlertService/Actions/AlertService.RemoveAlerts`.

**Privilege**: ALERT_MANAGEMENT

Response code | Description | 
---------|----------|
 **200** | Success | 
 **400** | Bad request | 
 **401** | Unauthorized | 
 **403** | Forbidden | 

Example response: 

<!-- 
type: tab
title: JSON
-->
```json
{
 "AlertIds": [100,200,300],
 "AllAlerts": true,
 "Filters": {
    "DeviceIds": [0],
    "DeviceType": 0,
    "Severity": 0,
    "StatusType": 1000,
    "StartDate": "",
    "EndDate": "",
    "SourceName": "",
    "Category": 0,
    "SubCategory": 0,
    "Message": ""
 }
}

```
<!-- type: tab-end -->

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**AlertIds** | `Integer array` |List of alert IDs. This value is an empty list when AllAlerts is True|
|**AllAlerts** | `Boolean` |True or False. Determines if action should be taken for all alerts. |
|**Filters** |  |Optional filters to specify when AllAlerts is True.|
|**DeviceIds** | `Integer array` |List of IDs of devices.|
|**DeviceType**  | `Integer` |Device type ID.|
|**Severity** | `Integer` |ID of severity.|
|**StatusType** | `Integer` |ID of status type.|
|**StartDate** | `String` |Start date and time when the alerts were raised.|
|**EndDate** | `String` |End date and time when the alerts were raised.|
|**SourceName** | `String` |Source of the alert.|
|**Category** | `Integer` |Category ID associated to the alert.|
|**SubCategory** | `Integer` |Subcategory ID associated to the alert.|
|**Message** | `String` |Description of the issue.|














