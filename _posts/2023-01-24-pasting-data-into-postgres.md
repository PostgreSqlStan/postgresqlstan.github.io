---
last_modified_at: 2023-01-26
title: "psql: how to paste data into a table"
category: PostgreSQL
tags:
  - psql
classes: wide
excerpt: "`\\copy mytable from stdin + paste + \\. + enter`"
header:
  overlay_image: /assets/headers/psql_stan.jpg
  overlay_filter: 0.8
  teaser: /assets/teasers/copy-from-stdin.jpg
---

Using [stdin](https://en.wikipedia.org/wiki/Standard_streams) is an easy and convenient way to enter or paste multiple lines of data in psql, something I wish I'd learned **much** earlier.

I'll use this data (`/usr/share/misc/birthtokens` on macOS) to demonstrate:
```
# Birthday : Birth Stone : Birth Flower
# @(#)birthtoken  8.1 (Berkeley) 6/8/93
January:Garnet:Carnation
February:Amethyst:Violet
March:Aquamarine:Jonquil
April:Diamond:Sweetpea
May:Emerald:Lily Of The Valley
June:Pearl:Rose
July:Ruby:Larkspur
August:Peridot:Gladiolus
September:Sapphire:Aster
October:Opal:Calendula
November:Topaz:Chrysanthemum
December:Turquoise:Narcissus
```

---

In psql, create an appropriate schema and table:

```sql
create schema paste_demo;
set search_path to paste_demo;
create table birthtoken(month text, stone text, flower text);
```

Use the `\copy` command, specifying the delimiter, with `stdin` as the source:

```sql
\copy birthtoken from stdin delimiter ':'
```

You should see this prompt:
```
Enter data to be copied followed by a newline.
End with a backslash and a period on a line by itself, or an EOF signal.
>>
```

Copy and paste the data from above (exluding the lines starting with "#") and press enter. Then enter backslash followed by a period.

Here's what it looked like when I did so:

![Screenshot: pasting data into stdin in psql](/assets/ss/pasting-data-into-postgres/copy-from-stdin.jpg)

The accumulated prompts (`>>`) might look weird, but it's normal. As you can verify, the data is now in the table:

```sql
table birthtoken;
```
```
   month   │   stone    │       flower
───────────┼────────────┼────────────────────
 January   │ Garnet     │ Carnation
 February  │ Amethyst   │ Violet
 March     │ Aquamarine │ Jonquil
 April     │ Diamond    │ Sweetpea
 May       │ Emerald    │ Lily Of The Valley
 June      │ Pearl      │ Rose
 July      │ Ruby       │ Larkspur
 August    │ Peridot    │ Gladiolus
 September │ Sapphire   │ Aster
 October   │ Opal       │ Calendula
 November  │ Topaz      │ Chrysanthemum
 December  │ Turquoise  │ Narcissus
(12 rows)
```
