---
last_modified_at: 2023-01-26
title: "Importing multiline JSON objects into PostgreSQL"
category: PostgreSQL
tags: psql JSON pgtab
classes: wide
excerpt: "Summary and examples of what I've learned about loading multiline JSON objects into postgres"
header:
  overlay_image: /assets/headers/loading-json.jpg
  overlay_filter: 0.8
  teaser: /assets/headers/loading-json.jpg
---

The easy way to load a JSON object into postgres is to use one of the many existing external tools, but I wanted to see what I can do with postgres alone. I'm funny that way.

This is what I've learned so far.

### simple JSON: import as tab delimited

I'll start with this minimal Tabular Data Package [example](https://specs.frictionlessdata.io/tabular-data-package/#example), which I copied and saved as `datapackage.json`.

```json
{
  "profile": "tabular-data-package",
  "name": "my-dataset",
  // here we list the data files in this dataset
  "resources": [
    {
      "path": "data.csv",
      "profile": "tabular-data-resource",
      "schema": {
        "fields": [
          {
            "name": "var1",
            "type": "string"
          },
          {
            "name": "var2",
            "type": "integer"
          },
          {
            "name": "var3",
            "type": "number"
          }
        ]
      }
    }
  ]
}
```

(The fourth line, using `//` as a comment, is not valid JSON. Delete it.)

From psql, create a schema and table for some experiments:

```
stan=> create schema json_demo;
CREATE SCHEMA
stan=> create unlogged table t(t text);
CREATE TABLE
```

Load as tab delimited text (the default format for the COPY command):

```
stan=> \copy t from datapackage.json
COPY 26
```

Concatenate the lines and convert to JSON:

```
stan=> select string_agg(t, E'\n')::json from t;
                string_agg
───────────────────────────────────────────
 {                                        ↵
   "profile": "tabular-data-package",     ↵
   "name": "my-dataset",                  ↵
   "resources": [                         ↵
     {                                    ↵
       "path": "data.csv",                ↵
       "profile": "tabular-data-resource",↵
       "schema": {                        ↵
         "fields": [                      ↵
           {                              ↵
             "name": "var1",              ↵
             "type": "string"             ↵
           },                             ↵
           {                              ↵
             "name": "var2",              ↵
             "type": "integer"            ↵
           },                             ↵
           {                              ↵
             "name": "var3",              ↵
             "type": "number"             ↵
           }                              ↵
         ]                                ↵
       }                                  ↵
     }                                    ↵
   ]                                      ↵
 }
```

Success.

### use csv format with special parameters

However, if the JSON contains backslashes, like the `readme` in this [data package](https://pkgstore.datahub.io/core/country-list/11/datapackage.json), it will fail:

```
stan=> truncate t;
TRUNCATE TABLE
stan=> \copy t from datapackage.json
COPY 220
stan=> select string_agg(t, E'\n')::json from t;
ERROR:  invalid input syntax for type json
DETAIL:  Character with value 0x0a must be escaped.
CONTEXT:  JSON data, line 24: ...country names and code elements. This list states
```

Andrew Dunstan's [solution](#dunstan) is to use the csv format with these options: `csv quote e'\x01' delimiter e'\x02'`

```
stan=> truncate t;
TRUNCATE TABLE
stan=> \copy t from datapackage.json csv quote e'\x01' delimiter e'\x02'
COPY 220
stan=> select (string_agg(t, E'\n')::json)->'name' as name from t;
      name
────────────────
 "country-list"
```

This method often works, but not always.

### use \set to load into a variable

Perhaps due to the (repeated) escaped quote character sequences, I was unable to load this file with Dunstan's method: [https://datahub.io/core/usa-education-budget-analysis/datapackage.json](https://datahub.io/core/usa-education-budget-analysis/datapackage.json).

```
stan=> truncate t;
TRUNCATE TABLE
stan=> \copy t from datapackage.json csv quote e'\x01' delimiter e'\x02'
COPY 450
stan=> select string_agg(t, E'\n')::json as j from t;
ERROR:  invalid input syntax for type json
DETAIL:  Expected string, but found "}".
CONTEXT:  JSON data, line 119:     }...
```

Stackoverflow's Doctor Eval [recommends](#eval) loading the file into a psql variable, which works on this file.

```
stan=> \set s `cat datapackage.json`
stan=> select (:'s'::json)->'name' as name;
              name
─────────────────────────────────
 "usa-education-budget-analysis"
```

There must be a limit to how large of a file can be processed this way, but I haven't encountered it yet and am not in a rush to do so. (I've learned enough for now.)

Discovering the limits to this method is left as an exercise to the reader.

### use \\lo_import to load into a variable

Finally, a slightly more complicated [method](#rhodium) uses the psql `\lo_import` command to load the file into a variable, which also works on the above file. Since the simpler method has worked for my tests (so far), I'll stick with that for the time being.

## Sources

<a name="dunstan"></a>Andrew Dunstan's PostgreSQL and Technical blog
: [Importing JSON data](http://adpgtech.blogspot.com/2014/09/importing-json-data.html)

```
\copy t from datapackage.json csv quote e'\x01' delimiter e'\x02'
select string_agg(t, E'\n')::json from t;
```

<a name="eval"></a>stackoverflow: Doctor Eval
: [How can I import a JSON file into PostgreSQL?](https://stackoverflow.com/questions/39224382/how-can-i-import-a-json-file-into-postgresql/48396608#48396608) - load into psql variable

```
\set s `cat datapackage.json`
select jsonb_pretty(:'s'::jsonb);
```

<a name="rhodium"></a>The Rhodium Toad
: [Loading data from JSON files](http://blog.rhodiumtoad.org.uk/2018/02/11/loading-data-from-json-files/) -

```
BEGIN;
\set filename datapackage.json
\lo_import :filename
\set obj :LASTOID
INSERT INTO import_json
  SELECT *
    FROM convert_from(lo_get(:'obj'),'UTF8');
\lo_unlink :obj
COMMIT;
```

