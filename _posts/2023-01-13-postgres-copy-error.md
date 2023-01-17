---
last_modified_at: 2023-01-13
title: "Postgres mystery: why does VACUUM prevent an error?"
category: PostgreSQL
tags:
  - note to self
classes: wide
excerpt: "Will revisit this issue. (Any errors with postgres are likely my fault.)"
header:
  overlay_image: /assets/headers/psqlstan.jpg
  overlay_filter: 0.8
  teaser: /assets/headers/psqlstan.jpg
---

When I run this psql script I get an error:

```
\copy _import from packages/corruption-perceptions-index/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();

\copy _import from packages/eu-emissions-trading-system/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();

\copy _import from packages/gdp/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();

\copy _import from packages/household-income-us-historical/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();

\copy _import from packages/s-and-p-500-companies/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();

\copy _import from packages/usa-education-budget-analysis/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();

\copy _import from packages/world-bank_sp.pop.totl/datapackage.json csv quote e'\x01' delimiter e'\x02'
CALL import();
```

After the fourth file is processed by the import() procedure, the next `\COPY` command fails:

```
psql:load_packages.psql:19: ERROR:  null value in column "name" of relation "field" violates not-null constraint
```

I tried wrapping each COPY/import() process in BEGIN/COMMIT but it didn't help.

On a wild hunch, I added `VACUUM _import` after the the first `import()` call and it fixed the problem.

🤗

But now I want to understand what caused the error and how a single `VACUUM` command solved it.

More details are available if anyone wants to help me figure this out.


