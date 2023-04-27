---
title: "Dynamic SQL with PostgreSQL"
category: PostgreSQL
tags:
  - psql
  - PL/pgSQL
excerpt: "How to execute dynamic SQL in a PostgreSQL procedure, demonstrated with psql queries and meta-commands."
classes: wide
header:
  overlay_image: /assets/headers/matrix.jpg
  overlay_filter: 0.5
  teaser: /assets/teasers/proc-create_text_table.jpg
last_modified_at: 2022-04-27
---

This PL/pgSQL procedure creates an unlogged table of numbered text columns.

See [How it Works](#how-it-works) (below) for an explanation of methods used by the procedure and how to accomplish the same with psql meta-commands.

```sql
CREATE PROCEDURE create_text_table (fields INT, tablename TEXT = '_import')
AS $$
/*
DESCRIPTION       create text-only staging table
  ARGUMENTS       fields: number of fields (columns)
                  tablename: [optional, default='_import'] name of table
*/
DECLARE
  fieldlist TEXT := '';
BEGIN
  SELECT string_agg(field, ', ' ORDER BY n)
    FROM (SELECT 'f' || n || ' text' AS field, n
            FROM generate_series(1, fields) AS gs(n) ) sq
    INTO fieldlist;

  EXECUTE FORMAT('CREATE UNLOGGED TABLE %I (%s)', tablename, fieldlist);

  RAISE NOTICE '✅ created table % (% text fields)', tablename, fields;
END; $$
LANGUAGE plpgsql;

COMMENT ON PROCEDURE create_text_table(INT, TEXT) IS
  'create unlogged table with specified # of text fields';
```

:page_facing_up: [create_text_table.sql](https://gist.github.com/PostgreSqlStan/cb346c8971c2ccb9a4b8d9aba9bc497c) (gist)


### Usage

Call the procedure, specifying the number of columns to be created:

```
=> call create_text_table (10);
NOTICE:  ✅ created table _import (10 text fields)
CALL
=> \d _import
        Unlogged table "explore._import"
 Column │ Type │ Collation │ Nullable │ Default
────────┼──────┼───────────┼──────────┼─────────
 f1     │ text │           │          │
 f2     │ text │           │          │
 f3     │ text │           │          │
 f4     │ text │           │          │
 f5     │ text │           │          │
 f6     │ text │           │          │
 f7     │ text │           │          │
 f8     │ text │           │          │
 f9     │ text │           │          │
 f10    │ text │           │          │
```

Specify a table name in the second parameter to override the default:

```
=> call create_text_table (2, 'i2');
NOTICE:  ✅ created table i2 (2 text fields)
CALL
=> \d
         List of relations
 Schema  │  Name   │ Type  │ Owner
─────────┼─────────┼───────┼───────
 explore │ _import │ table │ stan
 explore │ i2      │ table │ stan
```


## How it works

The focus here is on SQL queries and functions, not the PL/pgSQL language. For information regarding PL/pgSQL structure and syntax, see [Structure of PL/pgSQL](https://www.postgresql.org/docs/current/plpgsql-structure.html), which applies to both functions and procedures.

If you don't have psql installed, I suggest using the [Postgres Playground](https://www.crunchydata.com/developers/playground) to run PostgreSQL/psql in a web browser.

Example queries are available as a separate gist: :page_facing_up: [examples.sql](https://gist.github.com/PostgreSqlStan/cbf0592fd6e2f8031c47b1c5b6208a8e)


### psql variables

First, a brief introduction to psql variables, which are needed to replace the procedure parameters, `fields` and `tablename`, in interactive queries.

Declare a psql variable named `fields` with the `\set` meta-command:

```sql
\set fields 3
```

Precede the name of the variable with a colon to use its value in a command:

```
=> \echo :fields
3
=> select :fields;
 ?column?
──────────
        3
```

Text values are a little more complicated to deal with:

```
=> \set tablename notcolumn
=> \echo :tablename
notcolumn
=> select :tablename;
ERROR:  column "notcolumn" does not exist
LINE 1: select notcolumn;
               ^
```

Write a colon followed by the variable name in single quotes to use a variable as a *SQL literal*:

```
=> select :'tablename';
 ?column?
───────────
 notcolumn
```

See psql [Variables](https://www.postgresql.org/docs/current/app-psql.html#APP-PSQL-VARIABLES) and [SQL Interpolation](https://www.postgresql.org/docs/current/app-psql.html#APP-PSQL-INTERPOLATION) for more information.


### Generate a list of field definitions

The first command in the procedure generates a comma-delimited list of field (column) definitions, storing the output in a variable:

```sql
-- 1st command in create_text_table procedure
SELECT string_agg(field, ', ' ORDER BY n)
  FROM (SELECT 'f' || n || ' text' AS field, n
          FROM generate_series(1, fields) AS gs(n) ) sq
  INTO fieldlist;
```

I'll demonstrate how the query works by constructing it piece by piece, starting with the `generate_series` function.

Use `generate_series` in a `FROM` clause, using the `fields` variable as the second parameter:

```
stan=> \set fields 3
stan=> select * from generate_series(1, :fields);
 generate_series
─────────────────
               1
               2
               3
```

To rename the column from `generate_series` to something unique (and shorter), add a *table alias list* to the `FROM` clause:

```
=> select n from generate_series (1, :fields) as gs(n);
 n
───
 1
 2
 3
 ```

Next, add a column of concatenated text to create a field definition (e.g., "f1 text"), using an alias to rename it `field`:

```
=> select n, 'f' || n || ' text' as field
   from generate_series(1,:fields) as gs(n);
 n │  field
───┼─────────
 1 │ f1 text
 2 │ f2 text
 3 │ f3 text
```

Finally, to aggregate the `field` column into a string: 1) move the above query into a subquery, requiring an alias, and 2) use the `string_agg` function to aggregate the `field` column into a string delimited by ", ":

```
=> select string_agg(field, ', ' order by n) as fieldlist
   from (select n, 'f' || n || ' text' as field
         from generate_series (1, :fields) as gs(n) ) sq;
         fieldlist
───────────────────────────
 f1 text, f2 text, f3 text
```

Notice the use of `order by n` in the function call, which insures rows are aggregated in the correct order.

>Ordinarily, the input rows are fed to the aggregate function in an unspecified order. In many cases this does not matter…However, some aggregate functions (such as array_agg and string_agg) produce results that depend on the ordering of the input rows. When using such an aggregate, the optional order_by_clause can be used to specify the desired ordering. — [SQL Expressions: Aggregates](https://www.postgresql.org/docs/current/sql-expressions.html#SYNTAX-AGGREGATES)

**Store output in a psql variable**

In the procedure, the result of the query is stored in the PL/pgSQL variable `fieldlist`, which is not possible with a SQL query. Instead, the output column is renamed `fieldlist` for use with the `\gset` meta-command.

Assuming the previous example is still in the current query buffer, enter `\gset` to store its output in a psql variable named `fieldlist`:

```
=> \gset
=> \echo :'fieldlist'
'f1 text, f2 text, f3 text'
```

This method can be used to set multiple variables with a single query. For example:

```sql
=> select 5 as fields, 'demo' as tablename \gset
=> select :fields, :'tablename';
 ?column? │ ?column?
──────────┼──────────
        5 │ demo
```

### Format and execute dynamic SQL

The second command in the procedure constructs and executes a SQL statement:

```sql
-- 2nd command in create_text_table procedure
EXECUTE FORMAT('CREATE UNLOGGED TABLE %I (%s)', tablename, fieldlist);
```

**Format**

The [`format`](https://www.postgresql.org/docs/current/functions-string.html#FUNCTIONS-STRING-FORMAT) function produces output formatted according to a format string. The `%I` specification uses automatic quoting for tables or columns (equivalent to `quote_ident`) and `%s` formats as a simple string.

```
=> select format('create unlogged table %I (%s)', :'tablename',:'fieldlist');
                         format
────────────────────────────────────────────────────────
 create unlogged table demo (f1 text, f2 text, f3 text)

=> \set tablename 'quote test'
=> select format('create unlogged table %I (%s)', :'tablename',:'fieldlist');
                             format
────────────────────────────────────────────────────────────────
 create unlogged table "quote test" (f1 text, f2 text, f3 text)
```

See [Quoting Values in Dynamic Queries](https://www.postgresql.org/docs/current/plpgsql-statements.html#PLPGSQL-QUOTE-LITERAL-EXAMPLE) for more information.

**Execute**

Instead of the PL/pgSQL `EXECUTE` command, which cannot be used in a SQL query, we'll have to use a meta-command to execute dynamic SQL interactively.

Use `\gexec` to execute the output of the current query buffer:

```
=> \gexec
CREATE TABLE
=> \d "quote test"
      Unlogged table "examples.quote test"
 Column │ Type │ Collation │ Nullable │ Default
────────┼──────┼───────────┼──────────┼─────────
 f1     │ text │           │          │
 f2     │ text │           │          │
 f3     │ text │           │          │
```

 See [Executing Dynamic Commands](https://www.postgresql.org/docs/current/plpgsql-statements.html#PLPGSQL-STATEMENTS-EXECUTING-DYN) for more information.


### Raise a notice

The last command of the procedure [raises a notice](https://www.postgresql.org/docs/current/plpgsql-errors-and-messages.html), which cannot be done with a SQL query:

```sql
-- last command of create_text_table procedure:
RAISE NOTICE '✅ created table % (% text fields)', tablename, fields;
```

For the sake of completeness, here's how to do it with a [`DO`](https://www.postgresql.org/docs/current/sql-do.html) statement:

```
=> do $$ begin raise notice 'demo finished'; end $$ ;
NOTICE:  demo finished
DO
```
