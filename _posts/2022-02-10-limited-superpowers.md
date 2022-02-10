---
title: "PostgreSQL - Grant Yourself Limited Superpowers"
category: PostgreSQL
tags:
  - psql
classes: wide
excerpt: "Using **roles** in PostgreSQL, it's easy to grant a user superuser privileges temporarily, somewhat similar to using the sudo command on a Unix-compatible platform."
header:
  teaser: /assets/teasers/limited-superpowers.jpg
---

{% capture notice-0 %}

Requirements: PostgreSQL is installed on your computer and you're able to connect to it with psql.
{% endcapture %}<div class="notice--primary">{{ notice-0 | markdownify }}</div>

“Superuser status is dangerous and should be used only when really needed.” - [PostgreSQL Manual](#further-reading)

When learning or working with PostgreSQL on your own computer, it’s convenient to give your database user superuser privileges. With the default configuration, which only allows local connections, the danger is fairly limited.

Nonetheless, using superuser privileges only when needed is a good habit to develop. In addition to limiting the havoc you can wreak on your own databases, it’s a good way to learn what restrictions, or lack thereof, exist for “normal” users. You might be surprised.

Using **roles** in PostgreSQL, it's easy to grant a user superuser privileges temporarily, somewhat similar to using the sudo command on a Unix-compatible platform.

{% capture notice-1 %}
In PostgreSQL **roles are used as both users and groups**, which I only mention because the interchangeable terminology used by commands makes the topic unavoidable. This can be very confusing at first. Try not to worry about it.
{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

## Create a user

Run psql as the postgres superuser:

```
% psql -U postgres
psql (14.1)
Type "help" for help.

postgres=#
```

Use `\du` to list existing roles (users and groups). Upon creation, a new database cluster has a single superuser, conventionally named “postgres.”

```
postgres=# \du
                                   List of roles
 Role name │                         Attributes                         │ Member of
───────────┼────────────────────────────────────────────────────────────┼───────────
 postgres  │ Superuser, Create role, Create DB, Replication, Bypass RLS │ {}
```

{% capture notice-2 %}
**All of these commands can be entered in lower case.** If you're learning by entering these commands on your terminal, as I encourage you to do, save your time and use lower case. I'm using UPPER CASE commands only to highlight key words.
{% endcapture %}<div class="notice--primary">{{ notice-2 | markdownify }}</div>


Use the `CREATE USER` command to create a *role* with the LOGIN option:

```
postgres=# CREATE USER stan;
CREATE ROLE
```
{% capture notice-3 %}
"CREATE USER is the same as CREATE ROLE except that it implies LOGIN." - [PostgreSQL Manual](#further-reading)
{% endcapture %}<div class="notice">{{ notice-3 | markdownify }}</div>

## Create a superuser role

Next, create a role with superuser privileges but no login privileges:

```
postgres=# CREATE ROLE dba SUPERUSER;
CREATE ROLE
```

## Grant superpowers to your user

Use the appropriately named GRANT command to allow a user to use the privileges of another role:

```
postgres=# GRANT dba TO stan;
GRANT ROLE
```

List roles to review the results:

```
postgres=# \du
                                   List of roles
 Role name │                         Attributes                         │ Member of
───────────┼────────────────────────────────────────────────────────────┼───────────
 dba       │ Superuser, Cannot login                                    │ {}
 postgres  │ Superuser, Create role, Create DB, Replication, Bypass RLS │ {}
 stan      │                                                            │ {dba}
```

## Invoke your superpowers with SET ROLE

Re-connect to the same database (named “postgres”) as your normal user to test your standard and superuser privileges:

```
\c postgres stan
You are now connected to database "postgres" as user "stan".
```

{% capture notice-4 %}
You might have noticed your prompt has changed. With default settings, the last character of my prompt is “#” when I have superuser privileges and “>” when I don’t.
{% endcapture %}<div class="notice">{{ notice-4 | markdownify }}</div>

Try to create a database:

```
postgres=> CREATE DATABASE su_test;
ERROR:  permission denied to create database
```

Invoke your superpowers and try again:
```
postgres=> SET ROLE dba;
SET
postgres=# CREATE DATABASE su_test;
CREATE DATABASE
```

Relinquish your superpowers:
```
postgres=# RESET ROLE;
RESET
postgres=> DROP DATABASE su_test;
DROP DATABASE
```

(Because I created the su_test database, I’m allowed to drop it without using superuser privileges. This is something I "knew" and had forgotten before I wrote this tutorial. Using a non-superuser account helpfully reminded me.)

Trying to create the database again verifies my superpowers have been relinquished:

```
postgres=> CREATE DATABASE su_test;
ERROR:  permission denied to create database
```

!["No power! [meme]"](/assets/images/no-power.jpg)

I've limited my power. **Success!**

## Further reading

PostgreSQL Documentation:

* [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html)
* [ALTER ROLE](https://www.postgresql.org/docs/current/sql-alterrole.html)
* [SET ROLE, RESET ROLE](https://www.postgresql.org/docs/current/sql-set-role.html)
* [GRANT](https://www.postgresql.org/docs/current/sql-grant.html)
* [REVOKE](https://www.postgresql.org/docs/current/sql-revoke.html)


## Notes

{% capture notice-5 %}

**Revoking superuser privileges**: To apply this limited superpowers method to an existing user (role), you can revoke superuser privileges with the `ALTER ROLE` command:
```
postgres=# ALTER ROLE stan NOSUPERUSER;
ALTER ROLE
```
---
**Superuser is not "inherited"**: Unlike most other privileges, superuser privileges are not automatically inherited, which is why `SET ROLE` is  needed to invoke them. (I read this somewhere in the docs but now can't find the specific reference.)
{% endcapture %}<div class="notice--info">{{ notice-5 | markdownify }}</div>
