---
last_modified_at: 2023-01-26
title: "Self-concatenating table"
category: PostgreSQL
tags:
  - Questionable practices
classes: wide
excerpt: "Probably a terrible idea, but I couldn't resist trying."
header:
  overlay_image: /assets/headers/psql_stan.jpg
  overlay_filter: 0.8
  teaser: /assets/teasers/terminal.jpg
---

Can I make a postgres table automagically concatenate itself into a single row? 🧐 Let's find out.

## :warning: Don't try this in production

Create a schema/table to play with:

```
stan=> create schema questionable;
CREATE SCHEMA
stan=> set search_path to questionable ;
SET
stan=> create table demo(t text);
CREATE TABLE
```

Create a function that uses a temporary table to collect aggragated rows and reinsert them as a single row after emptying the table:

```sql
CREATE OR REPLACE FUNCTION tf_concatenate_rows()
  RETURNS TRIGGER AS $TF$
BEGIN
  IF (SELECT count(*) FROM demo) > 1 THEN
    CREATE TEMPORARY TABLE _squeeze(t TEXT);
    INSERT INTO _squeeze SELECT string_agg(t, E'\n') FROM demo;
    DELETE FROM demo;
    INSERT INTO demo SELECT * FROM _squeeze;
    DROP TABLE _squeeze;
  END IF;
  RETURN NEW;
END;
$TF$ LANGUAGE plpgsql;
```

{% capture note %}
See PostgreSQL Documentation: [Triggers](https://www.postgresql.org/docs/15/triggers.html), [Trigger Functions](https://www.postgresql.org/docs/current/plpgsql-trigger.html).
{% endcapture %}<div class="notice">{{ note | markdownify }}</div>

Create the table trigger:

```sql
CREATE TRIGGER concatenate_rows AFTER INSERT ON demo
  EXECUTE FUNCTION tf_concatenate_rows();
```

Let's see what this bad boy can do:

```
stan=> insert into demo select 'insert first row';
INSERT 0 1
stan=> table demo;
        t
──────────────────
 insert first row
(1 row)

stan=> insert into demo select 'insert 2nd row';
INSERT 0 1
stan=> table demo;
        t
──────────────────
 insert first row↵
 insert 2nd row
(1 row)

stan=> insert into demo values ('this'), ('is'), ('a'), ('bad'), ('idea?');
INSERT 0 5
stan=> table demo;
        t
──────────────────
 insert first row↵
 insert 2nd row  ↵
 this            ↵
 is              ↵
 a               ↵
 bad             ↵
 idea?
(1 row)

```

🙃

Of course, I had a semi-useful purpose for trying this, concatenating multiline JSON objects into a single line.

```
stan=> truncate demo;
TRUNCATE TABLE
stan=> \copy demo from stdin csv quote e'\x01' delimiter e'\x02'
Enter data to be copied followed by a newline.
End with a backslash and a period on a line by itself, or an EOF signal.
>> {
    "glossary": {
        "title": "example glossary",
    "GlossDiv": {
            "title": "S",
      "GlossList": {
                "GlossEntry": {
                    "ID": "SGML",
          "SortAs": "SGML",
          "GlossTerm": "Standard Generalized Markup Language",
          "Acronym": "SGML",
          "Abbrev": "ISO 8879:1986",
          "GlossDef": {
                        "para": "A meta-markup language, used to create markup languages such as DocBook.",
            "GlossSeeAlso": ["GML", "XML"]
                    },
          "GlossSee": "markup"
                }
            }
        }
    }
}>> >> >> >> >> >> >> >> >> >> >> >> >> >> >> >> >> >> >> >> >>
>> \.
COPY 22
stan=> table demo;
                                                      t
─────────────────────────────────────────────────────────────────────────────────────────────────────────────
 {                                                                                                          ↵
     "glossary": {                                                                                          ↵
         "title": "example glossary",                                                                       ↵
                 "GlossDiv": {                                                                              ↵
             "title": "S",                                                                                  ↵
                         "GlossList": {                                                                     ↵
                 "GlossEntry": {                                                                            ↵
                     "ID": "SGML",                                                                          ↵
                                         "SortAs": "SGML",                                                  ↵
                                         "GlossTerm": "Standard Generalized Markup Language",               ↵
                                         "Acronym": "SGML",                                                 ↵
                                         "Abbrev": "ISO 8879:1986",                                         ↵
                                         "GlossDef": {                                                      ↵
                         "para": "A meta-markup language, used to create markup languages such as DocBook.",↵
                                                 "GlossSeeAlso": ["GML", "XML"]                             ↵
                     },                                                                                     ↵
                                         "GlossSee": "markup"                                               ↵
                 }                                                                                          ↵
             }                                                                                              ↵
         }                                                                                                  ↵
     }                                                                                                      ↵
 }
(1 row)
```

*It works, but it is more complicated than using a query in a view or procedure. No real gain here; I just wanted to see if was possible.*
