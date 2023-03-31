# Base URIs

The console software provides a web-based hypermedia driven API using a simple folder structure. The client that is provided with the Service Root URI navigates through the entire resource tree through the links in the response payload on the individual URIs. 

The following schemes are provided for the service root URI:
- OEM scheme
- Redfish scheme

## OEM scheme
```json
https://[IP or DNS name]/api

```


| Service Root URI | Resources
|----------:|:---------
| `/api/DeviceService` | Devices, DeviceStatuses, DeviceType, etc.
| `/api/SearchService` | SearchService
| `/api/ApplicationService` | Settings, Certificate, Network, Security
| `/api/AlertService` | Alerts, AlertCatalogs, AlertStatuses, etc.
| `/api/SessionService` | Sessions
| `/api/JobService` | Job, JobTypes, JobStatuses
| `/api/GroupService` | Groups, GroupTypes, etc.
| `/api/UpdateService` | Catalogs, Baselines, RollbackSoftwares, ComplianceStatuses
| `/api/AccountService` | Accounts, Roles
| `/api/NetworkConfigurationService` | Networks, NetworkTypes, QosTypes
| `/api/ManagementDomainService` | Domains, DiscoverDomains, etc.
| `/api/NetworkService` | Fabrics, FabricDesigns, UplinkTypes, etc.
| `/api/TemplateService` | Templates, Profiles, TemplateTypes, etc.
| `/api/IdentityPoolService` | IdentityPools, IdentityStatuses
| `/api/TroubleshootService` | Files
| `/api/DiscoveryConfigService` | /api/DiscoveryConfigService
| `/api/QuerySupportService` | /api/QuerySupportService
| `/api/ReportService` | /api/ReportService
| `/api/WarrantyService` | /api/WarrantyService
| `/api/MIBImportService` | /api/MIBImportService

### OEM resource model

```json
https://[IP or DNS name]/api/$metadata
```
## Redfish scheme
```json
https://[IP or DNS name]/redfish/v1

```


|  Service Root URI              |  Resources                               |  Description                                                |
|--------------------------------|------------------------------------------|-------------------------------------------------------------|
|  `/redfish/v1/Systems`         |  `/redfish/v1/Systems/Members(<id>)`     |  General system status, Memory, Storage,   NICs, Processors |
|  `/redfish/v1/Chassis`         |  `/redfish/v1/Chassis/Members(<id>)`     |  General information, Power, Thermal                        |
|  `/redfish/v1/Registries`      |  `/redfish/v1/Registries/Members(<id>)`  |  Message registry file collection                           |
|  `/redfish/v1/SessionService`  |  `/redfish/v1/SessionService/Sessions`   |  Sessions collection                                        |
|  `/redfish/v1/AccountService`  |  `/redfish/v1/AccountService/Accounts`   |  Accounts collection                                        |
|  `/redfish/v1/AccountService`  |  `/redfish/v1/AccountService/Roles`      |  Roles collection                                           |

> The current version of the console supports the DMTF-defined schema for a selected set of resources in the hierarchy. 

### Redfish resource model

```json
https://[IP or DNS name]/redfish/v1/$metadata

```
