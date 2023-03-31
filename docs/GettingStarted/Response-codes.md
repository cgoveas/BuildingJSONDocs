# Response codes

For synchronous operations, the server returns HTTP response codes `200` or `204`. For operations that take a long time, the server returns a status code of `202` along with an HTTP response header (Location). This response corresponds to the `URI` of the temporary resource that can be used to monitor the operation.

> For more information about response codes, see **Status Code Definitions** on the [W3 website](https://www.w3.org/).

## Success codes

| Request type |  Response code |  Description |
| ------: | :------------------: | :-----------  |
| **GET**| `200` | OK with message body. |
| | `204` | OK with no message body. |
| | `206` | OK with partial message body. |
| **POST**| `201` | Resource created (operation complete).|
| | `202` | Resource accepted (operation pending).|
| **PUT** | `202` | Accepted (operation pending).|
| | `204` | Success (operation complete).|
| **DELETE** | `202` | Accepted (operation pending).|
| | `204` | Success (operation complete).|

## Failure codes

Issue | Response code | 
---------:|:----------|
 Invalid parameter | `400` | 
  Authorization failure | `401` | 
  Permission denied | `403` | 
  Resource not found | `404` | 
  Invalid request method | `405` | 
  External server error | `500` | 
  Service unavailable | `503` | 