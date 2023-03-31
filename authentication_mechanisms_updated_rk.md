--- API Authentication in OME-M
tags: [00. OME-M]
---

# API Authentication in OME-M


### OME-M allow two modes of authentication for API calls
1. Basic auth
2. X-Auth tokens (Preferred)

<!-- theme: info -->
> ### Basic Auth
1. This is not the preferred approach for authentication


<!-- theme: info -->
> ### X-Auth Token Creation
> - Perform a POST on `/api/SessionService/Sessions`
> - If the return code is 201 update headers for subsequent commands, using the X-Auth token in the returned response headers
```json
{
  "UserName": "xxx",
  "Password": "yyy",
  "SessionType": "API"
}
```
> The table below describes the attributes in detail

| Attribute Name  |      Type     |  Description |
| -------------- | :-----------: | ----:|
|UserName | String| The Session user name|
|Password | String| The session user password |
|SessionType | String| The type of session being created|


> Example response body for the SessionService

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
> The table below describes the attributes in detail:

| Attribute Name  |      Type     |  Description |
| -------------- | :-----------: | ----:|
|Id | String| The unique identifier of the SessionService|
|UserName | String| The session user name |
|Password | String| The session user password|
|Roles | String| Role privileges|
|IpAddress | String| IP address of session source|
|StartTimeStamp | String| Time when the session began|
|LastAccessedTimeStamp | String| Time when the session was last accessed| 
|Description | String | Description for the user session|


> Code Sample for X-auth session creation
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

![](./sessions.png)

<!-- theme: info -->
> ### Retrieve the active sessions
> - Perform a GET on `/api/SessionService/Sessions`
> - Parse returned response status code and check if it's 200 for success
> - Returns the collection of active sessions
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
> The table below describes the attributes in detail

| Attribute Name  |      Type     |  Description |
| -------------- | :-----------: | ----:|
|Id | String| The unique identifier of the SessionService|
|UserName | String| The session user name |
|Password | String| The session user password|
|Roles | String| Role privileges|
|IpAddress | String| IP address of session source|
|StartTimeStamp | String| Time when the session began|
|LastAccessedTimeStamp | String| Time when the session was last accessed| 
|Description | String | Description for the user session|


> ### Retrieve instance of a session
> - Perform a GET on `/api/SessionService/Sessions(Id)`
> - This method returns the sessions associated with the User ID
> - Parse returned response status code and check if it's 200 for success
> - The response from the session(ID) call is below

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
> The table below describes the attributes in detail:

| Attribute Name  |      Type     |  Description |
| -------------- | :-----------: | ----:|
|Id | String| The unique identifier of the SessionService|
|UserName | String| The session user name |
|Password | String| The session user password|
|Roles | String| Role privileges|
|IpAddress | String| IP address of session source|
|StartTimeStamp | String| Time when the session began|
|LastAccessedTimeStamp | String| Time when the session was last accessed| 
|Description | String | Description for the user session|

> ### Delete a specific session
> - Perform Delete method on `/api/SessionService/Sessions(arg1)`
> - Delete method deletes a specific session
> - On successful deletion of a session the api returns the status code 204


> ### Retrieve the session configuration
> - Perform a GET on `api/SessionService/SessionConfiguration`
> - Returns a response status code of 200 to indicate that the operation was successful
> - Example Response from the call sessionconfiguration is below :
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
> The table below describes the attributes returned in detail:

| Attribute Name  |      Type     |  Description |
| -------------- | :-----------: | ----:|
|SessionType | String| The session user name|
|MaxSessions |Long| Maximum session for this session type |
|SessionTimeout | Long| session timeout value for this session type|
|MinSessionTimeout | Long| Minimum session time for this session type|
|MaxSessionTimeout | Long| Maximum session time this session type |
|MinSessionsAllowed | Long| Minimum sessions allowed for this session type |
|MaxSessionsAllowed | Long| Maximum sessions allowed for this session type|
|MaxSessionsConfigurable | boolean| Boolean value if maximum sessions can be configured |
|SessionTimeoutConfigurable | boolean| Boolean value if session timeout can be configured |
