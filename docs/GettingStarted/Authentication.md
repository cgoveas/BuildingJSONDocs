# Authentication

Several common schemes are available for enabling authentication of REST requests in OpenManage Enterprise. Basic authentication and X-Auth-Token authentication are some of the common schemes. For more information, go to [docs.oasis-open.org](https://docs.oasis-open.org).

## Basic authentication

> Basic authentication is supported only when SSL/TLS is used for the transport.
 
The authorization header in the request has a `base-64` encoding of the `UserName` and `Password`. If you do not provide the credentials, a `401 — Authorization Failure` error is returned. 

## X-Auth-Token authentication

> X-Auth-Token authentication provides a more secure implementation. 

To establish a session, send a **POST** request to the `SessionService` REST API.

<!--
type: tab
title: Request
-->

```json
POST https://[ IP Address ]/api/SessionService/Sessions
Input
{
"UserName":"root",
"Password":"linux",
"SessionType":"API"
}
```
<!--
type: tab
title: Response
-->

```json
connection →Keep-Alive
content-length →268
content-type →application/json; odata.metadata=minimal
date →Tue, 05 Sep 2017 11:55:29 GMT
keep-alive →timeout=5, max=150
location →/api/SessionService/Sessions('e1817fe6-97e5-4ea0-88a9-d865c7302152')
odata-version →4.0
server →Apache
x-auth-token →13bc3f63-9376-44dc-a09f-3a94591a7c5d
x-frame-options →DENY
```
<!-- type: tab-end -->

> The `x-auth-token` is returned in the header. This X-Auth-Token is used in the header for subsequent REST operations and to authenticate the user.

## X-Auth-Token creation
To create an x-auth-token, send a **POST** request to `/api/SessionService/Sessions`. If the return code is `201` update headers for subsequent commands, using the `x-auth-token` in the returned response headers.

Response code | Description | 
---------|----------|
 **201** | Success | 
 

```json
{
  "UserName": "xxx",
  "Password": "yyy",
  "SessionType": "API"
}
```

| Attribute name  |      Type     |  Description |
| --------------: | :-----------: | :------------|
|**UserName** | `String`| The Session user name|
|**Password** | `String`| The session user password |
|**SessionType** | `String`| The type of session being created|


Example response:

```json
{
 "Id": "7b723932-a119-4843-a5b7-7a122e4def99",
 "Description": "root",
 "Name": "API",
 "UserName": "root",
 "Password": null,
 "Roles": [
 "ADMINISTRATOR"
 ],
 "IpAddress": "xx.xx.xx.xx",
 "StartTimeStamp": "2017-04-17 20:07:12.357",
 "LastAccessedTimeStamp": "2017-04-17 20:07:12.357"
}

```

| Attribute name  |      Type     |  Description |
| --------------: | :-----------: | :------------|
|**Id** | `String` | The unique identifier of the SessionService|
|**UserName** | `String` | The session user name |
|**Password** | `String` | The session user password|
|**Roles** | `String` | Role privileges|
|**IpAddress** | `String` | IP address of session source|
|**StartTimeStamp** | `String` | Time when the session began|
|**LastAccessedTimeStamp** | `String` | Time when the session was last accessed| 
|**Description** | `String` | Description for the user session|


<!--
type: tab
title: Python
-->

```python
def authenticate(ome-m_ip_address: str, ome-m_username: str, ome-m_password: str) -> dict:
    """
    Authenticates with OME-M and creates a session

    Args:
        ome-m_ip_address: IP address of the OME-M server
        ome-m_username:  Username for OME-M
        ome-m_password: OME-M password

    Returns: A dictionary of HTTP headers

    Raises:
        Exception: A generic exception in the event of a failure to connect
    """

    authenticated_headers = {'content-type': 'application/json'}
    session_url = 'https://%s/api/SessionService/Sessions' % ome-m_ip_address
    user_details = {'UserName': ome-m_username,
                    'Password': ome-m_password,
                    'SessionType': 'API'}
    try:
        session_info = requests.post(session_url, verify=False,
                                        data=json.dumps(user_details),
                                        headers=authenticated_headers)
    except requests.exceptions.ConnectionError:
        print("Failed to connect to OME-M. This typically indicates a network connectivity problem. Can you ping OME-M?")
        sys.exit(0)

    if session_info.status_code == 201:
        authenticated_headers['X-Auth-Token'] = session_info.headers['X-Auth-Token']
        return authenticated_headers
    
    print("There was a problem authenticating with OME-M. Are you sure you have the right username, password, "
        "and IP?")
    raise Exception("There was a problem authenticating with OME-M. Are you sure you have the right username, "
                    "password, and IP?")

```

<!--
type: tab
title: PowerShell 5.x
-->

```powershell
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
    Catch {
        Write-Error "Unable to add type for cert policy"
    }
}
## Assume the variable $Credentials contains user input
Set-CertPolicy
$SessionUrl = "https://$($IpAddress)/api/SessionService/Sessions"
$Type = "application/json"
$UserName = $Credentials.username
$Password = $Credentials.GetNetworkCredential().password
$UserDetails = @{"UserName" = $UserName; 
                 "Password" = $Password;
                 "SessionType" = "API"} | ConvertTo-Json
$Headers = @{}

$SessResponse = Invoke-WebRequest -Uri $SessionUrl -Method Post -Body $UserDetails -ContentType $Type
if ($SessResponse.StatusCode -eq 201) {
    $Headers."X-Auth-Token" = $SessResponse.Headers["X-Auth-Token"]
}

```

<!-- type: tab-end -->

## Get all active sessions
To retrieve all of the active sessions, send a **GET** request to `/api/SessionService/Sessions`. 

Response code | Description | 
---------|----------|
 **200** | Success | 


Example response:

```json
{
 "@odata.context": "$metadata#Collection(SessionService.Session)",
 "@odata.count": 1,
 "value": [
 {
 "@odata.id": "/api/SessionService/Sessions('7b723932-a119-4843-
a5b7-7a122e4def99')",
 "Id": "7b723932-a119-4843-a5b7-7a122e4def99",
 "Description": "root",
 "Name": "API",
 "UserName": "root",
 "Password": null,
 "Roles": [
 "ADMINISTRATOR"
 ],
 "IpAddress": "xx.xx.xx.xx",
 "StartTimeStamp": "2017-04-17 20:07:12.357",
 "LastAccessedTimeStamp": "2017-04-17 20:07:45.482"
 }
 ]
}

```
| Attribute name  |      Type     |  Description |
| --------------: | :-----------: | :------------|
|**Id** | `String`| The unique identifier of the SessionService|
|**UserName** | `String`| The session user name |
|**Password** | `String`| The session user password|
|**Roles** | `String`| Role privileges|
|**IpAddress** | `String`| IP address of session source|
|**StartTimeStamp** | `String`| Time when the session began|
|**LastAccessedTimeStamp** | `String`| Time when the session was last accessed| 
|**Description** | `String` | Description for the user session|


## Get a user's session
To retrieve an instance of a session associated with a user's ID, send a **GET** request to `/api/SessionService/Sessions(Id)`.

Response code | Description | 
---------|----------|
 **200** | Success | 


Example response:

```json
 {
 "Id": "7b723932-a119-4843-a5b7-7a122e4def99",
 "Description": "root",
 "Name": "API",
 "UserName": "root",
 "Password": null,
 "Roles": [
 "ADMINISTRATOR"
 ],
 "IpAddress": "xx.xx.xx.xx",
 "StartTimeStamp": "2017-04-17 20:07:12.357",
 "LastAccessedTimeStamp": "2017-04-17 20:07:12.357"
}

```
| Attribute name  |      Type     |  Description |
| --------------: | :-----------: | :------------|
|**Id** | `String`| The unique identifier of the SessionService|
|**UserName** | `String`| The session user name |
|**Password** | `String`| The session user password|
|**Roles** | `String`| Role privileges|
|**IpAddress** | `String`| IP address of session source|
|**StartTimeStamp** | `String`| Time when the session began|
|**LastAccessedTimeStamp** | `String`| Time when the session was last accessed| 
|**Description** | `String` | Description for the user session|

## Delete a session
To delete a specific session, send a **DELETE** request to `/api/SessionService/Sessions(arg1)`. 

Response code | Description | 
---------|----------|
 **204** | Success | 


## Retrieve a session's configuration
To retrieve the session configuration, send a **GET** request to `api/SessionService/SessionConfiguration`.

Response code | Description | 
---------|----------|
 **200** | Success | 

Example response:

```json
"{
    ""@odata.context"": ""/api/$metadata#Collection(SessionService.SessionConfiguration)"",
    ""@odata.count"": 5,
    ""value"": [
        {
            ""@odata.type"": ""#SessionService.SessionConfiguration"",
            ""SessionType"": ""GUI"",
            ""MaxSessions"": 6,
            ""SessionTimeout"": 1800000,
            ""MinSessionTimeout"": 60000,
            ""MaxSessionTimeout"": 7200000,
            ""MinSessionsAllowed"": 1,
            ""MaxSessionsAllowed"": 6,
            ""MaxSessionsConfigurable"": true,
            ""SessionTimeoutConfigurable"": true
        },
        {
            ""@odata.type"": ""#SessionService.SessionConfiguration"",
            ""SessionType"": ""API"",
            ""MaxSessions"": 100,
            ""SessionTimeout"": 1800000,
            ""MinSessionTimeout"": 60000,
            ""MaxSessionTimeout"": 86400000,
            ""MinSessionsAllowed"": 1,
            ""MaxSessionsAllowed"": 100,
            ""MaxSessionsConfigurable"": true,
            ""SessionTimeoutConfigurable"": true
        },
        {
            ""@odata.type"": ""#SessionService.SessionConfiguration"",
            ""SessionType"": ""UniversalTimeout"",
            ""MaxSessions"": 0,
            ""SessionTimeout"": -1,
            ""MinSessionTimeout"": -1,
            ""MaxSessionTimeout"": 86400000,
            ""MinSessionsAllowed"": 0,
            ""MaxSessionsAllowed"": 0,
            ""MaxSessionsConfigurable"": false,
            ""SessionTimeoutConfigurable"": true
        },
        {
            ""@odata.type"": ""#SessionService.SessionConfiguration"",
            ""SessionType"": ""Serial"",
            ""MaxSessions"": 1,
            ""SessionTimeout"": 1800000,
            ""MinSessionTimeout"": 60000,
            ""MaxSessionTimeout"": 86400000,
            ""MinSessionsAllowed"": 1,
            ""MaxSessionsAllowed"": 1,
            ""MaxSessionsConfigurable"": false,
            ""SessionTimeoutConfigurable"": true
        },
        {
            ""@odata.type"": ""#SessionService.SessionConfiguration"",
            ""SessionType"": ""SSH"",
            ""MaxSessions"": 4,
            ""SessionTimeout"": 1800000,
            ""MinSessionTimeout"": 60000,
            ""MaxSessionTimeout"": 10800000,
            ""MinSessionsAllowed"": 1,
            ""MaxSessionsAllowed"": 4,
            ""MaxSessionsConfigurable"": true,
            ""SessionTimeoutConfigurable"": true
        }
    ]
}"

```

| Attribute name  |      Type     |  Description |
| --------------: | :-----------: | :------------|
| **SessionType** | `String`| The session user name|
| **MaxSessions** | `Long`| Maximum session for this session type |
| **SessionTimeout** | `Long`| session timeout value for this session type|
| **MinSessionTimeout** | `Long`| Minimum session time for this session type|
| **MaxSessionTimeout** | `Long`| Maximum session time this session type |
| **MinSessionsAllowed** | `Long`| Minimum sessions allowed for this session type |
| **MaxSessionsAllowed** | `Long`| Maximum sessions allowed for this session type|
| **MaxSessionsConfigurable** | `Boolean`| Boolean value if maximum sessions can be configured |
| **SessionTimeoutConfigurable** | `Boolean`| Boolean value if session timeout can be configured |
