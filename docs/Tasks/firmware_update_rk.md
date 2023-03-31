# Updating device firmware
<!-- theme: info -->
OpenManage Enterprise supports two types of firmware updates:
- Firmware updates using a catalog
- Firmware updates using a DUP file

## Updating firmware using a catalog
> Supported types that host a catalog and the associated DUP files referenced in the catalog.

To update the device firmware:
1. Create a catalog from an online source (for example: dell.com FTP) or point to a remote (`HTTP`/`HTTPS`/`CIFS`/`NFS`) repository. 
2. Create a firmware baseline for the (device/devices/device group) targets  using the catalog specified in step 1.
3. Check the firmware baseline compliance report to see which components need to be updated or downgraded.
4. Update specific components and track the job to completion.

## Updating firmware using a DUP file
To update the device firmware:
1. Upload the DUP file to OpenManage Enterprise and retrieve the file token returned upon successful upload.
2. Determine for the device or group if there are specific components that this DUP applies to for an update or downgrade.
3. Apply the DUP in the case of an update or downgrade to the specified components and parse the ID of the returned job that handles the application of the DUP file.
4. Track the job to completion and note error or success indicators.


<!-- theme: info -->

### Uploading a DUP file
To upload a DUP file to OpenManage Enterprise, send a **POST** request to `/api/UpdateService/Actions/UpdateService.UploadFile` and set the `content-type` to `application/octet-stream`. The file token mapping to the uploaded DUP is returned in the response.

> Ensure you are uploading a valid DUP file.

<!--
type: tab
title: Python
-->
```python
def upload_dup_file(ip_address, headers, file_path):
    """ Upload DUP file to OME and get a file token in return """
    token = None
    upload_success = False
    url = 'https://%s/api/UpdateService/Actions/UpdateService.UploadFile' % ip_address
    curr_headers = copy.deepcopy(headers)
    curr_headers['content-type'] = 'application/octet-stream'
    if os.path.isfile(file_path):
        if os.path.getsize(file_path) > 0:
            with open(file_path, 'rb') as payload:
                print ("Uploading %s .. This may take a while" % file_path)
                response = requests.post(url, data=payload, verify=False,
                                         headers=curr_headers)
                if response.status_code == 200:
                    upload_success = True
                    token = str(response.text)
                    print ("Successfully uploaded ", file_path)
                else:
                    print ("Unable to upload %s to %s" % (file_path, ip_address))
                    print ("Request Status Code = %s" % response.status_code)
        else:
            print ("File %s seems to be empty ... Exiting" % file_path)

    else:
        print ("File not found ... Retry")
    return (upload_success, token)
```

<!--
type: tab
title: Powershell
-->
```powershell
function Push-DupToOME($IpAddress,$Headers, $DupFile) {
    $FileToken = $null
    $UploadActionUri = "https://$($IpAddress)/api/UpdateService/Actions/UpdateService.UploadFile"
    Write-Host "Uploading $($DupFile) to $($IpAddress). This action may take some time to complete."
    $UploadResponse = Invoke-WebRequest -Uri $UploadActionUri -UseBasicParsing -Method Post -InFile $DupFile -ContentType "application/octet-stream" -Headers $Headers
    if ($UploadResponse.StatusCode -eq 200) {
        ## Successfully uploaded the DUP file . Get the file token
        ## returned by OME on upload of the DUP file
        ## The file token is returned as an array of decimals that maps to ascii text values
        $FileToken = [System.Text.Encoding]::ASCII.GetString($UploadResponse.Content)
        Write-Host "Successfully uploaded $($DupFile)"
    }
    else {
        Write-Warning "Unable to upload $($DupFile) to $($IpAddress)..."
    }
    return $FileToken
}
```
<!-- type: tab-end -->


### Determining DUP components
To determine the components that the DUP applies to, send a **POST** request to `/api/UpdateService/Actions/UpdateService.GetSingleDupReport`. An array of devices is returned, with the applicable component list that the DUP can upgrade or downgrade on each device. A response status code of `200` is returned to indicate that the operation was successful.

Example response:

```json
{
    "SingleUpdateReportFileToken": "1481131455800",
    "SingleUpdateReportTargets": [1600,1601,1602,1603],
    "SingleUpdateReportBaseline": [32],
    "SingleUpdateReportGroup": [500]
}
```

