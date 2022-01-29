---
title: Post-installation
date: 2022-01-29
sidebar:
  nav: guides
classes: wide
---

After PostgreSQL is installed and the postgres user has been created, there are a few steps left before you can connect to the database server.

## Create the data directory

Create a Postgres data directory and change its user and group ownership to postgres:

```
% sudo mkdir /usr/local/pgsql/data
% sudo chown postgres:postgres /usr/local/pgsql/data
```

## Initialize the data directory

Run `initdb` as the postgres user to initialize the data directory:

```
% sudo -u postgres /usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
```

## Start the server

Run `pg_ctl` as the postgres user to start the server, specifying the location of the data directory and the log. (Backslashes "\\" are used to continue a command to the next line.)

```
% sudo -u postgres /usr/local/pgsql/bin/pg_ctl \
     -D /usr/local/pgsql/data \
     -l /usr/local/pgsql/data/logfile start 
```

Hopefully, you’ll see this message:

```
waiting for server to start.... done
server started
```

To stop the server:
```
% sudo -u postgres /usr/local/pgsql/bin/pg_ctl \
     -D /usr/local/pgsql/data stop 
```

## Configure Your Shell

In order to launch programs like `psql`  without typing the full path (`/usr/local/pgsql/bin/psql`), you need to add the PostgreSQL `bin` directory to your shell’s environment variable.

Add these lines to your shell’s startup file:

```
PATH="/usr/local/pgsql/bin:$PATH"
export PATH
```

If you installed the documentation, also add these lines so you can view them with the `man` command:

```
MANPATH=/usr/local/pgsql/share/man:$MANPATH
export MANPATH
```

## Connect to the server

The default settings allow the postgres superuser to connect to a server running on the same computer without a password.

Connect to the server as the postgres user:

```
% psql -U postgres
```

If everything worked, you should be connect to a database (also named "postgres") and see something like this:

```sql
psql (14.1)
Type "help" for help.

postgres=# 
```

My local installation of PostgreSQL is used for learning, development and testing. It does not contain any private or sensitive information, so I leave the password blank for convenience.

If you need to set a password, use the `ALTER USER` command:

```sql
postgres=# ALTER USER postgres PASSWORD 'myPassword';
ALTER ROLE
```
---
[Next: Start the Server Automatically](launchctl.md){: .btn .btn--info}
