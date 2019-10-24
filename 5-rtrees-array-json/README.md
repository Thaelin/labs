# Lab 5 - R-Trees indices, Array and JSON datatypes

Grading: 3pt

## Setup

Download the docker container as usual

````
docker run -p 5432:5432 fiitpdt/postgis
````

You can safely ignore errors about extension being already present and there are some
constraint validation failures, but don't worry about them.

| Attribute| Value                  |
|----------|------------------------|
| Login    | postgres               |
| Database | gis                    |
| Password | none, leave blank      |
| Spatial reference system | WGS 84 |


## Labs


1. Write a query which finds all restaurants (point, `amenity = 'restaurant'`) within
   1000 meters of 'Fakulta informatiky a informačných technológií STU'
   (polygon). Select the restaurant name and distance in meters. Sort the
   output by distance - closest restaurant first.

2. Check the query plan and measure how long the query takes. Now make it as
   fast as possible. Make sure to also use geo-indices, but don't expect large
   improvements. The dataset is small, and filtering in `amenity='restaurant'`
   will greatly limit the search space anyway.

   Take a measurement and note if the query is faster now.

   Hint: If you are computing distance from geography for accurate measurement,
   your index will also need to be created on geography, e.g. `... using
   gist((way::geography))`. Notice the double parens - they are required,
   otherwise the parser will be confused by the typecast and throw a syntax
   error.

3. Update the query to generate a geojson. The output should be a single row
   containg a json array like this:


```json
[
   {
     "name": "Drag",
     "dist": 99.9,
     "way": {
       "type": "Point",
       "coordinates": [17.07, 48.24]
     }
   },
   {
     "name": "Koliba",
     "dist": ...
   },
   ...
]
```

Ignore the formatting, the output of your query will not be nicely indented.

Hint: Use `st_asgeojson` to convert the `way` geometry to JSON. Note that
`st_asgeojson` will return the JSON as string, not as a json datatype, so you
will need to cast it to json using `::json`.
You will probably need to use `row_to_json` to convert each row into a json,
`array_agg` to turn all rows into a single row and then `array_to_json` to
convert it back to json.