| Attribute name |         Type         |  Description  |
| -------------: | :------------------: | :-----------  |
|**SingleUpdateReportFileToken** | `Numeric file token` | File token mapping to the DUP file |
|**SingleUpdateReportTargets**   | `Integer array` |Array of device IDs to check the DUP against |
|**SingleUpdateReportBaseline**  | `Integer array` | Array of baseline IDs. The DUP in this case is checked against the devices associated with these baselines|
|**SingleUpdateReportGroup**   | `Integer array` |Array of group IDs containing devices to check the DUP against|

Example response:

```json
[
    {
        "DeviceId": 10073,
        "DeviceReport": {
            "DeviceId": "10073",
            "DeviceName": "MX5108N-A1",
            "DeviceIPAddress": "100.101.21.58",
            "DeviceServiceTag": "CJ2K6Z2",
            "DeviceModel": "MX5108n Ethernet Switch",
            "DeviceTypeId": "4000",
            "DeviceTypeName": "NETWORK_IOM",
            "Components": [
                {
                    "ComponentUpdateAction": "UPGRADE",
                    "ComponentCurrentVersion": "10.5.0.5",
                    "ComponentUriInformation": null,
                    "ComponentVersion": "10.5.2.3",
                    "ComponentCriticality": "Recommended",
                    "IsCompliant": "CRITICAL",
                    "ComponentTargetIdentifier": "106550",
                    "ComponentSourceName": "MX5108N-ON",
                    "ComponentRebootRequired": "true",
                    "ComponentUniqueIdentifier": "7a7e7eaf-9c1d-43df-8a3b-952dc0aca0b8",
                    "ComponentName": "Dell EMC Networking MX5108n SmartFabric OS10",
                    "PrerequisiteInfo": "",
                    "ImpactAssessment": "",
                    "ComponentType": "FIRMWARE",
                    "DependencyUpgradeRequired": true,
                    "ComplianceDependencies": [
                        {
                            "ComplianceDependencyId": 0,
                            "Version": "10.5.0.7",
                            "Path": "FOLDER06412143M/1/Network_Firmware_8CC0D_WN64_10.5.0.7_A00.EXE",
                            "Name": "Dell EMC Networking OS10 Enterprise",
                            "UniqueIdentifier": "626b0bc2-a99c-4991-8651-ad3e7ba170f1",
                            "UpdateAction": "UPGRADE",
                            "Uri": "http://www.dell.com/support/home/us/en/19/Drivers/DriversDetails?driverId=8CC0D",
                            "SourceName": "MX5108N-ON",
                            "RebootRequired": false,
                            "IsHardDependency": true
                        }
                    ]
                }
            ]
        }
    },
    {
        "DeviceId": 10074,
        "DeviceReport": {
            "DeviceId": "10074",
            "DeviceName": "MX5108N-A2",
            "DeviceIPAddress": "100.101.21.56",
            "DeviceServiceTag": "CJ2J7Z2",
            "DeviceModel": "MX5108n Ethernet Switch",
            "DeviceTypeId": "4000",
            "DeviceTypeName": "NETWORK_IOM",
            "Components": [
                {
                    "ComponentUpdateAction": "DOWNGRADE",
                    "ComponentCurrentVersion": "10.5.0.5",
                    "ComponentUriInformation": null,
                    "ComponentVersion": "10.5.2.3",
                    "ComponentCriticality": "Ok",
                    "IsCompliant": "DOWNGRADE",
                    "ComponentTargetIdentifier": "106550",
                    "ComponentSourceName": "MX5108N-ON",
                    "ComponentRebootRequired": "true",
                    "ComponentUniqueIdentifier": "7a7e7eaf-9c1d-43df-8a3b-952dc0aca0b8",
                    "ComponentName": "Dell EMC Networking MX5108n SmartFabric OS10",
                    "PrerequisiteInfo": "",
                    "ImpactAssessment": "",
                    "ComponentType": "FIRMWARE",
                    "DependencyUpgradeRequired": false,
                    "ComplianceDependencies": [
                        {
                            "ComplianceDependencyId": 0,
                            "Version": "10.5.0.7",
                            "Path": "FOLDER06412143M/1/Network_Firmware_8CC0D_WN64_10.5.0.7_A00.EXE",
                            "Name": "Dell EMC Networking OS10 Enterprise",
                            "UniqueIdentifier": "626b0bc2-a99c-4991-8651-ad3e7ba170f1",
                            "UpdateAction": "DOWNGRADE",
                            "Uri": "http://www.dell.com/support/home/us/en/19/Drivers/DriversDetails?driverId=8CC0D",
                            "SourceName": "MX5108N-ON",
                            "RebootRequired": false,
                            "IsHardDependency": true
                        }
                    ]
                }
            ]
        }
    }
]
```

