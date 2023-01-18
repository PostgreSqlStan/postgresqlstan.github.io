---
last_modified_at: 2023-01-17
title: "Loading JSON into Postgres (learning notes)"
category:
tags:
  - note to self
classes: wide
excerpt: "Lesson: load JSON as binary to avoid (strange) problems with escaped characters"
header:
  overlay_image: /assets/headers/psqlstan.jpg
  overlay_filter: 0.8
  teaser: /assets/headers/psqlstan.jpg
---

Dangers of trying to load JSON as lines of text into Postgres.

## reproduce error (on my machine)

create dir & download json:

```zsh
mkdir jsonproblem
cd !$
curl -OL https://datahub.io/core/usa-education-budget-analysis/datapackage.json
```

load into postgres (from psql):

```sql
create schema jsonproblem;
set search_path to jsonproblem;
create table t(t text);
\copy t from datapackage.json csv quote e'\x01' delimiter e'\x02'
select string_agg(t,E'\n')::jsonb from t;
```

Results in this error:
```
ERROR:  invalid input syntax for type json
DETAIL:  Expected string, but found "}".
CONTEXT:  JSON data, line 119:     }...
```

## try some stuff

view by row #:

```sql
CREATE VIEW ln AS
  select row_number() over() as n, t from t;
select t as lines from ln where n > 117 and n < 121;
```
```
               lines
───────────────────────────────────
         "lineTerminator": "\r\n",
     },
         "quoteChar": "\"",
```

Back to shell, lines 118-120 from the file:

```zsh
cat datapackage.json | sed -n '118,120p'
```
```
        "lineTerminator": "\r\n",
        "quoteChar": "\"",
        "skipInitialSpace": false
```

😦

I suspect this is related to quoting/escaping issues. On the other hand, a similar line appears earlier in the file:

```zsh
cat datapackage.json | sed -n '53,55p'
```
```
        "lineTerminator": "\r\n",
        "quoteChar": "\"",
        "skipInitialSpace": false
```

Those lines loaded without error:

```sql
psql
set search_path to jsonproblem;
select t as lines from ln where n > 52 and n < 56;
```
```
               lines
───────────────────────────────────
         "lineTerminator": "\r\n",
         "quoteChar": "\"",
         "skipInitialSpace": false
```

🤔

Let's see what happens if line 119 is deleted before loading:

```sql
\copy t from program 'cat datapackage.json|sed ''119d''' csv quote e'\x01' delimiter e'\x02'
select string_agg(t,E'\n')::jsonb from t;
```

No error. 😯

It also works if I delete the similar line 53 (leaving 119 untouched):

```sql
truncate t;
\copy t from program 'cat datapackage.json|sed ''53d''' csv quote e'\x01' delimiter e'\x02'
select string_agg(t,E'\n')::jsonb from t;
```

No error.

## Solution

As suspected, loading as a binary JSON avoids this error.

Method from: [http://blog.rhodiumtoad.org.uk/2018/02/11/loading-data-from-json-files/](http://blog.rhodiumtoad.org.uk/2018/02/11/loading-data-from-json-files/)

From psql:

```
set search_path to jsonproblem;
\set filename datapackage.json
\set ON_ERROR_ROLLBACK interactive
begin;
\lo_import :filename
\set obj :LASTOID
create table testtab as
  select *
    from convert_from(lo_get(:'obj'),'UTF8') as t;
-- clean up the object
\lo_unlink :obj
commit;
```

Loaded this way, `select t::jsonb from testtab;` produces no error.

🥹
