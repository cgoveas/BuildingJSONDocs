# Getting device inventory
The workflow for retrieving inventory details of a device is:
1. Get the inventory details of a device based on it's `Device_ID`.
2. Get the inventory types.
3. Get the inventory details of a device based on the filters in the inventory response.

<!-- theme: info -->

## Get the inventory details of a device
To retrieve inventory details of a device, specify by a valid `Device_ID` when sending a **GET** request to `/api/DeviceService/Devices(Device_ID)/InventoryDetails`. 

> You can also retrieve the inventory by filtering on `InventoryType`.


Response code | Description | 
---------|----------|
 **200** | Success | 


Example request:

<!-- 
type: tab
title: Python
-->

```python
def get_device_inventory(ip_address, user_name, password, filter_by, field, inventory_type):
    """ Get inventory details for a device based on filters """
    filter_map = {'Name': 'DeviceName',
                  'Id': 'Id',
                  'SvcTag': 'DeviceServiceTag'}
    inventory_types = {
        "cpus": "serverProcessors",
        "os": "serverOperatingSystems",
        "disks": "serverArrayDisks",
        "controllers": "serverRaidControllers",
        "memory": "serverMemoryDevices"}

    try:

        session_url = 'https://%s/api/SessionService/Sessions' % ip_address
        base_url = "https://%s/api/DeviceService/Devices?$filter=%s eq" % (ip_address, filter_map[filter_by])
        if filter_by == 'Id':
            device_url = "%s %s" % (base_url, field)
        else:
            device_url = "%s '%s'" % (base_url, field)
        headers = {'content-type': 'application/json'}

        user_details = {'UserName': user_name,
                        'Password': password,
                        'SessionType': 'API'}
        session_info = requests.post(session_url, verify=False,
                                     data=json.dumps(user_details),
                                     headers=headers)
        if session_info.status_code == 201:
            headers['X-Auth-Token'] = session_info.headers['X-Auth-Token']
            response = requests.get(device_url, headers=headers, verify=False)
            if response.status_code == 200:
                json_data = response.json()
                if json_data['@odata.count'] > 1:
                    print("WARNING: Filter returned more than one result. Ignore all results except the first.")
                if json_data['@odata.count'] > 0:
                    device_id = json_data['value'][0]['Id']
                    inventory_url = "https://%s/api/DeviceService/Devices(%s)/InventoryDetails" % (
                        ip_address, device_id)
                    if inventory_type:
                        inventory_url = "https://%s/api/DeviceService/Devices(%s)/InventoryDetails(\'%s\')" % \
                                        (ip_address, device_id, inventory_types[inventory_type])
                    inven_resp = requests.get(inventory_url, headers=headers,
                                              verify=False)
                    if inven_resp.status_code == 200:
                        print("\n*** Inventory for device (%s) ***" % field)
                        print(json.dumps(inven_resp.json(), indent=4,
                                         sort_keys=True))
                    elif inven_resp.status_code == 400:
                        print("Inventory type %s not applicable for device with Id %s" % (inventory_type, device_id))
                    else:
                        print("Unable to retrieve inventory for device %s due to status code %s" %
                              (device_id, inven_resp.status_code))
                else:
                    print("Unable to retrieve details for device (%s) from %s" % (field, ip_address))
            else:
                print("No device data retrieved from %s" % ip_address)
        else:
            print("Unable to create a session with appliance %s" % ip_address)
    except Exception as error:
        print("Unexpected error:", str(error))

```

<!--
type: tab
title: Powershell
-->

