# Discovering devices 
OpenManage Enterprise can be used to discover devices in the data center environment to manage them, improve their usability, and improve resource availability for the  business-critical operations. 
The **Discovery** page displays the number of devices discovered in task and information about the status of discovery job for that device.

## Create a discovery job
To create the discovery job and display the discovered device in the **All Devices** screen, send a **POST** request to `/api/DiscoveryConfigService/Jobs` and set the **content-type** to `application/json`.

> The discovery can also be scheduled to be run in the future or can have multiple protocols specified in the payload. 

```json
{
	"DiscoveryConfigGroupName": "Discovery-2017033110553636",
	"DiscoveryConfigGroupDescription": "null",
	"DiscoveryConfigModels": [{
		"DiscoveryConfigId": 331105536,
		"DiscoveryConfigDescription": "",
		"DiscoveryConfigStatus": "",
		"DiscoveryConfigTargets": [{
			"DiscoveryConfigTargetId": 0,
			"NetworkAddressDetail": "xx.xx.xx.xx-xx.xx.xx.xx",
			"AddressType": 30,
			"Disabled": false,
			"Exclude": false
		}],
		"ConnectionProfileId": 0,
		"ConnectionProfile": "{\"profileName\":\"\",\"profileDescription\":\"\",\"type\":\"DISCOVERY\",\"credentials\":[{\"id\":0,\"type\":\"WSMAN\",\"authType\":\"Basic\",\"modified\":false,\"credentials\":{\"username\":\"dummyusername\",\"password\":\"dummypassword\",\"caCheck\":false,\"cnCheck\":false,\"port\":443,\"retries\":2,\"timeout\":4,\"isHttp\":false,\"keepAlive\":false}}]}",
		"DeviceType": [
			1000
		]
	}],
	"Schedule": {
		"RunNow": true,
		"RunLater": false,
		"Cron": "startnow",
		"StartTime": "",
		"EndTime": ""
	},
	"CreateGroup": true,
	"TrapDestination": false
}
```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **DiscoveryConfigGroupName** | `String` |It represents the discovery job name and the group which is created for this discovery job. |
| **DiscoveryConfigGroupDescription** | `String`	| The discovery job description |
| **DiscoveryConfigModels**	|`Object`	 | Complex object representation of DiscoveryConfiguration .One DiscoveryConfiguration Group can contain many Discovery Configurations.|
| **DiscoveryConfigId**	|`Integer`	|Unique Id to represent the DiscoveryConfig. |
| **DiscoveryConfigTargets** |`Object`	|Complex object representing information of target which needs to be discovered.|
| **NetworkAddressDetail** |`String`	|Network Address of the target which needs to be discovered. It can be IPv4.IPv6,IPv4 Range, Ipv6 Range or a hostname. |
| **ConnectionProfile**	|`Object`	|Complex object describing which protocol and what credentials to be used in discovering the mentioned target. This field should be used to specify the credentials and the protocol to be used to discover the targert device.|
| **DeviceType**	|`Object`	|object describing the target device(s) type to be discovered. <br>1000-> Server<Br>2000-> Chassis<Br>5000-> Storage<Br>7000-> Network Switch<Br>|
| **Schedule**	|`Object`	|Complex object describing when to run the discovery. API guide has examples for scheduling the discovery task in a given future time. API Guide has more examples to schedule the discovery job in future.|
| **TrapDestination** 	|`Boolean`	|Set trap destination on the target device to receive the SNMP alerts. |


## Valid IP range formats
The following IP formats are valid:
- 10.35.0.0 
- 10.36.0.0-10.36.0.255 
- 10.37.0.0/24
- 2607:f2b1:f083:135::5500/118
- 2607:f2b1:f083:135::a500-2607:f2b1:f083:135::a600
- hostname.domain.tld
- hostname
- 2607:f2b1:f083:139::22a


## Get discovery jobs
Too view all existing discovery configuration and task details, send a **GET** request to `/api/DiscoveryConfigService/Jobs` to return a list of all discovery jobs created in OpenManage Enterprise.

Example response:

