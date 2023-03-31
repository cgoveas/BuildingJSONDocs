# Paginating data

The console software provides pagination options on certain URIs that return a collection of entities. The pagination options enable the clients to get paginated results. If a URI supports pagination, the relevant URI sections indicate it. For example, to get the 5th to 8th devices from the Device collection, run:
``` 
GET /api/DeviceService/Devices?$skip=4&$top=4
```
To get the first four devices from the Device collection, run:

```
GET /api/DeviceService/Devices?$top=4
```

> For more information about `$skip` and `$top`, see http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html.