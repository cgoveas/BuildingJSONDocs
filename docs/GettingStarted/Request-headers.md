# Request headers

The request header represents headers in the client HTTPS request that are used to communicate client-preferences to the service endpoint. The service provides the supported preference in the response header. The following table contains a few request header examples.

Header | Description | Example
---------:|:----------|:---------
 **x-auth-token** | Authentication token taken from the return header of the SessionService/Sessions POST operation to create a session. | `x-auth-token: d6399a19-38c8-467ba1ec-75ffa03efb7c`
 **Accept-Language** | Choice of language that the client can request - Optional | `Accept-Language: en`