| Attribute name | Attribute Subtype | Type | Description |
|---|---|---|---|
| **DeviceId** |  | `Integer` | The ID of the device |
|  | **DeviceReport**: |  |  |
|  | **DeviceId** | `String` | The ID of the device |
|  | **DeviceName** | `String` | The ID of the device |
|  | **DeviceIPAddress** | `String` | The ID of the device |
|  | **DeviceServiceTag** | `String` | The ID of the device |
|  | **DeviceModel** | `String` | The ID of the device |
|  | **DeviceTypeId** | `String` | Shows the device type (Derived from `/api/DeviceService/DeviceType`) |
|  | **DeviceTypeName** | `String` | Indicates if the device is a server or a chassis etc (Derived from `/api/DeviceService/DeviceType`) |
| **Components**: |  |  |  |
|  | **ComponentUpdateAction** | `String` | Indicates whether the firmware versions match / will be upgraded or will be downgraded via status of EQUAL / UPGRADE / DOWNGRADE |
|  | **ComponentCurrentVersion** | `String` | Indicates the current version of the component |
| **ComponentUriInformation** |  | `String` |  |
|  | **ComponentVersion** | `String` | The ID of the device |
|  | **ComponentCriticality** | `String` | Indicates the criticality of the update |
|  | **IsCompliant** | `String` | Indicates if the component is compliant - a value of OK indicates that the version on the device matches the DUP version for instance. |
|  | **ComponentTargetIdentifier** | `String` | Matches the Component ID or PCI Device Info (device id, sub device id, vendor id, sub vendor id) |
|  | **ComponentSourceName** | `String` | Internal reference to the actual component |
|  | **ComponentRebootRequired** | `String` | Indicates if a reboot is required to apply the update |
|  | **ComponentUniqueIdentifier** | `String` | Internally used unique identifier of the component |
|  | **ComponentName** | `String` | Descriptive name of the component |
|  | **PrerequisiteInfo** | `String` | Displays the prerequisites for the firmware update |
|  | **ImpactAssessment** | `String` | Displays a message about the impact of the firmware update |
|  | **ComponentType** | `String` | Indicates the type of the component |
|  | **DependencyUpgradeRequired** | `Boolean` | Indicates Dependency Upgrade required before performing actual upgrade |
| **ComplianceDependencies**: |  |  |  |
|  | **ComplianceDependencyId** | `String` | Internally used compliance Dependency Id |
|  | **Version** | `String` | Indicates the dependent component version |
|  | **Path** | `String` | Intrnally used path of the dependent component dup |
|  | **Name** | `String` | Descriptive name of the dependent component |
|  | **UniqueIdentifier** | `String` | Internally used unique identifier of the component |
|  | **UpdateAction** | `String` | Indicates whether the dependent firmware versions match / will be upgraded or will be downgraded via status of EQUAL / UPGRADE / DOWNGRADE |
|  | **Uri** | `String` | Dependent dup URI from which dup is downloaded |
|  | **SourceName** | `String` | Internal reference to the actual component |
|  | **RebootRequired** | `Boolean` | Indicates if a reboot is required to apply the update |
|  | **IsHardDependency** | `Boolean` | Indicates if a dependency is hard dependency. If the value is true, actual update cannot perform unless dependency is resolved |

<!-- theme: info -->
### Creating a job on the DUP
> If they are any dependencies, create a job on the dependent DUP before creating a job on an actual DUP.

To create a job to upgrade or downgrade components, send a **POST** request to `/api/JobService/Jobs`. A response status code of `201` is returned to indicate that the operation was successful.

