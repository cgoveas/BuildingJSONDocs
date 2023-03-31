# Addressing resources

Each managed resource must be uniquely addressable using a distinct URI. The URI syntax must be intuitive and should indicate the relationships with a parent resource. There are several ways to address specific resources either as instances of specific resource classes or within an associated parent context. 
Resource URIs have the following format:

```json
    <BASE_URI>/<resource class identifier>/<resource instance identifier>
```

Example resource URI, where `1234` is the unique identifier of the device:

```json
    <BASE_URI>/DeviceService/Devices(1234)
```