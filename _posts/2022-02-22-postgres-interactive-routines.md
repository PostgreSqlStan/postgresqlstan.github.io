---
title: "Postgres procedures that return data aren't fun to call."
category: PostgreSQL
---

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