```json
{
	"@odata.context": "/api/$metadata#Collection(DiscoveryConfigService.DiscoveryJob)",
	"@odata.count": 2,
	"value": [{
			"@odata.type": "#DiscoveryConfigService.DiscoveryJob",
			"@odata.id": "/api/DiscoveryConfigService/Jobs(25249)",
			"JobId": 25249,
			"JobName": "Discovery-2017033110553636",
			"JobDescription": "Discovery-2017033110553636",
			"JobSchedule": "",
			"JobStartTime": "2018-09-14 01:24:25.322",
			"JobEndTime": null,
			"JobProgress": "89",
			"JobStatusId": 2050,
			"JobNextRun": null,
			"JobEnabled": true,
			"UpdatedBy": "admin",
			"LastUpdateTime": "2018-09-14 01:24:25.338",
			"DiscoveryConfigGroupId": 8,
			"DiscoveryConfigExpectedDeviceCount": 256,
			"DiscoveryConfigDiscoveredDeviceCount": 0,
			"DiscoveryConfigEmailRecipient": null,
			"DiscoveredDevicesByType": []
		},
		{
			"@odata.type": "#DiscoveryConfigService.DiscoveryJob",
			"@odata.id": "/api/DiscoveryConfigService/Jobs(25125)",
			"JobId": 25125,
			"JobName": "Discovery-201809131357033",
			"JobDescription": "Discovery-201809131357033",
			"JobSchedule": "",
			"JobStartTime": "2018-09-13 18:57:19.374",
			"JobEndTime": "2018-09-13 19:12:24.776",
			"JobProgress": "100",
			"JobStatusId": 2090,
			"JobNextRun": "2018-09-13 18:57:19.235",
			"JobEnabled": true,
			"UpdatedBy": "admin",
			"LastUpdateTime": "2018-09-13 19:12:24.777",
			"DiscoveryConfigGroupId": 7,
			"DiscoveryConfigExpectedDeviceCount": 256,
			"DiscoveryConfigDiscoveredDeviceCount": 31,
			"DiscoveryConfigEmailRecipient": null,
			"DiscoveredDevicesByType": [{
				"DeviceType": "SERVER",
				"Count": 31
			}]
		}
	]
}
```

## Edit existing discovery jobs
To make changes to any existing discovery configuration and then re-run the associated discovery task, send a **PUT** request to `/api/DiscoveryConfigService/DiscoveryConfigGroups({DiscoveryConfigGroupId})` and make sure to provide the `DiscoveryConfigGroupId`.

> `DiscoveryConfigGroupId` is not the `DiscoveryConfigId`.

```json
{
	"DiscoveryConfigGroupId": 5,
	"DiscoveryConfigGroupName": "discover1",
	"DiscoveryConfigGroupDescription": "null",
	"DiscoveryConfigParentGroupId": 0,
	"CreateGroup": true,
	"DiscoveryConfigModels": [{
		"DiscoveryConfigId": 15,
		"DiscoveryConfigDescription": "",
		"DiscoveryConfigStatus": "",
		"DiscoveryConfigTargets": [{
			"DiscoveryConfigTargetId": 0,
			"NetworkAddressDetail": "10.255.3.65",
			"AddressType": 30,
			"Disabled": false,
			"Exclude": false
		}],
		"ConnectionProfileId": 10139,
		"ConnectionProfile": "{\"profileName\":\"\",\"profileDescription\":\"\",\"type\":\"DISCOVERY\",\"credentials\":[{\"id\":0,\"type\":\"WSMAN\",\"authType\":\"Basic\",\"modified\":false,\"credentials\":{\"username\":\"root\",\"password\":\"OMEDev\",\"caCheck\":false,\"cnCheck\":false,\"port\":443,\"retries\":3,\"timeout\":60,\"isHttp\":false,\"keepAlive\":false}},{\"id\":0,\"type\":\"REDFISH\",\"authType\":\"Basic\",\"modified\":false,\"credentials\":{\"username\":\"root\",\"password\":\"OMEDev\",\"caCheck\":false,\"cnCheck\":false,\"port\":443,\"retries\":3,\"timeout\":60,\"isHttp\":false,\"keepAlive\":false}}]}",
		"DeviceType": [1000]
	}],
	"Schedule": {
		"RunNow": true,
		"RunLater": false,
		"Cron": "startnow",
		"StartTime": "",
		"EndTime": ""
	},
	"TrapDestination": false,
	"CommunityString": false,
	"UseAllProfiles": false
}
```

## Delete existing discovery jobs
To delete existing discovery configuration and the associated discovery job, send a **POST** request to `api/DiscoveryConfigService/Actions/DiscoveryConfigService.RemoveDiscoveryGroup` and make sure to provide the `DiscoveryGroupIds`.


```json
{
"DiscoveryGroupIds":[8] 
}
```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
| **DiscoveryGroupIds** | `Object` |Complex object representing the IDs of the discovery configuration groups which needs to be deleted.|

