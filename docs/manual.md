---
title: Manual
layout: default
subtitle: Manual
---

#### Introduction
{: .mt-5}
Welcome to the Marim manual! Before reading it, we recommend you follow the [tutorial](tutorial.html) to have an overview of how Marim works.

#### Data sources
{: .mt-5}
The `source` keyword lets you specify data sources. For example, the snippet below specifies a data source named `DvdRental`:

<pre><code class="language-marim">source DvdRental
    dialect  postgresql
    url      "jdbc:postgresql://localhost:5432/dvdrental"
    property "user"     "postgres"
    property "password" "postgres"</code></pre>

##### Dialects
You specify to which DBMS (Database Management System) a data source refers to through the keyword `dialect`. Marim supports the following dialects:

|Dialect     |DBMS                                                                                                                                                                |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|`calcite`   |[Apache Calcite](https://calcite.apache.org/), not really a DBMS, actually a framework that lets you query CSV file content through SQL (Structured Query Language)|
|`db2`       |[IBM DB2](https://www.ibm.com/products/db2)                                                                                                                         |
|`mysql`     |[MySQL](https://www.mysql.com)                                                                                                                                      |
|`oracle`    |[Oracle Database](https://www.oracle.com/database)                                                                                                                  |
|`postgresql`|[PostgreSQL](https://www.postgresql.org/)                                                                                                                           |
|`sqlserver` |[SQL Server](https://www.microsoft.com/sql-server)                                                                                                                  |
|`teradata`  |[Teradata Database](https://www.teradata.com/Products/Software/Database)                                                                                            |
{: .table .table-bordered}

##### URLs
You give Marim the JDBC (Java Database Connectivity) URL of the database the data source refers to through the keyword `url`.
The table below shows the JDBC driver Marim bundles for each dialect, and gives links to the sections in their documentations that discusses their URL formats.

|Dialect     |GroupId                 |ArtifactId       |Version     |More information                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|`calcite`   |org.apache.calcite      |calcite-csv      |1.34.0      |<https://calcite.apache.org/docs/adapter.html#drivers>                                                                                    |
|`db2`       |com.ibm.db2             |jcc              |11.5.8.0    |<https://www.ibm.com/docs/en/db2/11.5?topic=cdsudidsdjs-url-format-data-server-driver-jdbc-sqlj-type-4-connectivity>                      |
|`mysql`     |com.mysql               |mysql-connector-j|8.0.33      |<https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-jdbc-url-format.html>                                                 |
|`oracle`    |com.oracle.database.jdbc|ojdbc11          |21.7.0.0    |<https://docs.oracle.com/en/database/oracle/oracle-database/23/jjdbc/data-sources-and-URLs.html#GUID-C4F2CA86-0F68-400C-95DA-30171C9FB8F0>|
|`postgresql`|org.postgresql          |postgresql       |42.5.4      |<https://jdbc.postgresql.org/documentation/use/#connecting-to-the-database>                                                               |
|`sqlserver` |com.microsoft.sqlserver |mssql-jdbc       |11.2.3.jre17|<https://learn.microsoft.com/en-us/sql/connect/jdbc/building-the-connection-url?view=sql-server-ver16>                                    |
|`teradata`  |com.teradata.jdbc       |terajdbc         |20.00.00.10 |<https://teradata-docs.s3.amazonaws.com/doc/connectivity/jdbc/reference/current/frameset.html>                                            |
{: .table .table-bordered}

##### Properties
The keyword `property` must be followed by a property name and value. The JDBC specification requires that every driver must support the properties `user` and `password`,
which define the name and password of the user with which the driver will connect to the database.

##### URL and property value sources
The keyword `file`, which can be used together with the keywords `url` and `property`, 
instruct Marim to set the content of a file as the URL of a data source or the value of a property.
The same applies to the keyword `variable`. In this case, however, Marim will set the content of an environment variable as the URL of a data source or the value of a property. 
For example, in the snippet below, Marim will set the content of the `DVD_RENTAL_URL` environment variable as the URL of the `DvdRental` data source, 
and the content of the `/var/run/secrets/dvd_rental/password.txt` file as the value of the `password` property.

<pre><code class="language-marim">source DvdRental
    dialect  postgresql
    url      variable "DVD_RENTAL_URL"
    property "user"     "postgres"
    property "password" file "/var/run/secrets/dvd_rental/password.txt"
</code></pre>

#### Queries
{: .mt-5}
The keyword `query` lets you specify a query. A query specification is comprised, at least, of a SQL statement and the data source in which it will be executed.

<pre><code class="language-marim">source DvdRental
    dialect  postgresql
    url      "jdbc:postgresql://localhost:5432/dvdrental"
    property "user"     "postgres"
    property "password" "postgres"
    
query Categories
    source DvdRental

    statement                  
        "select category_id as Id, 
                name        as Name,
                last_update as LastUpdate
        from category"</code></pre>

##### Anonymous data sources
The specification of the data source in which a query will be executed can be provided directly in the query specification.
In this case, however, the keyword `source` cannot be followed by an identifier, rendering it an anonymous data source,
and preventing other queries from referring to it. For example, in the snippet below, the data source of the `Categories` query is an anonymous one:

<pre><code class="language-marim">query Categories
    source
        dialect  postgresql
        url      "jdbc:postgresql://localhost:5432/dvdrental"
        property "user"     "postgres"
        property "password" "postgres"

    statement                  
        "select category_id as Id, 
                name        as Name,
                last_update as LastUpdate
           from category"</code></pre>

##### Automatic parameters: _skip and _top
Unless you state otherwise, Marim automatically adds the parameters `_skip` and `top` to every query.
You can confirm this by checking the Open API specification Marim generates from your queries.
For example, Marim generates the following Open API specification for the previous snippet:

<pre class="terminal">...
      "parameters" : [ {
        "name" : "_skip",
        "in" : "query",
        "schema" : {
          "type" : "integer",
          "format" : "int64",
          "maximum" : 9223372036854775807,
          "minimum" : 0
        }
      }, {
        "name" : "_top",
        "in" : "query",
        "schema" : {
          "type" : "integer",
          "format" : "int32",
          "maximum" : 2147483647,
          "minimum" : 1
        }
      } ]
...
</pre>

To prevent Marim from adding the `_skip` and/or `_top` parameters to a query, use the keyword `result` followed by the keyword `unpaginable` and/or `unlimitable`, as in the snippet below:

<pre><code class="language-marim">query Categories
    source DvdRental

    result
        unpaginable
        unlimitable

    statement
        "select category_id as Id, 
                name        as Name,
                last_update as LastUpdate
           from category"</code></pre>

You can instruct Marim to add the parameters `_skip` and/or `_top` again with the keywords `paginable` and/or `limitable`.
For example, the snippet below instructs Marim to add only the `_top` parameter to Categories query:

<pre><code class="language-marim">query Categories
    source DvdRental

    result
        unpaginable
        limitable

    statement
        "select category_id as Id, 
                name        as Name,
                last_update as LastUpdate
           from category"</code></pre>

#### Open Telemetry
{: .mt-5}

#### Command-line interface tool
{: .mt-5}