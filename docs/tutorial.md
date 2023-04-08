---
title: Tutorial
layout: default
subtitle: Tutorial
---
#### Welcome
Welcome to the Marim tutorial! Follow the steps below to develop your first data service in Marim.



#### Database
{: .mt-5}
This tutorial makes use of a sample database. To execute it, type the command

<pre><code class="language-bash">docker run --rm --network host marimplatform/dvdrental</code></pre>



#### Project
{: .mt-5}
A Marim project, i.e., a set of specification files, is simply a directory. Therefore, type the following command to create a Marim project.

<pre><code class="language-bash">mkdir ./dvdrental</code></pre>



#### Specification
{: .mt-5}
Create the `./dvdrental/dvdrental.marim` file with the specification below.

<pre><code class="language-marim">table Category
    column Id         type integer
    column Name       type string
    column LastUpdate type timestamp

source DvdRental
    dialect  postgresql
    driver   "org.postgresql.Driver"
    url      "jdbc:postgresql://localhost:5432/dvdrental"
    property "user"     "postgres"
    property "password" "postgres"

query Categories
    result table Category

    source DvdRental

    statement "
        select category_id as Id, 
    	       name        as Name,
    	       last_update as LastUpdate
    	  from category
    "

query Category
    parameter Id schema integer not null

    result table Category

    source DvdRental

    statement "
        select category_id as Id, 
               name        as Name,
               last_update as LastUpdate
          from category
         where category_id = (" Id ") 
    "</code></pre>



#### Execution
{: .mt-5}
Execute the service typing the command

<pre><code class="language-bash">docker run --rm --network host -v $(pwd)/dvdrental:/marim marimplatform/service</code></pre>



#### Request
{: .mt-5}
Type the command below to send a request to the service.

<pre><code class="language-bash">curl http://localhost:8080/rest/Categories</code></pre>

The service will respond

<pre><code class="language-json">[{"Id":1,"Name":"Action","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":2,"Name":"Animation","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":3,"Name":"Children","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":4,"Name":"Classics","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":5,"Name":"Comedy","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":6,"Name":"Documentary","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":7,"Name":"Drama","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":8,"Name":"Family","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":9,"Name":"Foreign","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":10,"Name":"Games","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":11,"Name":"Horror","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":12,"Name":"Music","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":13,"Name":"New","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":14,"Name":"Sci-Fi","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":15,"Name":"Sports","LastUpdate":"2006-02-15T09:46:27Z"},{"Id":16,"Name":"Travel","LastUpdate":"2006-02-15T09:46:27Z"}]</code></pre>



#### Pretty-print
{: .mt-5}
To reduce response length, Marim does not pretty-print responses, but you can easily pretty-print JSON responses piping to [jq](https://stedolan.github.io/jq/). For example, the command

<pre><code class="language-bash">curl http://localhost:8080/rest/Categories | jq</code></pre>

results in

<pre><code class="language-json">[
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
]</code></pre>



#### CSV
{: .mt-5}
Set the `Accept` header to `text/csv` to get CSV responses. For instance, the command 

<pre><code class="language-bash">curl -v -H "Accept: text/csv" http://localhost:8080/rest/Categories</code></pre>

results in 

<pre class="csv">Id,Name,LastUpdate
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
By default, every resource of a Marim data service accepts the following parameters:

- **$select** - column names the response will have;
- **$filter** - [RSQL](https://github.com/jirutka/rsql-parser) predicate the response elements must satisfy;
- **$orderby** - response sort criteria;
- **$skip** - number of elements that will be excluded from the response start;
- **$top** - maximum number of elements the response will have.



##### $select
{: .mt-5}
<pre><code class="language-bash">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?\$select=Id,Name</code></pre>

results in 

<pre class="csv">Id,Name
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



##### $filter
{: .mt-5}
<pre><code class="language-bash">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?\$filter=Name=gt=Games</code></pre>

results in 

<pre class="csv">Id,Name,LastUpdate
11,Horror,2006-02-15T09:46:27Z
12,Music,2006-02-15T09:46:27Z
13,New,2006-02-15T09:46:27Z
14,Sci-Fi,2006-02-15T09:46:27Z
15,Sports,2006-02-15T09:46:27Z
16,Travel,2006-02-15T09:46:27Z</pre>



##### $orderby
{: .mt-5}
<pre><code class="language-bash">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?\$orderby=Name%20desc</code></pre>

results in 

<pre class="csv">Id,Name,LastUpdate
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



##### $skip
{: .mt-5}
<pre><code class="language-bash">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?\$skip=1</code></pre>

results in 

<pre class="csv">Id,Name,LastUpdate
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



##### $top
{: .mt-5}
<pre><code class="language-bash">curl -H "Accept: text/csv" http://localhost:8080/rest/Categories?\$top=1</code></pre>

results in 

<pre class="csv">Id,Name,LastUpdate
1,Action,2006-02-15T09:46:27Z</pre>



#### OpenAPI
{: .mt-5}
Request the service OpenAPI specification typing the command

<pre><code class="language-bash">curl http://localhost:8080/openapi</code></pre>

The service will respond

<pre><code class="language-json">{
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
}</code></pre>



#### Swagger UI
{: .mt-5}
Open the address `http://localhost:8080/swagger-ui` in a browser to visualize 
the service OpenAPI specification and send requests to it in a more 
human-friendly way through the embedded 
[Swagger UI](https://swagger.io/tools/swagger-ui/) application.

<div class="text-center">
    <img src="img/swagger-ui.png" style="width: 75%">
</div>



#### Next steps
{: .mt-5}
Read the [manual](/manual.html) to learn more about Marim and start developing your own data services!