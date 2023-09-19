---
title: "PostgreSQL - scoping parameters in plpgsql"
excerpt: "Scope plpgsql parameters with qualified names, e.g. my_function.my_param."
header:
  overlay_image: /assets/headers/pg_bg_header.jpg
  overlay_filter: 0.85
  teaser: /assets/teasers/alt-solution.jpg
category: PostgreSQL
tags:
  - plpgsql
---

A recent [post](https://fluca1978.github.io/2023/09/19/PostgreSQLFORAutodeclaredVariables.html) from Luca Ferrari demonstrated a potential unexpected result from using variables in a `FOR` loop in plpgsql, as well as a reasonable workaround.

I was blissfully unaware of the danger presented by the `FOR` loop, but have dealt with the scope of variables in plpgsql a few times.

Another reliable way to deal with the issue is using the qualified name of the variables, which for this example would be `a_table.i`, `a_table.j`, `a_table.k`.

## Example

```sql
CREATE OR REPLACE FUNCTION a_table()
 RETURNS TABLE(i integer, j integer, k integer)
 LANGUAGE plpgsql
AS $function$
BEGIN
FOR i IN 1 .. 2 LOOP
    FOR j IN 1 .. 2 LOOP
    FOR k IN 1 .. 2 LOOP
    a_table.i := i;
    a_table.j := j;
    a_table.k := k;
    RAISE INFO 'i=%, j=%, k=%', i, j, k;
    RETURN NEXT;
END LOOP;
    END LOOP;
END LOOP;
END
$function$
```

### Result

```
=> select * from a_table();
INFO:  i=1, j=1, k=1
INFO:  i=1, j=1, k=2
INFO:  i=1, j=2, k=1
INFO:  i=1, j=2, k=2
INFO:  i=2, j=1, k=1
INFO:  i=2, j=1, k=2
INFO:  i=2, j=2, k=1
INFO:  i=2, j=2, k=2
 i │ j │ k
───┼───┼───
 1 │ 1 │ 1
 1 │ 1 │ 2
 1 │ 2 │ 1
 1 │ 2 │ 2
 2 │ 1 │ 1
 2 │ 1 │ 2
 2 │ 2 │ 1
 2 │ 2 │ 2
(8 rows)
```



