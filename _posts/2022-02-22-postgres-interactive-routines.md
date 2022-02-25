---
last_modified_at: 2022-02-24
title: "Learning how to use Postgres procedures"
category: PostgreSQL
tags:
  - note to self
---

*Personal learning notes*

I have a collection of functions designed to be used interactively in psql. Because the `call` syntax seems more appropriate for code that performs DDL actions, I was hoping to replace some functions with procedures. So:

```sql
> select do_something();
```
could become:

```sql
> call do_something();
```

Unfortunately, it turns out I have to supply a value for the OUT argument:

```sql
> call do_something(null::text);
```

Not so convenient for interactive use. Oh, well. Maybe in a future version.

### Update

If I define an INOUT argument with a default value for the procedure, I can call it without needing to passing a dummy argument.

This works how I want it to:

```sql
CREATE OR REPLACE PROCEDURE p_import (
     cols_cnt     INT                ,
     tlb_name     TEXT = '_import'   ,
     INOUT msg TEXT = ''
                                     )
AS $PROC$
DECLARE sql_txt         TEXT := '';
BEGIN
  -- generate list of columns used in execute statement
    WITH cols AS(SELECT 'f' || n || ' TEXT' AS f
               FROM GENERATE_SERIES(1,cols_cnt) AS n)
  SELECT ARRAY_TO_STRING(ARRAY_AGG(f),',')
    INTO sql_txt
    FROM cols;

  sql_txt := FORMAT('CREATE UNLOGGED TABLE %s (%s)', tlb_name, sql_txt);
  EXECUTE sql_txt;

  msg := 'created import table';
END; $PROC$ LANGUAGE 'plpgsql';
```

(The simplified procedure above creates a table with the specified number of text columns, used to examine data that doesn't have column names.)

Now, I can call it with this syntax:

```sql
> call p_import(10);
         msg
──────────────────────
 created import table
```
