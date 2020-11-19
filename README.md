# Mock Contact API
A simple contact api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `mock-contact-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

### Usage
This package exposes operations for a simple contact REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `io/toro/mock/contact/api/Contact` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Flag used for testing
debug.enabled=true

# The database connection name to be used
database.name=mock_contact_api

# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/mock_contact_api.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>
mysql.username=root
mysql.password=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.


### Sending Authenticated Requests

You can use your ECC account to send authenticated request to the API. Your ECC credentials must be sent in the `Authorization` header in the HTTP request

#### To authenticate a request with basic authentication

1. Combine your email and password with a colon (`:`). e.g. `jdoe@mailinator.com:pa$$w0rd`
2. Encode the resulting string in Base64
3. Include an Authorization header in the HTTP request containing the base64-encoded string. Example: ```
Authorization: Basic amRvZUBtYWlsaW5hdG9yLmNvbTpwYSQkdzByZA==```

### Operations

The base url is `<host>/api/mock-contact-api` where `host` is the location where the Martini is deployed. By default, it's `localhost:8080`.

`GET /employee`

Returns a list of contacts

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-contact-api/contacts \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully retrieved contacts",
    "contacts": [
        {
            "firstName": "John",
            "lastName": "Doe",
            "email": "jdoe@mailinator.com",
            "phoneNumber": "(538) 686-0011",
            "dateCreated": 1605663588705,
            "dateUpdated": 1605663588705,
            "id": "7c29a7b7-e034-46b7-826f-526c6736061e"
        },
        {
            "firstName": "Jane",
            "lastName": "Doe",
            "email": "janed@mailinator.com",
            "phoneNumber": "(538) 686-0011",
            "dateCreated": 1605663588705,
            "dateUpdated": 1605663588705,
            "id": "c4417a7d-34e9-4d5f-846d-95f60d9e4766"
        }
    ]
}
```

`POST /contacts`

Create a new contact record

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-contact-api/contacts \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -d '{
    "firstName": "John",
    "lastName": "Doe",
    "email": "jdoe@mail.com",
    "phoneNumber": "(273) 721-4244"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully added contact.",
    "contact": {
        "firstName": "John",
        "lastName": "Doe",
        "email": "jdoe@mail.com",
        "phoneNumber": "(273) 721-4244",
        "id": "7c29a7b7-e034-46b7-826f-526c6736061e"
    }
}
```

`GET /employees/<contactId>`

Returns a single contact record that matches the given `contactId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-contact-api/contacts/7c29a7b7-e034-46b7-826f-526c6736061e \
  -H 'Accept: application/json'
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "OK",
    "message": "Successfully added contact",
    "contact": {
        "firstName": "John",
        "lastName": "Doe",
        "email": "jdoe@mailinator.comt",
        "phoneNumber": "(273) 721-4244",
        "dateCreated": 1605663588705,
        "dateUpdated": 1605663588705,
        "id": "7c29a7b7-e034-46b7-826f-526c6736061e"
    }
}
```

`PATCH /contacts/<contactId>`

Updates the employee record that matches the given `contactId`

**Sample Request**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-contact-api/contacts/26 \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -d '{
    "email": "jdoe.updated@mail.com",
    "phoneNumber": "(538) 686-0011"
}'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code of `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully updated contact.",
    "contact": {
        "firstName": "John",
        "lastName": "Doe",
        "email": "jdoe.updated@mail.com",
        "phoneNumber": "(538) 686-0011",
        "id": "7c29a7b7-e034-46b7-826f-526c6736061e"
    }
}
```

`DELETE /contacts/<contactId>`

Deletes a contact record that matches the `contactId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-contact-api/contacts/25 \
  -H 'Accept: application/json'
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted contact."
}
```
