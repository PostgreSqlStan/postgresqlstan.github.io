---
last_modified_at: 2021-02-11
title: "PostgreSQL - Grant Yourself Limited Superpowers"
category: PostgreSQL
tags:
  - psql
classes: wide
excerpt: "Using roles in PostgreSQL, it's easy to grant a user superuser privileges that can be invoked when needed, somewhat similar to using the sudo command on a Unix-compatible platform."
header:
  teaser: /assets/teasers/limited-superpowers.jpg
---

{% capture notice-0 %}

Requirements: PostgreSQL is installed on your computer and you're able to connect to it with psql.
{% endcapture %}<div class="notice--primary">{{ notice-0 | markdownify }}</div>

> [S]uperusers can access all objects regardless of object privilege settings. This is comparable to the rights of root in a Unix system. As with root, it's unwise to operate as a superuser except when absolutely necessary. - [PostgreSQL Manual](#further-reading)

When learning or working with PostgreSQL on your own computer, it’s convenient to give your database user superuser privileges. With the default configuration, which doesn't allow remote connections, the danger is fairly limited.

Nonetheless, using superuser privileges only when needed is a good habit to develop. In addition to limiting the havoc you can wreak on your own databases, it’s a good way to learn what restrictions, or lack thereof, exist for “normal” users. You might be surprised.

Using **roles** in PostgreSQL, it's easy to grant a user superuser privileges that can be invoked when needed, somewhat similar to using the sudo command on a Unix-compatible platform.

{% capture notice-1 %}
In PostgreSQL **roles are used as both users and groups**, which I only mention because the interchangeable terminology used by commands makes the topic unavoidable. This can be very confusing at first.

Try not to worry about it. In practice, as I hope to demonstrate, the concept is fairly simple.
{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

## Create a user

Connect to the database as the postgres superuser:

```
% psql -U postgres
psql (14.1)
Type "help" for help.

postgres=#
```

Upon creation, a new database cluster has a single superuser, conventionally named “postgres.” Use `\du` to list existing roles (users and groups):

```
postgres=# \du
                                   List of roles
 Role name │                         Attributes                         │ Member of
───────────┼────────────────────────────────────────────────────────────┼───────────
 postgres  │ Superuser, Create role, Create DB, Replication, Bypass RLS │ {}
```

{% capture notice-2 %}
**All of these commands can be entered in lower case.** I'm using UPPER CASE commands here only to highlight key words. You can give your shift key a rest.
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

Use the GRANT command to give a user membership in a role:

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

### Revoking privileges or membership

To apply this method to an existing user (role) that already has superuser privileges, you can revoke them with the `ALTER ROLE` command:
```
postgres=# ALTER ROLE stan NOSUPERUSER;
ALTER ROLE
```

Use the `REVOKE` command to remove a user from a role:

```
postgres=# REVOKE dba FROM stan;
REVOKE ROLE
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

{% capture notice-5 %}
Note: The SUPERUSER attribute (as well as LOGIN, CREATEDB, and CREATEROLE) can not be inherited through membership and must be explicitly invoked with the `SET ROLE` command.
{% endcapture %}<div class="notice">{{ notice-5 | markdownify }}</div>

Relinquish your superpowers:
```
postgres=# RESET ROLE;
RESET
postgres=> DROP DATABASE su_test;
DROP DATABASE
```

(Because I created the su_test database, I’m allowed to drop it without using superuser privileges. This is something I "knew" and had forgotten before I wrote this tutorial. Using a non-superuser account helpfully reminded me.)

Again, try to create a database to verify your superpower have been relinquished:

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
* [ROLE MEMBERSHIP](https://www.postgresql.org/docs/current/role-membership.html)

