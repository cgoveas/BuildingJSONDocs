# Performance considerations

The expected time taken to run individual APIs for retrieving data from a database is a few seconds for normal API calls and paginated data. When data is retrieved from multiple servers by enumeration for generating reports, it may take ample time to run multiple iterations with multiple queries per server. The time taken and the utilization of resources is more and may impact the performance of the OpenManage Enterprise appliance. For generating reports, it is recommended to use integrated or custom reports for retrieving information from a large number of servers that are required on a regular basis.

## Best Practices
1. It is not recommended to call APIs in a loop. Doing so will incur significant additional overhead and network delays.  Instead, either use `$top` to retrieve all records at once or create a custom report to provide the data and query the report instead.
2. If `$top` is used to retrieve large volumes of data, particularly all data on a scale configuration, response times will be longer.  In such cases, the response size may also be large (> 100 MB) enough to incur additional network delay.
3. It is not recommended to simultaneously bombard the appliance through API calls from multiple clients for large amounts of data.  This will incur additional CPU, memory, and disk impact and will need throttling based on the scale of the appliance.
4. Certain APIs have performance limitations.  While these APIs have been maintained for backward compatibility, it is recommended to instead use the more recent and efficient version. Instead of using `DeviceService/Devices` or `GroupService/Groups(id)/AllLeafDevices`, please use `GroupService/Groups(id)/AllLeafDeviceSummaries`.
5. Keep iDRAC firmware up to date to ensure that data such as controller lists and virtual disk lists is consistent.