Example response:

```json
{
    "State": "Enabled",
    "JobDescription": "dup test",
    "Params": [
        {
            "Value": "INSTALL_FIRMWARE",
            "Key": "operationName",
            "JobId": 0
        },
        {
            "Value": "false",
            "Key": "complianceUpdate",
            "JobId": 0
        },
        {
            "Value": "false",
            "Key": "stagingValue",
            "JobId": 0
        },
        {
            "Value": "true",
            "Key": "signVerify",
            "JobId": 0
        }
    ],
    "Targets": [
        {
            "TargetType": {
                "Id": 1000,
                "Name": "SERVER"
            },
            "Data": "DCIM:INSTALLED#741__BIOS.Setup.1-1=1549578753319",
            "Id": 25008
        }
    ],
    "CreatedBy": "admin",
    "Schedule": "startnow",
    "Id": 0,
    "JobType": {
        "Id": 5,
        "Name": "Update_Task"
    },
    "JobName": "Firmware Update Task"
}
```

|  **Attribute name** | **Attribute Subtype** |            **Type**          |    **Description** |  |
|---|---|---|---|---|
| **State**  |  | `String` |  State for the update   job |  |
| **JobDescription**  |  | `String` |  Description for the update job |  |
| **Id**  |  | `Integer` |  Default to 0 |  |
| **JobName**  |  | `String` |  Name for the update job |  |
| **Schedule**  |  | `String` |  When to run the   update job |  |
| **JobType**  |  |  |   |  |
|  | **ID**  | `Integer` |  Use 5 for an update   task |  |
|  | **Name**  | `String` |  Type of the task - Update_Task |  |
| **Params**  |  |  |   |  |
|  | **operationName**  | `String` |  Type of operation - INSTALL_FIRMWARE |  |
|  | **complianceUpdate**  | `Boolean` |  Indicates if this   update is to match compliance |  |
|  | **signVerify**  | `Boolean` |  Verify DUP signature or not |  |
|  | **stagingValue**  | `Boolean` | If `true`,  stages   updates and runs them on the next reboot of the system  |  |
| **Targets**  |  |  |   |  |
|  | **ID**  | `Integer` |  Device ID being   updated  |  |
|  | **Data**  | `String` |  String identifying component to be updated   (seen from the component compliance report ID `SourceName` field). The   User needs to append the filetoken at the end of the Component Source Name   preceded by an " = " sign. |  |
| **TargetType**  |  |  |  |  |
|  | **ID**  | `Integer` |  Numeric Device Type ID |  |
|  | **Name**  | `String` |  Device / group |  |


### Tracking an existing job on the DUP
To track an existing job on the DUP:
1. Use a valid DUP file.
2. Send a **POST** request to `/api/UpdateService/Actions/UpdateService.UploadFile`.
2. Set the `content-type` to `application/octet-stream`.
3. Parse the returned response status code and ensure it is `200` for success.

> The filetoken mapping to this DUP is returned in the response.

For example:

<!--
type: tab
title: Python
-->

