# Response headers

Here are a few examples of response headers in the OpenManage Enterprise API:

| Response header |  Description |  Example |
| ------: | :------------------: | :-----------  |
| **Connection**| Control options for the current connection and list of hop-by-hop request fields. | `Connection: Keep-Alive` |
| **Content-Type**| Specifies the format of the content that the server returns. If there are multiple formats that can be accepted in the client request (using the Accept header), the server chooses the appropriate supported format. | `Content-Type: application/json; odata.metadata=minimal` |
| **Keep-alive**| `Timeout` header parameter indicates the time that a connection is allowed to remain idle before it is closed. `Max` header parameter indicates the maximum number of requests that are permitted before the connection is closed. | `Timeout=5; max=150` |
| **Content-length**| The length of the request body in 8-bit bytes or octets. | `Content-Length: 348` |
| **date**| The date and time that the message originated, in HTTP-date format as defined by RFC 7231 Date/Time Formats. | `date: Thu, 02 Apr 2009 11:11:28 GMT` |
| **Odata-version**| The version of Odata that is used. | `Odata: 4.0` |
| **Location**| Used in redirection or when a new resource is created. | `Location: <BASE_URI>/SessionService/ Sessions('3204bb9d-409d-4bd9-8a5f- d44005c81a2c')` |
| **Server**| A name for the server. | `Server: Apache` |
| **x-frame-options**| Clickjacking protection: can be `deny`; no rendering within a frame, or `sameorigin`; no rendering if origin mismatch, or `allow-from`; allow from specified location, or `allowall`; non-standard, allow from any location | `DENY` |