---
excerpt: "Surely, there's an easier way."
classes: wide
last_modified_at: 2023-08-13
title: "PostreSQL: Cast a row into an array?"
category: PostgreSQL
tags:
  - note to self
header:
  overlay_image: /assets/teasers/row2array.jpg
  overlay_filter: 0.7
  teaser: /assets/teasers/row2array.jpg
---

It seems surprising difficult to convert a row into an array in postgres. I keep thinking I'm overlooking something obvious. I'll demonstrate.

First, a table and data to mess around with:

```sql
create schema row2array;
set schema 'row2array';
create table i (f1 text, f2 text, f3 text, f4 text, f5 text);
insert into i values ('c1', 'c2', 'c3', 'c4', 'c5');
```

The table looks like this:

```
 f1 │ f2 │ f3 │ f4 │ f5
────┼────┼────┼────┼────
 c1 │ c2 │ c3 │ c4 │ c5
```

I want to convert column values into rows, which would look like this:

```
 array2rows
────────────
 c1
 c2
 c3
 c4
 c5
```

The last step, converting an array into rows is easy:

```sql
select unnest(arr) as array2rows from (select '{c1,c2,c3,c4,c5}'::text[] as arr) as r;
```

The hard part is converting a row into an array.

If I try to cast `i` into an array, postgres (reasonably) says it can't do that:

```sql
select i::text[] from i;
```
```
ERROR:  cannot cast type i to text[]
LINE 1: select i::text[] from i;
                ^
```

Casting to text and then a text array doesn't work either:

```sql
select i::text::text[] from i;
```
```
ERROR:  malformed array literal: "(c1,c2,c3,c4,c5)"
DETAIL:  Array value must start with "{" or dimension information.
```

## Solutions

For clarity, each step in the conversion is broken down to a separate query in a CTE.

### Solution 1


Here'a a solution I copied from somewhere on  the internet a long time ago. It uses json functions for the row-to-array conversion.

```sql
with
  r as (select row(i.*) as rec from i limit 1),
  j as (select row_to_json((select rec from r))),
  jet as (select * from json_each_text((select * from j)) with ordinality ),
  r_array as (select array_agg(value order by ordinality) as a from jet)
select unnest(a) as columns_to_rows1 from r_array;
```


### Solution 2

My solution was inspired by the error message in the 2nd failed attempt above:

```
ERROR:  malformed array literal: "(c1,c2,c3,c4,c5)"
DETAIL:  Array value must start with "{" or dimension information.
```

What if I replace the surrounding parenthesis with curly brackets? (I use `format` to avoid some ugly quoting syntax.)

```sql
with
  r_row as (select i as r from i limit 1),
  r_text as (select r::text as t from r_row),
  r_list as (select substring(t, 2, length(t)-2) as l from r_text),
  r_string as (select format('{ %s }',l) as s from r_list),
  r_array as (select s::text[] as a from r_string)
select unnest(a) as columns_to_rows2 from r_array;
```

It works, but I hate it. There's got to be a more sensible way.

I'll update this post if I learn anything useful.


