---
title: Tutorial
layout: default
subtitle: Tutorial
---
#### Welcome
Welcome to the Marim tutorial! Follow the steps below to develop your first
data service.



#### Database
{: .mt-5}
This tutorial makes use of a sample database. To execute it, type the command

<pre class="terminal">docker run --rm --network host marimplatform/dvdrental</pre>



#### Project
{: .mt-5}
A Marim project, i.e., a set of specification files, is simply a directory.
Therefore, create the tutorial project typing the command

<pre class="terminal">mkdir ./dvdrental</pre>



#### Specification
{: .mt-5}
Create the `./dvdrental/dvdrental.marim` file with the specification below.

<pre><code class="language-marim">
table Category                       // Declares the structure of a query result
    column Id         type integer   // Declares a column of a query result
    column Name       type string    // Supported datatypes are date, decimal, integer, string, time and timestamp
    column LastUpdate type timestamp

source DvdRental                                          // Declares a JDBC data source
    dialect  postgresql                                   // Supported dialects are calcite, db2, mysql, oracle, postgresql, sqlserver and teradata
    url      "jdbc:postgresql://localhost:5432/dvdrental" // JDBC URL with which the service will connect to the database
    property "user"     "postgres"                        // The property keyword sets a JDBC driver property value
    property "password" "postgres"                        // All JDBC drivers support the properties "user" and "password" 

query Categories                          // Declares a query
    result table Category                 // Declares the query result structure

    source DvdRental                      // Declares the data source in which the service will execute the query

    statement                             // The query statement is a single or multiline string                  
        "select category_id as Id, 
                name        as Name,
                last_update as LastUpdate
           from category"

query Category
    parameter Id schema type integer not null  // Declares a query parameter and its schema, i.e., its type, nullability etc.

    result table Category

    source DvdRental

    statement
        "select category_id as Id, 
                name        as Name,
                last_update as LastUpdate
           from category
          where category_id = (" Id ")"   // Parameterized query statements may refer its parameters</code></pre>



#### Service execution
{: .mt-5}
To execute the service, type the command

<pre class="terminal">docker run --rm --network host -v $(pwd)/dvdrental:/marim marimplatform/service</pre>

which will result in

