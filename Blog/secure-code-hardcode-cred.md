# CWE-798: Use of Hard-coded Credentials
---



 <p style="text-align: justify; letter-spacing: 0.002em;">
	The software contains hard-coded credentials, such as a password or cryptographic key, which it uses for its own inbound authentication, outbound communication to external components, or encryption of internal data.  
</p>

<p style="text-align: justify; letter-spacing: 0.002em;">
Hard-coded credentials typically create a significant hole that allows an attacker to bypass the authentication that has been configured by the software administrator. This hole might be difficult for the system administrator to detect. Even if detected, it can be difficult to fix, so the administrator may be forced into disabling the product entirely. There are two main variations:
</p>

 <p style="text-align: justify; letter-spacing: 0.002em;">
Inbound: the software contains an authentication mechanism that checks the input credentials against a hard-coded set of credentials.

</p>

 <p style="text-align: justify; letter-spacing: 0.002em;">
Outbound: the software connects to another system or component, and it contains hard-coded credentials for connecting to that component. 
</p>


## Example hardcoded credential 
* Sensitive Code Example

```js
    var mysql = require('mysql');
    var connection = mysql.createConnection(
    {
        host:'localhost',
        user: "admin",
        database: "project",
        password: "mypassword", // sensitive
        multipleStatements: true
    });
    connection.connect();
```

* compliant solution
```js
    var mysql = require('mysql');
    var connection = mysql.createConnection({
        host: process.env.MYSQL_URL,
        user: process.env.MYSQL_USERNAME,
        password: process.env.MYSQL_PASSWORD,
        database: process.env.MYSQL_DATABASE
    });
    connection.connect();
```


### Recommended Secure Coding Practices
1. Store the credentials in a configuration file that is not pushed to the code repository.
2. Store the credentials in a database.
3. Use your cloud provider’s service for managing secrets.
4. If the a password has been disclosed through the source code: change it.


### Ask Yourself Whether
1. Credentials allows access to a sensitive component like a database, a file storage, an API or a service.
2. Credentials are used in production environments.
3. Application re-distribution is required before updating the credentials.
