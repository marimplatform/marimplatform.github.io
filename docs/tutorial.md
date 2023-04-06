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
    <code class="language-java">
table Category
    column Id         type integer
    column Name       type string
    column LastUpdate type timestamp

source DvdRental
    dialect postgresql
    driver "org.postgresql.Driver"
    url "jdbc:postgresql://db:5432/dvdrental"
    property "user" "postgres"
    property "password" "postgres"

query Categories
    result
        table Category

    source DvdRental	

    statement "
        select category_id as Id, 
               name        as Name,
               last_update as LastUpdate
          from category
    "

query Category
    parameter Id type integer collection required

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

 query Films
    result 
        table
            column Id              type integer
            column Title           type string
            column Description     type string
            column ReleaseYear     type integer			
            column LanguageId      type integer			
            column RentalDuration  type integer
            column RentalRate      type decimal
            column Length          type integer
            column ReplacementCost type decimal
            column Rating          type string
            column LastUpdate      type timestamp
            column SpecialFeatures type string
            column Fulltext        type string

    source DvdRental

    statement "
        select film_id          as Id,
               title            as Title,
               description      as Description,
               release_year     as ReleaseYear,
               language_id      as LanguageId,
               ental_duration   as RentalDuration,
               rental_rate      as RentalRate,
               length           as Length,
               replacement_cost as ReplacementCost,
               rating           as Rating,
               last_update      as LastUpdate,
               special_features as SpecialFeatures,
               fulltext         as Fulltext
          from film
    "
    </code>
</pre>