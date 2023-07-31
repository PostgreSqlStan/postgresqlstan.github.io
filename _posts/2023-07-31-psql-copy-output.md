---
last_modified_at: 2023-07-31
classes: wide
title: "psql tip: copy by sending output to the clipboard"
category: PostgreSQL
tags:
  - psql
excerpt: "Redirecting output is a very easy way to copy psql query output."
header:
  overlay_image: /assets/headers/matrix.jpg
  overlay_filter: 0.4
  teaser: /assets/teasers/psql-copy-output.jpg
---

Recently, while practicing the art of reading postgres query plans, I was reminded how useful this “trick” is and realized I should share it.

To re-run the previous query and send the output to the macOS clipboard:

```
\g | pbcopy
```

{% capture n1 %}
The psql meta-command `\g` redirects output to [standard output](https://en.wikipedia.org/wiki/Standard_streams) and the shell command `| pbcopy` pipes it to the macOS clipboard. Programs similar to `pbcopy` are available for Linux; I don't know about Windows.
{% endcapture %}<div class="notice">{{ n1 | markdownify }}</div>

For example, from psql:

```
=> explain select * from pg_tables;
                                    QUERY PLAN
──────────────────────────────────────────────────────────────────────────────────
 Hash Left Join  (cost=2.27..19.35 rows=69 width=260)
   Hash Cond: (c.reltablespace = t.oid)
   ->  Hash Left Join  (cost=1.23..17.57 rows=69 width=140)
         Hash Cond: (c.relnamespace = n.oid)
         ->  Seq Scan on pg_class c  (cost=0.00..16.09 rows=69 width=80)
               Filter: (relkind = ANY ('{r,p}'::"char"[]))
         ->  Hash  (cost=1.10..1.10 rows=10 width=68)
               ->  Seq Scan on pg_namespace n  (cost=0.00..1.10 rows=10 width=68)
   ->  Hash  (cost=1.02..1.02 rows=2 width=68)
         ->  Seq Scan on pg_tablespace t  (cost=0.00..1.02 rows=2 width=68)
(10 rows)

=> \g | pbcopy
```

Of course, you might not want to re-run a lengthy query or one that modifies the database. To redirect a query's output without running it again, add `\g | pbcopy`, instead of a semicolon, to the end of it:

```
=> create table t(i int);
CREATE TABLE
=> insert into t(i) select * from generate_series(1,100) returning i \g | pbcopy
INSERT 0 100
```

---

{% capture n2 %}
**Related:**<br>
CLI Notes - [psql: how to paste data into a table](/postgresql/pasting-data-into-postgres/)<br>
PostgreSQL Docs - [psql](https://www.postgresql.org/docs/current/app-psql.html)
{% endcapture %}<div class="notice--primary">{{ n2 | markdownify }}</div>