```python

def track_job_to_completion(ip_address, headers, job_id):
    """ Tracks the update job to completion / error """
    job_status_map = {
        "2020": "Scheduled",
        "2030": "Queued",
        "2040": "Starting",
        "2050": "Running",
        "2060": "Completed",
        "2070": "Failed",
        "2090": "Warning",
        "2080": "New",
        "2100": "Aborted",
        "2101": "Paused",
        "2102": "Stopped",
        "2103": "Canceled"
    }

    max_retries = 20
    sleep_interval = 60
    failed_job_status = [2070, 2090, 2100, 2101, 2102, 2103]
    job_url = 'https://%s/api/JobService/Jobs(%s)' % (ip_address, job_id)
    loop_ctr = 0
    job_incomplete = True
    print ("Polling %s to completion ..." % job_id)
    while loop_ctr < max_retries:
        loop_ctr += 1
        time.sleep(sleep_interval)
        job_resp = requests.get(job_url, headers=headers, verify=False)
        if job_resp.status_code == 200:
            job_status = str((job_resp.json())['LastRunStatus']['Id'])
            print ("Iteration %s: Status of %s is %s" % (loop_ctr, job_id, job_status_map[job_status]))
            if int(job_status) == 2060:
                job_incomplete = False
                print ("Completed updating firmware successfully ... Exiting")
                break
            elif int(job_status) in failed_job_status:
                job_incomplete = False
                print ("Update job failed ... ")
                job_hist_url = str(job_url) + "/ExecutionHistories"
                job_hist_resp = requests.get(job_hist_url, headers=headers, verify=False)
                if job_hist_resp.status_code == 200:
                    job_history_id = str((job_hist_resp.json())['value'][0]['Id'])
                    job_hist_det_url = str(job_hist_url) + "(" + job_history_id + ")/ExecutionHistoryDetails"
                    job_hist_det_resp = requests.get(job_hist_det_url,
                                                     headers=headers,
                                                     verify=False)
                    if job_hist_det_resp.status_code == 200:
                        print (job_hist_det_resp.text)
                    else:
                        print ("Unable to parse job execution history .. Exiting")
                break
        else:
            print ("Unable to poll status of %s - Iteration %s " % (job_id, loop_ctr))
    if job_incomplete:
        print ("Job %s incomplete after polling %s times...Check status" % (job_id, max_retries))
```

<!--
type: tab
title: PowerShell
-->

```powershell

function Wait-OnUpdateJobs($IpAddress, $Headers, $Type, $JobId)
{
    $JOB_STATUS_MAP = @{
        "2020"="Scheduled";
        "2030"="Queued";
        "2040"="Starting";
        "2050"="Running";
        "2060"="Completed";
        "2070"="Failed";
        "2090"="Warning";
        "2080"="New";
        "2100"="Aborted";
        "2101"="Paused";
        "2102"="Stopped";
        "2103"="Canceled"
    }

    $FailedJobStatuses = @(2070,2090,2100,2101,2102,2103)

    $MAX_RETRIES = 20
    $SLEEP_INTERVAL = 60

    $JobSvcUrl = "https://$($IpAddress)/api/JobService/Jobs($($JobId))"
    $Ctr = 0
    do {        
        $Ctr++
        Start-Sleep -Seconds $SLEEP_INTERVAL
        $JobResp = Invoke-WebRequest -UseBasicParsing -Uri $JobSvcUrl -Headers $Headers -ContentType $Type -Method Get
        if ($JobResp.StatusCode -eq 200) {
            $JobData = $JobResp.Content | ConvertFrom-Json
            $JobStatus = $JobData.LastRunStatus.Id
            Write-Host "Iteration $($Ctr): Status of $($JobId) is $(($JOB_STATUS_MAP.$JobStatus))"
            if ($JobStatus -eq 2060) {
                ## Completed successfully
                Write-Host "Completed updating firmware successfully ..."
                break
            }
            elseif ($FailedJobStatuses -contains $JobStatus) {
                Write-Warning "Update job failed .... "
                $JobExecUrl = "$($JobSvcUrl)/ExecutionHistories"
                $ExecResp = Invoke-WebRequest -UseBasicParsing -Uri $JobExecUrl -Method Get -Headers $Headers -ContentType $Type
                if ($ExecResp.StatusCode -eq 200) {
                    $ExecRespInfo = $ExecResp.Content | ConvertFrom-Json
                    $HistoryId = $ExecRespInfo.value[0].Id
                    $ExecHistoryUrl = "$($JobExecUrl)($($HistoryId))/ExecutionHistoryDetails"
                    $HistoryResp = Invoke-WebRequest -UseBasicParsing -Uri $ExecHistoryUrl -Method Get -Headers $Headers -ContentType $Type
                    if ($HistoryResp.StatusCode -eq 200) {
                        Write-Host ($HistoryResp.Content | ConvertFrom-Json | ConvertTo-Json -Depth 4)
                    }
                    else {
                        Write-Warning "Unable to get job execution history details"
                    }
                }
                else {
                    Write-Warning "Unable to get job execution history info"
                }
                break
            }
            else { continue }
        }
        else {Write-Warning "Unable to get status for $($JobId) .. Iteration $($Ctr)"}
    } until ($Ctr -ge $MAX_RETRIES)
}
```
<!-- type: tab-end -->


---