# Filtering devices

The console software provides filtering options on certain URIs that return a collection of entities. Using this feature, the clients can extract a selected set of records using comparison operators on attributes of the model entity behind the collection. The relevant URI sections contain the information about the attributes and the operators that support filtering.

```json
<Collection API>?$filter=<attribute> <operator> <value>&<attribute> <operator> <value>
```

## Filtering devices by system ID

You can filter devices by system ID, for example, by running: 

```json
/api/DeviceService/Devices?$filter=SystemId eq 123
```

## Filtering devices by model
You can filter devices by model, for example, by running: 
```json
/api/DeviceService/Devices?$filter=Model eq 'PowerEdge MX740c'
```

## Filtering devices by device type
You can filter devices by device type, for example, by running: 
```json
/api/DeviceService/Devices?$filter=Type eq 3000 or Type eq 5000
```

> For enumeration of `Type`, see `/api/DeviceService/DeviceType`.

For more information about `$filter`, see the [OData Version 4.0 Part 1: Protocol](https://docs.oasis-open.org/).