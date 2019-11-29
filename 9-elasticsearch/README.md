# Session 9 - Elasticsearch

Grading: 4pt

## Setup

```
docker run -p 9200:9200 -e "discovery.type=single-node" elasticsearch:7.4.2
```

## Labs

Work with your GIS project data. Choose a simple but meaningful subset of the data and import it from PostgreSQL into Elasticsearch. Do not import it manually, but write a code which will do the import for you. Don't import all columns from the database, select 3-4 fields that are interesting.

Write an Elasticsearch query which will allow you to search the imported objects by name - don't worry about search quality, a basic match query should be enough for this lab. Write some interesting (meaningful) aggregations (1-2 are enough, don't worry about nested aggregations).

Don't write any front-end code or UI, just the import code and the Elasticsearch query.

An example for inspiration: Import all rivers from your database: their names, lengths, and cities that they cross. Your query will search the cities by name and show a breakdown (aggregation) of all cities that are being crossed by the matched rivers.
If you can't come with your own mini-assignmen idea, feel free to use this example.

As a rough guideline:

1. Start by writing the SQL query or queries which will select the data that you want to import to Elasticsearch. Keep in mind that you need to flatten the data, as there are no relations in Elasticsearch. Do the neccessary joins, interesction computations, etc.
2. Once you have the data, design the document, e.g., for my example above:

```
{
  "name": "Danube",
  "length": 324, // totally made up
  "cities": ["Bratislava", "Vienna", "Budapest"]
}
```

3. Prepare a mapping, feel free to do it "by hand", e.g. via Postman/curl, you don't need to write code for this.
4. Write a code in a language of your choice which will run the SQL queries, build JSON documents and index them in Elasticsearch
5. You can always check the data in elasticsearch by running a simple `GET` request at the `_search` endpoint.
6. Once you have the data, write the Elasticsearch query. It should be really simple, a match query with an aggregation.
