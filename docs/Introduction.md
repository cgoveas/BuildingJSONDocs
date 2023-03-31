# Introduction
OpenManage Enterprise is a hardware management and monitoring console that provides a comprehensive view of devices. These devices include servers, chassis, network switches, and other third-party devices on the enterprise network. OpenManage Enterprise is designed with a focus on simplicity, automation, and unification of data center management.

Some of the key features of OpenManage Enterprise include:
-	Architecture is delivered as a virtual appliance—ESXi, Hyper-V, and KVM
-	Engineered on CentOS with PostgreSQL database
-	No operating system and database licenses are required
-	End-to-end server life cycle management that includes inventory, monitoring, reporting, configuration, and deployment
-	Northbound API that helps you automate and integrate with existing solutions
-	Simplified discovery processes that enable devices to automatically register themselves with the console
-	Access to warranty status
-	Customized reporting capabilities

> See the **Dell OpenManage Enterprise Release Notes** on https://www.dell.com/support for the features and limitations of this OpenManage Enterprise version.

## The OpenManage Enterprise API
OpenManage Enterprise supports RESTful APIs that enhance system management capabilities. The RESTful interface is provided over `HTTPS` in `JSON` format based on `ODATA v4` usable by clients, scripts, and browser-based GUIs. 

Use the OpenManage Enterprise APIs to build console management tools based on common programming and scripting languages such as `Python`, `Java`, and `C`. 

> See the **Dell OpenManage Enterprise User's Guide** and the **Dell OpenManage Enterprise RESTful API Guide** on https://www.dell.com/support for a more detailed decriptions of the OpenManage Enterprise APIs and workflows. 

See the **Dell OpenManage Enterprise Modular Edition RESTful API Guide** on https://www.dell.com/support for the OpenManage Enterprise Modular Edition APIs.

### What are RESTful APIs?
Representational State Transfer or REST is a software architectural style used within the World Wide Web. REST architectures are commonly used for many IT solutions, including the definition of web-based APIs. Systems that adhere to REST practices are often referred to as RESTful interfaces. RESTful interfaces use the HTTP methods—`GET`, `POST`, `DELETE`, and so on—that web browsers use to access web pages.

### What is OData?
OData is an open protocol standard for the definition and exchange of information using RESTful APIs. When implementing a common interface across multiple vendors, it is important to standardize the data formats. Standardizing the data formats ensure that the data structures remain interchangeable between different manufacturers.

### What are JSONs?
The console API represents data using JSON. JSON is a lightweight data-interchange format that is readable and can also be easily parsed by machines. JSON is based on a subset of the JavaScript Programming Language. JSON uses a text format that is language independent but uses conventions familiar to programmers of the C-family of languages such as `C`, `C++`, `C#`, `Java`, `JavaScript`, `PERL`, and `Python`. These properties make JSON an ideal data-interchange language.

### What is HTTPS communication?
The Hypertext Transfer Protocol or `HTTP` is an application protocol for distributed, collaborative, hypermedia information systems. HTTP forms the foundation of data communication for the World Wide Web. 

Secure HTTP - or `HTTPS` - is a secure version of HTTP which operates within a network connection encrypted by `TLS` or `SSL`. By enforcing HTTPS, the security of console management is significantly enhanced.

