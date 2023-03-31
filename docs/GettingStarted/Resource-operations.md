# Operating on resources

These standard HTTP methods are used for creating, retrieving, updating, and deleting resources. 

| HTTP method  |      Description     |  Example |
| --------------: | :----------- | :------------|
|**GET** | Used for retrieving the resource representation. This method does not modify the resource across repeated invocations. The query parameters are appended to the URI to appropriately filter the resource instances. | To retrieve all logs with dates and times after 2016-11-15 16:35:39.820, run: `GET <BASE_URI>/ApplicationService/AuditLogs?$filter=CreatedDate gt '2016-11-15 16:35:39.820'` | 
|**POST** | Used for creating resources or performing actions.| To create a user session (payload is not displayed), run: `POST <BASE_URI>/SessionService/Sessions`|
|**PUT** | Used for updating a specific instance or create a specific resource instance with a specific identifier.| To update the user account details (payload is not displayed), run: `PUT <BASE_URI>/AccountService/Accounts('1414')` |
|**DELETE** | Used for removing a specific resource. If the resource does not exist, a `Success` response is returned.| To delete a specific user account, run: `DELETE <BASE_URI>/AccountService/Accounts('1414')` |

When you perform tasks using these methods, they return an **HTTP response code**. For more information about response codes, see **Status Code Definitions** on the W3.org website. 

> If a method fails, you may also see an `Error` and `Event Message` code. You can search for more details about this code using the **Dell QRL app** on your mobile device.