<pre class="terminal">  __  __            _
 |  \/  |          (_)
 | \  / | __ _ _ __ _ _ __ ___
 | |\/| |/ _` | '__| | '_ ` _ \
 | |  | | (_| | |  | | | | | | |
 |_|  |_|\__,_|_|  |_|_| |_| |_| Data Services Platform
 
                                         1.0.0-SNAPSHOT
                          https://www.marimplatform.dev

                        Copyright (C) 2022 Saulo Araujo
                                    All rights reserved

2023-04-09T13:37:13.530Z  INFO 1 --- [           main] dev.marimplatform.ServiceApplication     : Starting ServiceApplication using Java 17.0.6 with PID 1 (/app/classes started by root in /)
2023-04-09T13:37:13.538Z  INFO 1 --- [           main] dev.marimplatform.ServiceApplication     : No active profile set, falling back to 1 default profile: "default"
2023-04-09T13:37:17.333Z  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2023-04-09T13:37:17.356Z  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2023-04-09T13:37:17.357Z  INFO 1 --- [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.7]
2023-04-09T13:37:17.748Z  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2023-04-09T13:37:17.753Z  INFO 1 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 3941 ms
2023-04-09T13:37:24.263Z  INFO 1 --- [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 3 endpoint(s) beneath base path '/actuator'
2023-04-09T13:37:24.563Z  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2023-04-09T13:37:24.668Z  INFO 1 --- [           main] dev.marimplatform.ServiceApplication     : Started ServiceApplication in 12.349 seconds (process running for 13.697)</pre>

#### Query execution
{: .mt-5}
Marim maps queries to URLs. Query identifiers become URL suffixes. Thus, to 
execute a query, send a HTTP request to its URL. For example, to execute the 
query `Categories`, send a request to the URL
`http://localhost:8080/rest/Categories`. To send such request with
[curl](https://curl.se/), type the command

<pre class="terminal">curl http://localhost:8080/rest/Categories</pre>

The service will respond

<pre class="terminal">[{"Id":1,"Name":"Action","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":2,"Name":"Animation","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":3,"Name":"Children","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":4,"Name":"Classics","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":5,"Name":"Comedy","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":6,"Name":"Documentary","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":7,"Name":"Drama","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":8,"Name":"Family","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":9,"Name":"Foreign","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":10,"Name":"Games","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":11,"Name":"Horror","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":12,"Name":"Music","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":13,"Name":"New","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":14,"Name":"Sci-Fi","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":15,"Name":"Sports","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":16,"Name":"Travel","LastUpdate":"2006-02-15T09:46:27Z"}]</pre>



#### Pretty-print
{: .mt-5}
To reduce response length, Marim does not pretty-print responses, but you can
easily pretty-print JSON responses piping to
[jq](https://stedolan.github.io/jq/). For instance, the command

<pre class="terminal">curl http://localhost:8080/rest/Categories | jq</pre>

results in

<pre class="terminal">[
  {
    "Id": 1,
    "Name": "Action",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 2,
    "Name": "Animation",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 3,
    "Name": "Children",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 4,
    "Name": "Classics",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 5,
    "Name": "Comedy",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 6,
    "Name": "Documentary",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 7,
    "Name": "Drama",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 8,
    "Name": "Family",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 9,
    "Name": "Foreign",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 10,
    "Name": "Games",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 11,
    "Name": "Horror",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 12,
    "Name": "Music",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 13,
    "Name": "New",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 14,
    "Name": "Sci-Fi",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 15,
    "Name": "Sports",
    "LastUpdate": "2006-02-15T09:46:27Z"
  },
  {
    "Id": 16,
    "Name": "Travel",
    "LastUpdate": "2006-02-15T09:46:27Z"
  }
]</pre>



#### CSV
{: .mt-5}
Set the `Accept` HTTP request header to `text/csv` to get CSV responses. For 
example, the command

<pre class="terminal">curl -v -H "Accept: text/csv" http://localhost:8080/rest/Categories</pre>

results in 

<pre class="terminal">Id,Name,LastUpdate
1,Action,2006-02-15T09:46:27Z
2,Animation,2006-02-15T09:46:27Z
3,Children,2006-02-15T09:46:27Z
4,Classics,2006-02-15T09:46:27Z
5,Comedy,2006-02-15T09:46:27Z
6,Documentary,2006-02-15T09:46:27Z
7,Drama,2006-02-15T09:46:27Z
8,Family,2006-02-15T09:46:27Z
9,Foreign,2006-02-15T09:46:27Z
10,Games,2006-02-15T09:46:27Z
11,Horror,2006-02-15T09:46:27Z
12,Music,2006-02-15T09:46:27Z
13,New,2006-02-15T09:46:27Z
14,Sci-Fi,2006-02-15T09:46:27Z
15,Sports,2006-02-15T09:46:27Z
16,Travel,2006-02-15T09:46:27Z</pre>



#### Default parameters
{: .mt-5}
By default, every Marim data service URL accepts the following parameters:

- **_select** - names of the properties/columns the response will have;
- **_filter** - [RSQL](https://github.com/jirutka/rsql-parser) predicate 
                the response elements must satisfy;
- **_orderby** - response sort criteria;
- **_skip** - number of elements that will be excluded from the response start;
- **_top** - maximum number of elements the response will have.



##### _select
{: .mt-5}
The `_select` parameter allows one to specify which property/column names the response
will include. Its value must be a comma-separated list of property/column names. For
instance, the command

<pre class="terminal">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?_select=Id,Name</pre>

results in 

<pre class="terminal">Id,Name
1,Action
2,Animation
3,Children
4,Classics
5,Comedy
6,Documentary
7,Drama
8,Family
9,Foreign
10,Games
11,Horror
12,Music
13,New
14,Sci-Fi
15,Sports
16,Travel</pre>



##### _filter
{: .mt-5}
The `_filter` parameter allows one to specify which elements the response will
include. Its value must be a [RSQL](https://github.com/jirutka/rsql-parser)
expression. For example, the command

<pre class="terminal">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?_filter=Name=gt=Games</pre>

results in 

<pre class="terminal">Id,Name,LastUpdate
11,Horror,2006-02-15T09:46:27Z
12,Music,2006-02-15T09:46:27Z
13,New,2006-02-15T09:46:27Z
14,Sci-Fi,2006-02-15T09:46:27Z
15,Sports,2006-02-15T09:46:27Z
16,Travel,2006-02-15T09:46:27Z</pre>



##### _orderby
{: .mt-5}
Through the `_orderby` parameter, one can specify how the service will sort the
response. Its value must be a comma-separated list of sort criterias, i.e., a
column name and a sort direction (asc or desc) separated by a white space. For
instance, the command

<pre class="terminal">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?_orderby=Name%20desc</pre>

results in 

<pre class="terminal">Id,Name,LastUpdate
16,Travel,2006-02-15T09:46:27Z
15,Sports,2006-02-15T09:46:27Z
14,Sci-Fi,2006-02-15T09:46:27Z
13,New,2006-02-15T09:46:27Z
12,Music,2006-02-15T09:46:27Z
11,Horror,2006-02-15T09:46:27Z
10,Games,2006-02-15T09:46:27Z
9,Foreign,2006-02-15T09:46:27Z
8,Family,2006-02-15T09:46:27Z
7,Drama,2006-02-15T09:46:27Z
6,Documentary,2006-02-15T09:46:27Z
5,Comedy,2006-02-15T09:46:27Z
4,Classics,2006-02-15T09:46:27Z
3,Children,2006-02-15T09:46:27Z
2,Animation,2006-02-15T09:46:27Z
1,Action,2006-02-15T09:46:27Z</pre>



##### _skip
{: .mt-5}
The `_skip` parameter allows one to specfy how many elements will be excluded
from the response start. Its value must be a integer greater than or equal to
zero. For example, the command

<pre class="terminal">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?_skip=1</pre>

results in 

<pre class="terminal">Id,Name,LastUpdate
2,Animation,2006-02-15T09:46:27Z
3,Children,2006-02-15T09:46:27Z
4,Classics,2006-02-15T09:46:27Z
5,Comedy,2006-02-15T09:46:27Z
6,Documentary,2006-02-15T09:46:27Z
7,Drama,2006-02-15T09:46:27Z
8,Family,2006-02-15T09:46:27Z
9,Foreign,2006-02-15T09:46:27Z
10,Games,2006-02-15T09:46:27Z
11,Horror,2006-02-15T09:46:27Z
12,Music,2006-02-15T09:46:27Z
13,New,2006-02-15T09:46:27Z
14,Sci-Fi,2006-02-15T09:46:27Z
15,Sports,2006-02-15T09:46:27Z
16,Travel,2006-02-15T09:46:27Z</pre>



##### _top
{: .mt-5}
The `_top` parameter allows one to specfy the maximum number of elements the 
response will have. Its value must be a integer greater than zero. For
instance, the command

<pre class="terminal">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?_top=1</pre>

results in 

<pre class="terminal">Id,Name,LastUpdate
1,Action,2006-02-15T09:46:27Z</pre>



#### OpenAPI
{: .mt-5}
Request the service OpenAPI specification typing the command

<pre class="terminal">curl http://localhost:8080/openapi</pre>

The service will respond

<pre class="terminal">{
  "openapi" : "3.0.1",
  "servers" : [ {
    "url" : "http://localhost:8080/rest"
  } ],
  "paths" : {
    "/Categories" : {
      "get" : {
        "tags" : [ "Categories" ],
        "responses" : {
          "200" : {
            "description" : "OK",
            "content" : {
              "application/json" : {
                "schema" : {
                  "type" : "array",
                  "items" : {
                    "type" : "object",
                    "properties" : {
                      "Id" : {
                        "type" : "integer",
                        "format" : "int64"
                      },
                      "Name" : {
                        "type" : "string"
                      },
                      "LastUpdate" : {
                        "type" : "string",
                        "format" : "date-time"
                      }
                    }
                  }
                }
              },
              "text/csv" : { }
            }
          },
          "400" : {
            "description" : "BAD_REQUEST",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          },
          "500" : {
            "description" : "INTERNAL_SERVER_ERROR",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          }
        }
      },
      "post" : {
        "tags" : [ "Categories" ],
        "responses" : {
          "200" : {
            "description" : "OK",
            "content" : {
              "application/json" : {
                "schema" : {
                  "type" : "array",
                  "items" : {
                    "type" : "object",
                    "properties" : {
                      "Id" : {
                        "type" : "integer",
                        "format" : "int64"
                      },
                      "Name" : {
                        "type" : "string"
                      },
                      "LastUpdate" : {
                        "type" : "string",
                        "format" : "date-time"
                      }
                    }
                  }
                }
              },
              "text/csv" : { }
            }
          },
          "400" : {
            "description" : "BAD_REQUEST",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          },
          "500" : {
            "description" : "INTERNAL_SERVER_ERROR",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          }
        }
      },
      "parameters" : [ {
        "name" : "$select",
        "in" : "query",
        "style" : "form",
        "explode" : false,
        "schema" : {
          "type" : "array",
          "items" : {
            "type" : "string",
            "enum" : [ "Id", "Name", "LastUpdate" ]
          }
        }
      }, {
        "name" : "$filter",
        "in" : "query",
        "schema" : {
          "type" : "string"
        }
      }, {
        "name" : "$orderby",
        "in" : "query",
        "style" : "form",
        "explode" : false,
        "schema" : {
          "type" : "array",
          "items" : {
            "type" : "string",
            "enum" : [ "Id asc", "Id desc", "Name asc", "Name desc", "LastUpdate asc", "LastUpdate desc" ]
          }
        }
      }, {
        "name" : "$skip",
        "in" : "query",
        "schema" : {
          "type" : "integer",
          "format" : "int64",
          "maximum" : 9223372036854775807,
          "minimum" : 0
        }
      }, {
        "name" : "$top",
        "in" : "query",
        "schema" : {
          "type" : "integer",
          "format" : "int32",
          "maximum" : 2147483647,
          "minimum" : 1
        }
      } ]
    },
    "/Category" : {
      "get" : {
        "tags" : [ "Category" ],
        "responses" : {
          "200" : {
            "description" : "OK",
            "content" : {
              "application/json" : {
                "schema" : {
                  "type" : "array",
                  "items" : {
                    "type" : "object",
                    "properties" : {
                      "Id" : {
                        "type" : "integer",
                        "format" : "int64"
                      },
                      "Name" : {
                        "type" : "string"
                      },
                      "LastUpdate" : {
                        "type" : "string",
                        "format" : "date-time"
                      }
                    }
                  }
                }
              },
              "text/csv" : { }
            }
          },
          "400" : {
            "description" : "BAD_REQUEST",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          },
          "500" : {
            "description" : "INTERNAL_SERVER_ERROR",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          }
        }
      },
      "post" : {
        "tags" : [ "Category" ],
        "responses" : {
          "200" : {
            "description" : "OK",
            "content" : {
              "application/json" : {
                "schema" : {
                  "type" : "array",
                  "items" : {
                    "type" : "object",
                    "properties" : {
                      "Id" : {
                        "type" : "integer",
                        "format" : "int64"
                      },
                      "Name" : {
                        "type" : "string"
                      },
                      "LastUpdate" : {
                        "type" : "string",
                        "format" : "date-time"
                      }
                    }
                  }
                }
              },
              "text/csv" : { }
            }
          },
          "400" : {
            "description" : "BAD_REQUEST",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          },
          "500" : {
            "description" : "INTERNAL_SERVER_ERROR",
            "content" : {
              "text/plain" : {
                "schema" : {
                  "type" : "string"
                }
              }
            }
          }
        }
      },
      "parameters" : [ {
        "name" : "Id",
        "in" : "query",
        "required" : true,
        "explode" : false,
        "schema" : {
          "type" : "integer",
          "format" : "int64"
        }
      }, {
        "name" : "$select",
        "in" : "query",
        "style" : "form",
        "explode" : false,
        "schema" : {
          "type" : "array",
          "items" : {
            "type" : "string",
            "enum" : [ "Id", "Name", "LastUpdate" ]
          }
        }
      }, {
        "name" : "$filter",
        "in" : "query",
        "schema" : {
          "type" : "string"
        }
      }, {
        "name" : "$orderby",
        "in" : "query",
        "style" : "form",
        "explode" : false,
        "schema" : {
          "type" : "array",
          "items" : {
            "type" : "string",
            "enum" : [ "Id asc", "Id desc", "Name asc", "Name desc", "LastUpdate asc", "LastUpdate desc" ]
          }
        }
      }, {
        "name" : "$skip",
        "in" : "query",
        "schema" : {
          "type" : "integer",
          "format" : "int64",
          "maximum" : 9223372036854775807,
          "minimum" : 0
        }
      }, {
        "name" : "$top",
        "in" : "query",
        "schema" : {
          "type" : "integer",
          "format" : "int32",
          "maximum" : 2147483647,
          "minimum" : 1
        }
      } ]
    }
  }
}</pre>



#### Swagger UI
{: .mt-5}
Open the address `http://localhost:8080/swagger-ui` in a browser to visualize 
the service OpenAPI specification and send requests to it in a more 
human-friendly way through the embedded 
[Swagger UI](https://swagger.io/tools/swagger-ui/) application.

<div class="text-center">
    <img src="img/swagger-ui.png" style="width: 100%">
</div>



#### Next steps
{: .mt-5}
Read the [manual](/manual.html) to learn more about Marim and start developing
your own data services!