```powershell

 .EXAMPLE
   .\Get-DeviceInventory.ps1 -IpAddress "10.xx.xx.xx" -InventoryType {InventoryType} -FilterBy SvcTag -DeviceInfo BZ0M630
    where {InventoryType} can be cpus or memory or controllers or disks or os
   In this instance you will be prompted for credentials to use to
   connect to the appliance
#>

function Set-CertPolicy() {
  ## Trust all certs - for sample usage only
  Try {
    add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
    }
}
"@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  }
  catch {
    Write-Error "Unable to add type for cert policy"
  }
}

Try {
  Set-CertPolicy
  $FilterMap = @{'Name' = 'DeviceName'; 'Id' = 'Id'; 'SvcTag' = 'DeviceServiceTag' }
  $SessionUrl = "https://$($IpAddress)/api/SessionService/Sessions"
  $InventoryTypeMap = @{"cpus" = "serverProcessors"; "os" = "serverOperatingSystems"; "disks" = "serverArrayDisks"; "controllers" = "serverRaidControllers"; "memory" = "serverMemoryDevices" }
  $FilterExpr = $FilterMap[$FilterBy]
  $BaseUrl = "https://$($IpAddress)/api/DeviceService/Devices?`$filter=$($FilterExpr) eq"
  $DevUrl = ""
  $Type = "application/json"
  $UserName = $Credentials.username
  $Password = $Credentials.GetNetworkCredential().password
  $UserDetails = @{"UserName" = $UserName; "Password" = $Password; "SessionType" = "API" } | ConvertTo-Json
  $Headers = @{}
  $DeviceId = ""
  if ($FilterBy -eq 'Id') {
    $DevUrl = "$($BaseUrl) $($DeviceInfo)"
  }
  else {
    $DevUrl = "$($BaseUrl) '$($DeviceInfo)'"
  }
  $SessResponse = Invoke-WebRequest -Uri $SessionUrl -Method Post -Body $UserDetails -ContentType $Type
  if ($SessResponse.StatusCode -eq 200 -or $SessResponse.StatusCode -eq 201) {
    ## Successfully created a session - extract the auth token from the response
    ## header and update our headers for subsequent requests
    $Headers."X-Auth-Token" = $SessResponse.Headers["X-Auth-Token"]
    $DevResp = Invoke-WebRequest -Uri $DevUrl -Headers $Headers -Method Get -ContentType $Type
    if ($DevResp.StatusCode -eq 200) {
      $DevInfo = $DevResp.Content | ConvertFrom-Json
      if ($DevInfo.'@odata.count' -gt 0) {
        $DeviceId = $DevInfo.value[0].Id
        $InventoryUrl = "https://$($IpAddress)/api/DeviceService/Devices($($DeviceId))/InventoryDetails"
        if ($InventoryType) {
          $InventoryUrl = "https://$($IpAddress)/api/DeviceService/Devices($($DeviceId))/InventoryDetails('$($InventoryTypeMap[$InventoryType])')"
        }
        $InventoryResp = Invoke-WebRequest -Uri $InventoryUrl -Headers $Headers -Method Get -ContentType $Type
        if ($InventoryResp.StatusCode -eq 200) {
          $InventoryInfo = $InventoryResp.Content | ConvertFrom-Json
          $InventoryDetails = $InventoryInfo | ConvertTo-Json -Depth 6
          Write-Host  $InventoryDetails
        }
        elseif ($InventoryResp.StatusCode -eq 400) {
          Write-Warning "Inventory type $($InventoryType) not applicable for device id  $($DeviceId) "
        }
        else {
          Write-Warning "Unable to retrieve inventory for device $($DeviceId) due to status code ($($InventoryResp.StatusCode))"
        }
      }
      else {
        Write-Warning "Unable to retrieve details for device ($($DeviceInfo)) from $($IpAddress)"
      }
    }
    else {
      Write-Warning "No device data retrieved from from $($IpAddress)"
    }
  }
  else {
    Write-Error "Unable to create a session with appliance $($IpAddress)"
  }
}
catch {
  Write-Error "Exception occured at line $($_.InvocationInfo.ScriptLineNumber) - $($_.Exception.Message)"
}

```

<!-- type: tab-end -->

Example response:

<!-- 
type: tab
title: JSON
-->

```json

