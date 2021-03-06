# Session 7 - Fulltext

Grading: 2pt

## Setup

```
docker run -p 5432:5432 fiitpdt/postgres-fulltext
```

## Labs

Implement fulltext search in contracts. The search should be case insensitive and accents insensitive. It should support searching by
- contract name
- department
- customer
- supplier
- by supplier ICO
- and by contract code (anywhere inside contract code, such as `OIaMIS`)

Boost newer contracts, so they have a higher chance of being at top positions.

Try to find some limitations of your solutions - find a few queries (2 cases are enough) that are not showing expected results, or worse, are not showing any results. Think about why it's happening and propose a solution (no need to implement it).

Hints:

- The data is in slovak, so you will need to use an appropriate dictionary. Type \dF inside `psql` to see available configurations for your installation. There's no support for slovak language, don't worry about it, just create an unaccenting configuration based on the `simple` configuration.
   ```
    oz=# \dF
                  List of text search configurations
      Schema   |    Name    |              Description
    ------------+------------+---------------------------------------
    pg_catalog | danish     | configuration for danish language
    pg_catalog | dutch      | configuration for dutch language
    pg_catalog | english    | configuration for english language
    pg_catalog | finnish    | configuration for finnish language
    pg_catalog | french     | configuration for french language
    pg_catalog | german     | configuration for german language
    pg_catalog | hungarian  | configuration for hungarian language
    pg_catalog | italian    | configuration for italian language
    pg_catalog | norwegian  | configuration for norwegian language
    pg_catalog | portuguese | configuration for portuguese language
    pg_catalog | romanian   | configuration for romanian language
    pg_catalog | russian    | configuration for russian language
    pg_catalog | simple     | simple configuration
    pg_catalog | spanish    | configuration for spanish language
    pg_catalog | swedish    | configuration for swedish language
    pg_catalog | turkish    | configuration for turkish language
   ```
- To implement boosting, you must combine (e.g. multiply) the fulltext score with a boosting value. Since you want to boost by "freshness", compute a difference of current date and the `published_on` date. The result will be an `interval` datatype, which you want to convert to number of days, e.g. `extract(days from (now() - published_on))`. You will need to apply some smoothing functions or other rescaling to the number of days to dampen its impact and retain the value of fulltext score.

- Searching by ICO will require a different kind of processing. You query will need to have (at least) 2 parts - one handling the fulltext search, and the other handling ICO search. Make sure to use a trigram index to keep the '%%' search fast.

- If you don't want to concatenate the fields repeatedly (it really makes the query harder to read and is error-prone), feel free to create an extra column (with `ts_vector` type) and update the column with the concatenated string. You can now use this "computed" column in your queries.

- You will need to copy&paste the input query into several places in your SQL query. It is OK and don't worry about it. Under "normal" circumstances, the SQL query would be dynamically generated by your application, with the input query interpolated into it.

## Recommended reading
- https://www.postgresql.org/docs/9.4/static/textsearch.html
- https://www.postgresql.org/docs/9.4/static/unaccent.html
- https://www.postgresql.org/docs/9.4/pgtrgm.html

