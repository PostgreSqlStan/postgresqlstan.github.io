---
last_modified_at: 2023-03-29
title: "Postgres: a question of order"
category: PostgreSQL
tags:
  - SQL
excerpt: "Is text created by generated_series always aggregated in numerical order?"
header:
  overlay_image: /assets/headers/pg-bg.jpg
  overlay_filter: 0.8
  teaser: /assets/headers/matrix.jpg
---

According to PostreSQL [documentation](https://www.postgresql.org/docs/current/queries-order.html):

> After a query has produced an output table (after the select list has been processed) it can optionally be sorted. If sorting is not chosen, the rows will be returned in an unspecified order.

With that in mind, is `ORDER BY` needed to preserve order when aggregating alphanumeric text created by generate_series?

For example, will numerical order always be preserved while aggregating text from generate_series like this:

```sql
select array_agg(f) from
(select 'f' || generate_series(1,3) as f ) s;
```

Or is `ORDER BY` necessary to preserve the numercial row order? Like this:

```sql
select array_agg(f order by n)
  from (select 'f' || n as f, n
          from generate_series(1,3) as t(n) ) s;
```

 My guess: order is currently is preserved in the first example and it's likely to stay that way in future versions of postgres. But based on the postgres docs, as well my second-hand understanding of the SQL standard, I probably shouldn't rely on it.
