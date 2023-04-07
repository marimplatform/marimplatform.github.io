---
title: Tutorial
layout: default
subtitle: Tutorial
---
#### Welcome
Welcome to the Marim tutorial! Follow the steps below to create your first data service in Marim.

#### Sample database
{: .mt-5 }
This tutorial makes use of a sample database. To execute it, type the command
<pre>
    <code class="language-bash">
docker run -it --rm -p 5432:5432 marimplatform/dvdrental   
    </code>
</pre>

This tutorial makes use of a sample database. To execute it, type the command
<pre>
    <code class="language-marim">
table Category
    column Id         type integer
    column Name       type string
    column LastUpdate type timestamp

source DvdRental
    dialect  postgresql
    driver   "org.postgresql.Driver"
    url      "jdbc:postgresql://db:5432/dvdrental"
    property "user" "postgres"
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
    parameter Id type integer not null

    result 
        table Category

    source DvdRental

    statement "
        select category_id as Id, 
               name        as Name,
               last_update as LastUpdate
          from category
         where category_id in (" Id ") 
    "
    </code>
</pre>