"{
  ""@odata.context"": ""$metadata#Collection(DeviceService.InventoryDetail)"",
  ""@odata.count"": 18,
  ""value"": [
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverDeviceCards')"",
      ""InventoryType"": ""serverDeviceCards"",
      ""InventoryInfo"": [
        {
          ""Id"": 127,
          ""SlotNumber"": ""SMBus.Embedded.3-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Lewisburg SMBus"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 128,
          ""SlotNumber"": ""P2PBridge.Embedded.1-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Lewisburg PCI Express Root Port #1"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 129,
          ""SlotNumber"": ""HostBridge.Embedded.1-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Intel Corporation"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 130,
          ""SlotNumber"": ""Video.Embedded.1-1"",
          ""Manufacturer"": ""Matrox Electronics Systems Ltd."",
          ""Description"": ""Matrox Electronics Systems Ltd."",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 131,
          ""SlotNumber"": ""ISABridge.Embedded.1-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Lewisburg LPC Controller"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 132,
          ""SlotNumber"": ""AHCI.Embedded.1-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Lewisburg SSATA Controller [AHCI mode]"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 133,
          ""SlotNumber"": ""P2PBridge.Embedded.2-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Lewisburg PCI Express Root Port #5"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        },
        {
          ""Id"": 134,
          ""SlotNumber"": ""AHCI.Embedded.2-1"",
          ""Manufacturer"": ""Intel Corporation"",
          ""Description"": ""Lewisburg SATA Controller [AHCI mode]"",
          ""DatabusWidth"": ""Unknown"",
          ""SlotLength"": ""Unknown"",
          ""SlotType"": ""Unknown""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverProcessors')"",
      ""InventoryType"": ""serverProcessors"",
      ""InventoryInfo"": [
        {
          ""Id"": 28,
          ""Family"": ""Intel(R) Xeon(TM)"",
          ""MaxSpeed"": 4000,
          ""CurrentSpeed"": 1600,
          ""SlotNumber"": ""CPU.Socket.1"",
          ""Status"": 2000,
          ""NumberOfCores"": 8,
          ""NumberOfEnabledCores"": 8,
          ""BrandName"": ""Intel"",
          ""ModelName"": ""Genuine Intel(R) CPU 0000%@"",
          ""InstanceId"": ""CPU.Socket.1"",
          ""Voltage"": ""1.8""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverNetworkInterfaces')"",
      ""InventoryType"": ""serverNetworkInterfaces"",
      ""InventoryInfo"": []
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverOperatingSystems')"",
      ""InventoryType"": ""serverOperatingSystems"",
      ""InventoryInfo"": [
        {
          ""Id"": 19,
          ""Hostname"": ""WIN-02GODDHDJTC""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverVirtualFlashes')"",
      ""InventoryType"": ""serverVirtualFlashes"",
      ""InventoryInfo"": []
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverPowerSupplies')"",
      ""InventoryType"": ""serverPowerSupplies"",
      ""InventoryInfo"": []
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverArrayDisks')"",
      ""InventoryType"": ""serverArrayDisks"",
      ""InventoryInfo"": [
        {
          ""Id"": 10,
          ""DeviceId"": 0,
          ""DiskNumber"": ""Disk 0 on Embedded AHCI Controller 2"",
          ""VendorName"": ""SEAGATE"",
          ""Status"": 2000,
          ""StatusString"": ""Unknown"",
          ""ModelNumber"": ""ST1000NX0423"",
          ""SerialNumber"": ""S47171Y1"",
          ""Revision"": ""NA03"",
          ""EnclosureId"": ""Disk 0 on Embedded AHCI Controller 2"",
          ""Channel"": 0,
          ""Size"": ""931.52 GB"",
          ""FreeSpace"": ""0 bytes"",
          ""UsedSpace"": ""0 bytes"",
          ""BusType"": ""SATA"",
          ""SlotNumber"": 0,
          ""MediaType"": ""Magnetic Drive"",
          ""RemainingReadWriteEndurance"": ""255"",
          ""SecurityState"": ""Not Capable""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverRaidControllers')"",
      ""InventoryType"": ""serverRaidControllers"",
      ""InventoryInfo"": [
        {
          ""Id"": 20,
          ""DeviceId"": 0,
          ""Name"": ""Lewisburg SATA Controller [AHCI mode]"",
          ""Fqdd"": ""AHCI.Embedded.2-1"",
          ""Status"": 2000,
          ""StatusTypeString"": ""UNKNOWN"",
          ""RollupStatus"": 2000,
          ""RollupStatusString"": ""UNKNOWN"",
          ""CacheSizeInMb"": 0,
          ""PciSlot"": 0
        },
        {
          ""Id"": 19,
          ""DeviceId"": 0,
          ""Name"": ""Lewisburg SSATA Controller [AHCI mode]"",
          ""Fqdd"": ""AHCI.Embedded.1-1"",
          ""Status"": 2000,
          ""StatusTypeString"": ""UNKNOWN"",
          ""RollupStatus"": 2000,
          ""RollupStatusString"": ""UNKNOWN"",
          ""CacheSizeInMb"": 0,
          ""PciSlot"": 0
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverMemoryDevices')"",
      ""InventoryType"": ""serverMemoryDevices"",
      ""InventoryInfo"": [
        {
          ""Id"": 19,
          ""Name"": ""DIMM.Socket.A1"",
          ""BankName"": ""A"",
          ""Size"": 8192,
          ""Status"": 2000,
          ""Manufacturer"": ""Micron Technology"",
          ""PartNumber"": ""9ASF1G72PZ-2G6D1"",
          ""SerialNumber"": ""1446F14C"",
          ""TypeDetails"": ""DDR4 DIMM"",
          ""ManufacturerDate"": ""Mon Oct 17 07:00:00 2016 UTC"",
          ""Speed"": 2666,
          ""CurrentOperatingSpeed"": 2133,
          ""Rank"": ""Single Rank"",
          ""InstanceId"": ""DIMM.Socket.A1"",
          ""DeviceDescription"": ""DIMM A1""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverStorageEnclosures')"",
      ""InventoryType"": ""serverStorageEnclosures"",
      ""InventoryInfo"": []
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('serverSupportedPowerStates')"",
      ""InventoryType"": ""serverSupportedPowerStates"",
      ""InventoryInfo"": [
        {
          ""Id"": 109,
          ""PowerState"": 2
        },
        {
          ""Id"": 110,
          ""PowerState"": 5
        },
        {
          ""Id"": 111,
          ""PowerState"": 8
        },
        {
          ""Id"": 112,
          ""PowerState"": 10
        },
        {
          ""Id"": 113,
          ""PowerState"": 11
        },
        {
          ""Id"": 114,
          ""PowerState"": 12
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('deviceLicense')"",
      ""InventoryType"": ""deviceLicense"",
      ""InventoryInfo"": [
        {
          ""SoldDate"": ""2016-06-12 14:48:13.000"",
          ""LicenseBound"": 4,
          ""EvalTimeRemaining"": 0,
          ""AssignedDevices"": ""iDRAC.Embedded.1"",
          ""LicenseStatus"": 3000,
          ""EntitlementId"": ""PX1234567"",
          ""LicenseDescription"": ""iDRAC9 Enterprise License"",
          ""LicenseType"": {
            ""Name"": ""Perpetual"",
            ""LicenseId"": 1
          }
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('deviceCapabilities')"",
      ""InventoryType"": ""deviceCapabilities"",
      ""InventoryInfo"": [
        {
          ""Id"": 366,
          ""CapabilityType"": {
            ""CapabilityId"": 17,
            ""Name"": ""FEATURES_14G"",
            ""Description"": ""14G specific features""
          }
        },
        {
          ""Id"": 367,
          ""CapabilityType"": {
            ""CapabilityId"": 16,
            ""Name"": ""VIRTUAL_CONSOLE"",
            ""Description"": ""Ability to execute RACADM tasks""
          }
        },
        {
          ""Id"": 368,
          ""CapabilityType"": {
            ""CapabilityId"": 15,
            ""Name"": ""TEMP_HISTORY"",
            ""Description"": ""Retrieve historical temperature data""
          }
        },
        {
          ""Id"": 369,
          ""CapabilityType"": {
            ""CapabilityId"": 14,
            ""Name"": ""POWER_HISTORY"",
            ""Description"": ""Retrieve historical power data""
          }
        },
        {
          ""Id"": 370,
          ""CapabilityType"": {
            ""CapabilityId"": 13,
            ""Name"": ""TSR"",
            ""Description"": ""Tech Support Report""
          }
        },
        {
          ""Id"": 371,
          ""CapabilityType"": {
            ""CapabilityId"": 12,
            ""Name"": ""DIAGS"",
            ""Description"": ""Diagnostics""
          }
        },
        {
          ""Id"": 372,
          ""CapabilityType"": {
            ""CapabilityId"": 11,
            ""Name"": ""HW_LOGS "",
            ""Description"": ""System Hardware logs""
          }
        },
        {
          ""Id"": 373,
          ""CapabilityType"": {
            ""CapabilityId"": 9,
            ""Name"": ""BLINK"",
            ""Description"": ""Identify function on server""
          }
        },
        {
          ""Id"": 374,
          ""CapabilityType"": {
            ""CapabilityId"": 41,
            ""Name"": ""SHARED_STORAGE_ALLLOWED"",
            ""Description"": ""Capability to share externally assigned Storage""
          }
        },
        {
          ""Id"": 375,
          ""CapabilityType"": {
            ""CapabilityId"": 8,
            ""Name"": ""FW_UPDATE"",
            ""Description"": ""Remote Firmware update capability. ""
          }
        },
        {
          ""Id"": 376,
          ""CapabilityType"": {
            ""CapabilityId"": 7,
            ""Name"": ""CONFIGURE"",
            ""Description"": ""Set attributes on the system""
          }
        },
        {
          ""Id"": 377,
          ""CapabilityType"": {
            ""CapabilityId"": 4,
            ""Name"": ""SENSOR_DETAILS"",
            ""Description"": ""Get Sensor Info, sub system health details""
          }
        },
        {
          ""Id"": 378,
          ""CapabilityType"": {
            ""CapabilityId"": 3,
            ""Name"": ""POWER_CONTROL_RESET"",
            ""Description"": ""Power reset hard/graceful""
          }
        },
        {
          ""Id"": 379,
          ""CapabilityType"": {
            ""CapabilityId"": 2,
            ""Name"": ""POWER_CONTROL_OFF"",
            ""Description"": ""Power Down hard/graceful""
          }
        },
        {
          ""Id"": 380,
          ""CapabilityType"": {
            ""CapabilityId"": 1,
            ""Name"": ""POWER_CONTROL_ON"",
            ""Description"": ""Power up""
          }
        },
        {
          ""Id"": 381,
          ""CapabilityType"": {
            ""CapabilityId"": 32,
            ""Name"": ""REMOTE_SSH"",
            ""Description"": ""14G specific features""
          }
        },
        {
          ""Id"": 382,
          ""CapabilityType"": {
            ""CapabilityId"": 31,
            ""Name"": ""REMOTE_IPMI"",
            ""Description"": ""14G specific features""
          }
        },
        {
          ""Id"": 383,
          ""CapabilityType"": {
            ""CapabilityId"": 30,
            ""Name"": ""REMOTE_RACADM"",
            ""Description"": ""14G specific features""
          }
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('deviceFru')"",
      ""InventoryType"": ""deviceFru"",
      ""InventoryInfo"": [
        {
          ""Id"": 38,
          ""Manufacturer"": ""Dell Inc."",
          ""Name"": ""SystemPlanar""
        },
        {
          ""Id"": 37,
          ""Manufacturer"": ""Micron Technology"",
          ""Name"": ""DDR4 DIMM"",
          ""PartNumber"": ""9ASF1G72PZ-2G6D1"",
          ""SerialNumber"": ""1446F14C""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('deviceLocation')"",
      ""InventoryType"": ""deviceLocation"",
      ""InventoryInfo"": [
        {
          ""Id"": 19,
          ""Rack"": ""78"",
          ""Aisle"": ""Lakeln"",
          ""Datacenter"": ""AlladiaAnoop""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('deviceManagement')"",
      ""InventoryType"": ""deviceManagement"",
      ""InventoryInfo"": [
        {
          ""ManagementId"": 5003,
          ""IpAddress"": ""xx.xx.xx.xx"",
          ""MacAddress"": ""18:66:da:a6:c2:1d"",
          ""ManagementType"": {
            ""Name"": ""PUBLIC"",
            ""Description"": ""Public Management Interface"",
            ""ManagementType"": 2
          },
          ""InstrumentationName"": ""WIN-02GODDHDJTC"",
          ""DnsName"": ""iDRAC-SVCTG06"",
          ""EndPointAgents"": [
            {
              ""ManagementProfileId"": 5003,
              ""ProfileId"": """",
              ""AgentName"": ""iDRAC"",
              ""Version"": ""3.20.20.20"",
              ""ManagementURL"": ""https://[ IP Address ]"",
              ""Status"": 1000,
              ""StatusDateTime"": ""2020-09-28 15:00:00.000""
            }
          ]
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('deviceSoftware')"",
      ""InventoryType"": ""deviceSoftware"",
      ""InventoryInfo"": [
        {
          ""Version"": ""0"",
          ""InstallationDate"": ""2017-06-16T00:54:12Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""APAC"",
          ""ComponentId"": ""25806"",
          ""DeviceDescription"": ""Diagnostics"",
          ""InstanceId"": ""DCIM:INSTALLED#802__Diagnostics.Embedded.1:LC.Embedded.1""
        },
        {
          ""Version"": ""0"",
          ""InstallationDate"": ""2017-06-16T00:54:12Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""APAC"",
          ""ComponentId"": ""18981"",
          ""DeviceDescription"": ""OS Drivers Pack"",
          ""InstanceId"": ""DCIM:INSTALLED#802__DriverPack.Embedded.1:LC.Embedded.1""
        },
        {
          ""Version"": ""3.20.20.20"",
          ""InstallationDate"": ""NA"",
          ""Status"": ""Available"",
          ""SoftwareType"": ""FRMW"",
          ""ComponentId"": ""25227"",
          ""DeviceDescription"": ""Integrated Dell Remote Access Controller"",
          ""InstanceId"": ""DCIM:PREVIOUS#iDRAC.Embedded.1-1#IDRACinfo""
        },
        {
          ""Version"": ""0.3.0"",
          ""InstallationDate"": ""2017-06-16T01:22:28Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""BIOS"",
          ""ComponentId"": ""159"",
          ""DeviceDescription"": ""BIOS"",
          ""InstanceId"": ""DCIM:INSTALLED#741__BIOS.Setup.1-1""
        },
        {
          ""Version"": ""NA03"",
          ""InstallationDate"": ""2017-07-21T03:30:51Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""FRMW"",
          ""ComponentId"": ""104258"",
          ""DeviceDescription"": ""Disk 0 on Embedded AHCI Controller 2"",
          ""InstanceId"": ""DCIM:INSTALLED#304_C_Disk.Direct.0-0:AHCI.Embedded.2-1""
        },
        {
          ""Version"": ""3.20.20.20"",
          ""InstallationDate"": ""2017-06-16T00:54:04Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""FRMW"",
          ""ComponentId"": ""25227"",
          ""DeviceDescription"": ""Integrated Dell Remote Access Controller"",
          ""InstanceId"": ""DCIM:INSTALLED#iDRAC.Embedded.1-1#IDRACinfo""
        },
        {
          ""Version"": ""0"",
          ""InstallationDate"": ""2017-06-16T00:54:12Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""APAC"",
          ""ComponentId"": ""101734"",
          ""DeviceDescription"": ""OS Collector"",
          ""InstanceId"": ""DCIM:INSTALLED#802__OSCollector.Embedded.1""
        },
        {
          ""Version"": ""0"",
          ""InstallationDate"": ""2017-06-16T00:54:12Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""APAC"",
          ""ComponentId"": ""104684"",
          ""DeviceDescription"": ""iDRAC Service Module Installer"",
          ""InstanceId"": ""DCIM:INSTALLED#802__ServiceModule.Embedded.1""
        },
        {
          ""Version"": ""3.20.20.20"",
          ""InstallationDate"": ""2017-06-16T00:54:12Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""APAC"",
          ""ComponentId"": ""28897"",
          ""DeviceDescription"": ""Lifecycle Controller"",
          ""InstanceId"": ""DCIM:INSTALLED#802__USC.Embedded.1:LC.Embedded.1""
        },
        {
          ""Version"": ""0.2.1"",
          ""InstallationDate"": ""2017-06-16T00:54:12Z"",
          ""Status"": ""Installed"",
          ""SoftwareType"": ""FRMW"",
          ""ComponentId"": ""27763"",
          ""DeviceDescription"": ""System CPLD"",
          ""InstanceId"": ""DCIM:INSTALLED#803__CPLD.Embedded.1""
        }
      ]
    },
    {
      ""@odata.id"": ""/api/DeviceService/Devices(3315)/InventoryDetails('subsystemRollupStatus')"",
      ""InventoryType"": ""subsystemRollupStatus"",
      ""InventoryInfo"": [
        {
          ""Id"": 145,
          ""Status"": 2000,
          ""SubsystemName"": ""cpuRollupStatus""
        },
        {
          ""Id"": 146,
          ""Status"": 1000,
          ""SubsystemName"": ""sysMemPrimaryStatus""
        },
        {
          ""Id"": 147,
          ""Status"": 1000,
          ""SubsystemName"": ""voltRollupStatus""
        },
        {
          ""Id"": 148,
          ""Status"": 1000,
          ""SubsystemName"": ""batteryRollupStatus""
        },
        {
          ""Id"": 149,
          ""Status"": 3000,
          ""SubsystemName"": ""licensingRollupStatus""
        },
        {
          ""Id"": 150,
          ""Status"": 1000,
          ""SubsystemName"": ""storageRollupStatus""
        },
        {
          ""Id"": 151,
          ""Status"": 1000,
          ""SubsystemName"": ""tempRollupStatus""
        },
        {
          ""Id"": 152,
          ""Status"": 2000,
          ""SubsystemName"": ""intrusionRollupStatus""
        }
      ]
    }
  ]
}"
```
<!-- type: tab-end -->


The table below describes the attributes returned in detail:

| **Attribute name**             | **Attribute subtype**       | **Type** | **Description**                                                                                                                                         |
|--------------------------------|-----------------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **DeviceId**                   |                             | `Integer`  | The ID of the device                                                                                                                                    |
| **Count**                      |                             | `Integer`  | Indicates the count of inventory details responses                                                                                                      |
|                                | **inventoryDetail**             |          | The list of inventory details                                                                                                                           |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
| **ServerDeviceCards**          |                             |          | The inventory details of the serverdevice (Derived from `/api/DeviceService/Devices(Id)/InventoryDetails('serverDeviceCards')`)                         |
|                                | **DeviceId**                    | `Integer`  | The ID of the device                                                                                                                                    |
|                                | **DeviceCardIndex**             | `Integer`  | Device card index                                                                                                                                       |
|                                | **SlotNumber**                  | `String`   | Slot number of server device card                                                                                                                       |
|                                | **Manufacturer**                | `String`   | Manufacturer of server device card                                                                                                                      |
|                                | **Description**                 | `String`   | Description of server device card                                                                                                                       |
|                                | **DatabusWidth**                | `String`   | Databus width of server device card                                                                                                                     |
|                                | **SlotLength**                  | `String`   | Slot length of server device card                                                                                                                       |
|                                | **SlotType**                    | `String`   | Slot type of server device card                                                                                                                         |
| **ServerProcessors**           |                             |          | The inventory details of the Server processors (Derived from `/api/DeviceService/Devices(3315)/InventoryDetails('serverProcessors')`)                   |
|                                | **id**                          | `Integer`  | ID of server processor                                                                                                                                  |
|                                | **deviceId**                    | `Integer`  | ID of device                                                                                                                                            |
|                                | **family**                      | `String`   | Family of server processor                                                                                                                              |
|                                | **maxSpeed**                    | `Integer`  | Max speed of server processor                                                                                                                           |
|                                | **currentSpeed**                | `Integer`  | Current speed of server processor                                                                                                                       |
|                                | **slotNumber**                  | `String`   | Slot number of server processor                                                                                                                         |
|                                | **status**                      | `Long`     | Status of server processor                                                                                                                              |
|                                | **numberOfCores**               | `Integer`  | Internal reference to the actual component                                                                                                              |
|                                | **numberOfEnabledCores**        | `Integer`  | Number of enabled cores of server processor                                                                                                             |
|                                | **brandName**                   | `String`   | Brand name of server processor                                                                                                                          |
|                                | **modelName**                   | `String`   | Model name of server processor                                                                                                                          |
|                                | **steppingName**                | `String`   | Stepping name of server processor                                                                                                                       |
|                                | **instanceId**                  | `String`   | Instance Id of server processor                                                                                                                         |
|                                | **voltage**                     | `String`   | Voltage of server processor                                                                                                                             |
| **ServerNetworkInterfaces**    |                             |          | The inventory details of the Server Network Interfaces (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('serverNetworkInterfaces')`)      |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
| **ServerOperatingSystem**      |                             |          | The inventory details of the Server Operating System (Derived from `/api/DeviceService/Devices(3315)/InventoryDetails('serverOperatingSystem')`)        |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Id**                          | `Integer`  | ID of server operating system                                                                                                                           |
|                                | **hostname**                    | `String`   | Host name of operating system                                                                                                                           |
| **ServerVirtualFlashes**       |                             |          | The inventory details of the Server Virtual Flashes (Derived from `api/DeviceService/Devices(3315)/InventoryDetails('serverVirtualFlashes')`)           |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
| **ServerPowerSupplies**        |                             |          | The inventory details of the Server Power Supplies (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('serverPowerSupplies')`)              |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
| **ServerArrayDisks**           |                             |          | The inventory details of the Server Array Disks (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('serverArrayDisks')`)                    |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **id**                          | `Integer`  | ID of server disk                                                                                                                                       |
|                                | **deviceId**                    | `Integer`  | ID of device                                                                                                                                            |
|                                | **diskNumber**                  | `String`   | Disk number                                                                                                                                             |
|                                | **vendorName**                  | `Integer`  | Vendor name of server disk                                                                                                                              |
|                                | **status**                      | `Integer`  | Status of server array                                                                                                                                  |
|                                | **statusString**                | `String`   | Status of server disk                                                                                                                                   |
|                                | **modelNumber**                 | `String`   | Model number of server disk.                                                                                                                            |
|                                | **serialNumber**                | `String`   | Serial number of server disk                                                                                                                            |
|                                | **sasAddress**                  | `String`   | SAS address of server disk                                                                                                                              |
|                                | **revision**                    | `String`   | Revision of server disk                                                                                                                                 |
|                                | **enclosureId**                 | `String`   | D of enclosure                                                                                                                                          |
|                                | **channel**                     | `Integer`  | Channel number                                                                                                                                          |
|                                | **size**                        | `String`   | Size of server disk                                                                                                                                     |
|                                | **freeSpace**                   | `String`   | Free space on server disk                                                                                                                               |
|                                | **usedSpace**                   | `String`   | Used space on server disk                                                                                                                               |
|                                | **busType**                     | `String`   | us type of server disk                                                                                                                                  |
|                                | **slotNumber**                  | `Integer`  | Slot number of server disk                                                                                                                              |
|                                | **mediaType**                   | `String`   | Media type of server disk                                                                                                                               |
|                                | **remainingReadWriteEndurance** | `String`   | Remaining read write endurance                                                                                                                          |
|                                | **securityState**               | `String`   | Security state of server disk                                                                                                                           |
| **serverRaidControllers**      |                             |          | The inventory details of the Server Raid Controllers (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('serverRaidControllers')`)          |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **id**                          | `Integer`  | ID of server RAID controller                                                                                                                            |
|                                | **deviceId**                    | `Integer`  | ID of device                                                                                                                                            |
|                                | **name**                        | `String`   | Name of server RAID controller                                                                                                                          |
|                                | **fqdd**                        | `String`   | FQDD of server RAID controller                                                                                                                          |
|                                | **deviceDescription**           | `String`   | Description of device                                                                                                                                   |
|                                | **status**                      | `String`   | Status of server RAID controller                                                                                                                        |
|                                | **rollupStatus**                | `Integer`  | Rollup status of server RAID controller                                                                                                                 |
|                                | **cacheSizeInMb**               | `Integer`  | Cache size in MB of server RAID controller                                                                                                              |
|                                | **pciSlot**                     | `String`   | PCI slot of server RAID controller                                                                                                                      |
|                                | **revision**                    | `String`   | Revision of server disk                                                                                                                                 |
|                                | **enclosureId**                 | `String`   | D of enclosure                                                                                                                                          |
|                                | **channel**                     | `Integer`  | Channel number                                                                                                                                          |
|                                | **size**                        | `String`   | Size of server disk                                                                                                                                     |
| **serverMemoryDevices**        |                             |          | The inventory details of the Server Memory Devices (Derived from `/api/DeviceService/Devices(Id)/InventoryDetails('serverMemoryDevices')`)              |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **id**                          | `Integer`  | ID of server memory device                                                                                                                              |
|                                | **deviceId**                    | `Integer`  | ID of device                                                                                                                                            |
|                                | **name**                        | `String`   | Name of server memory device                                                                                                                            |
|                                | **bankName**                    | `String`   | Bank name of server memory device                                                                                                                       |
|                                | **size**                        | `Long`     | Size of server memory device                                                                                                                            |
|                                | **status**                      | `String`   | Status of server memory device                                                                                                                          |
|                                | **manufacturer**                | `String`   | Manufacturer of server memory device                                                                                                                    |
|                                | **partNumber**                  | `Integer`  | Part number of server memory device                                                                                                                     |
|                                | **serialNumber**                | `String`   | Serial number of server memory device                                                                                                                   |
|                                | **typeDetails**                 | String   | Type details of server memory device                                                                                                                    |
|                                | **manufacturerDate**            | `String`   | Date of manufacture                                                                                                                                     |
|                                | **speed**                       | `Integer`  | Speed of server memory device                                                                                                                           |
|                                | **currentOperatingSpeed**       | `Integer`  | Current operating speed of server memory device                                                                                                         |
|                                | **rank**                        | `String`   | Rank of server memory device                                                                                                                            |
|                                | **instanceId**                  | `String`   | Instance ID of server memory device                                                                                                                     |
|                                | **deviceDescription**           | `String`   | Description of server memory device                                                                                                                     |
| **serverStorageEnclosures**    |                             |          | The inventory details of the Server Storage Enclosures (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('serverStorageEnclosures')`)      |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
| **serverSupportedPowerStates** |                             |          | The inventory details of the Server Supported PowerStates(Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('serverSupportedPowerStates')`) |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Id**                          | `Integer`  | ID of server supported power state                                                                                                                      |
|                                | **powerState**                  | `String`   | Power state                                                                                                                                             |
| **deviceLicense**              |                             |          | The inventory details of the Device License (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('deviceLicense')`)                           |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **SoldDate**                    | `String`   | Indicates the sold date of the device license                                                                                                           |
|                                | **LicenseBound**                | `Long`     | Device license bound type                                                                                                                               |
|                                | **EvalTimeRemaining**           | `Integer`  | Indicates the evaluation remaining time of the device license                                                                                           |
|                                | **AssignedDevices**             | `String`   | Indicates the assigned devices to the device license                                                                                                    |
|                                | **LicenseStatus**               | `Integer`  | Indicates the status of the device license                                                                                                              |
|                                | **EntitlementId**               | `String`   | Device license entitlement id                                                                                                                           |
|                                | **LicenseDescription**          | `String`   | Device license description                                                                                                                              |
|                                | **LicenseType**:                |          | Device license description                                                                                                                              |
|                                | **licenseId**                   | `Integer`  | Licensing type id                                                                                                                                       |
|                                | **name**                        | `String`   | Licensing type name                                                                                                                                     |
| **deviceCapabilities**         |                             |          | The inventory details of the Device Capabilities (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('deviceCapabilities')`)                 |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Id**                          | `Long`     | Device capabilities id                                                                                                                                  |
|                                | **capabilityType**              |          | Indicates the type of capability                                                                                                                        |
|                                | **capabilityId**                | `Integer`  | Capability type id                                                                                                                                      |
|                                | **name**                        | `String`   | Capability type name                                                                                                                                    |
|                                | **description**                 | `String`   | Capability type description                                                                                                                             |
| **deviceFru**                  |                             |          | The inventory details of the Device Fru (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('deviceFru')`)                                   |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Id**                          | `Integer`  | Device field replaceable unit id                                                                                                                        |
|                                | **name**                        | `String`   | Device field replaceable unit name                                                                                                                      |
|                                | **manufacturer**                | `String`   | Device field replaceable unit manufacturer                                                                                                              |
|                                | **partNumber**                  | `String`   | Device field replaceable unit description                                                                                                               |
|                                | **serialNumber**                | `String`   | Device field replaceable unit serial number                                                                                                             |
| **deviceLocation**             |                             |          | The inventory details of the Device Location (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('deviceLocation')`)                         |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Id**                          | `Long`     | Device location ID                                                                                                                                     |
|                                | **datacenter**                  | `String`   | Device location datacenter information                                                                                                                  |
|                                | **aisle**                       | `String`   | Device location aisle information                                                                                                                       |
|                                | **rack**                        | `String`   | Device location rack information                                                                                                                        |
| **deviceManagement**           |                             |          | The inventory details of the Device Management (Derived from `/api/DeviceService/Devices(Id)/InventoryDetails('deviceManagement')`)                     |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **ManagementId**                | `Long`     | Device management id                                                                                                                                    |
|                                | **IpAddress**                   | `String`   | Device management IP address                                                                                                                            |
|                                | **MacAddress**                  | `String`   | Device management MAC address                                                                                                                           |
| **ManagementType**             |                             |          | Indicates the management type for the device management                                                                                                 |
|                                | **Name**                        | `String`   | Management type name                                                                                                                                    |
|                                | **Description**                 | `String`   | Management type description                                                                                                                             |
|                                | **ManagementType**              | `Long`     | Management type id                                                                                                                                      |
|                                | **InstrumentationName**         | `String`   | Indicates the instrumentation name for the device management                                                                                            |
|                                | **DnsName**                     | `String`   | Device management DNS name information                                                                                                                  |
|                                | **EndPointAgents** :            |          | Indicates the profile associated with the device management                                                                                             |
|                                | **ManagementProfileId**         | `Long`     | Management profile id                                                                                                                                   |
|                                | **ProfileId**                   | `String`   | Indicates the profile id associated with the management profile                                                                                         |
|                                | **AgentName**                   | `String`   | Management profile agent name information                                                                                                               |
|                                | **Version**                     | `String`   | Management profile version information                                                                                                                  |
|                                | **ManagementURL**               | `String`   | Indicates the management URL for the management profile                                                                                                 |
|                                | **Status**                      | `Long`     | Indicates the status of the management profile                                                                                                          |
|                                | **StatusDateTime**              | `String`   | The management profile status time                                                                                                                      |
| **deviceSoftware**             |                             |          | The inventory details of the Device Software (Derived from `/api/DeviceService/Devices(3315)/InventoryDetails('deviceSoftware')`)                       |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Version**                     | `String`   | Device software version                                                                                                                                 |
|                                | **InstallationDate**            | `String`   | Device software installation date                                                                                                                       |
|                                | **Status**                      | `String`   | Indicates the status of the device software                                                                                                             |
|                                | **SoftwareType**                | `String`   | Indicates the type of device software                                                                                                                   |
|                                | **ComponentId**                 | `String`   | Device software component id                                                                                                                            |
|                                | **DeviceDescription**           | `String`   | Device software additional device description                                                                                                           |
|                                | **InstanceId**                  | `String`   | Device software instance identifier                                                                                                                     |
|                                |                             |          |                                                                                                                                                         |
| **subsystemRollupStatus**      |                             |          | The inventory details of the Sub System Rollup Status (Derived from `/api/DeviceService/Devices(ID)/InventoryDetails('subsystemRollupStatus')`)         |
|                                | **InventoryType**               | `String`   | Indicates the type of inventory                                                                                                                         |
|                                | **InventoryInfo**               |          | Indicates the inventory information details                                                                                                             |
|                                | **Id**                          | `Integer`  | Subsystem rollup status id                                                                                                                              |
|                                | **Status**                      | `Integer`  | Indicates the status of the subsystem rollup status                                                                                                     |
|                                | **SubsystemName**               | `String`   | Subsystem rollup status name                                                                                                                            |

> You can also retrieve the inventory by filtering on `InventoryTypes`.


## Get the inventory types
To retrieve a list of the inventory types, send a **GET** request to `/api/DeviceService/Devices(Id)/InventoryTypes`.

Response code | Description | 
---------|----------|
 **200** | Success | 


Example response:

<!-- 
type: tab
title: JSON
-->

```json

{
 "@odata.context": "$metadata#DeviceService.InventoryTypes/$entity",
 "@odata.id": "/api/DeviceService/Devices(3265)/InventoryTypes",
 "InventoryTypes": [
  "serverDeviceCards",
  "serverProcessors",
  "serverNetworkInterfaces",
  "serverOperatingSystems",
  "serverVirtualFlashes",
  "serverPowerSupplies",
  "serverArrayDisks",
  "serverRaidControllers",
  "serverMemoryDevices",
  "serverStorageEnclosures",
  "serverSupportedPowerStates",
  "deviceLicense",
  "deviceCapabilities",
  "deviceFru",
  "deviceManagement",
  "deviceSoftware",
  "subsystemRollupStatus"
 ]
}

```

<!-- type: tab-end -->

## Get the filtered inventory details of a device
Retrieve the inventory details of a device based on the individual filters in the inventory response.

