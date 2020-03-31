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


### Operations

The base url is `<host>/api/mock-contact-api` where `host` is the location where the Martini is deployed. By default, it's `localhost:8080`.

`GET /employee`

Returns a list of contacts

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-contact-api/contacts \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
[
    {
        "firstName": "Jared",
        "lastName": "Pilbeam",
        "email": "jpilbeam0@prlog.org",
        "phoneNumber": "+46 (963) 473-8538",
        "id": 1
    },
    {
        "firstName": "Inglis",
        "lastName": "Kirkwood",
        "email": "ikirkwood1@nytimes.com",
        "phoneNumber": "+598 (876) 701-1420",
        "id": 2
    },
    ...
    {
        "firstName": "Stanleigh",
        "lastName": "O'Mannion",
        "email": "somannionn@drupal.org",
        "phoneNumber": "+7 (374) 438-6317",
        "id": 24
    },
    {
        "firstName": "Janene",
        "lastName": "Maysor",
        "email": "jmaysoro@techcrunch.com",
        "phoneNumber": "+967 (520) 649-4447",
        "id": 25
    }
]
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
        "id": 26
    }
}
```

`GET /employees/<contactId>`

Returns a single contact record that matches the given `contactId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-contact-api/contacts/26 \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "firstName": "John",
    "lastName": "Doe",
    "email": "jdoe@mail.com",
    "phoneNumber": "(273) 721-4244",
    "id": 26
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
        "phoneNumber": "(538) 686-0011"
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
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted contact."
}
```
