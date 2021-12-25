[Install Postgres from Source on MacOS/](./)
# Start the server and configure your environment

## Create the data directory

Create a Postgres data directory and change its user and group ownership to postgres:

```
sudo mkdir /usr/local/pgsql/data
sudo chown postgres:postgres /usr/local/pgsql/data
```

## Initialize the data directory

Run `initdb` as the postgres user to initialize the data directory:

```
sudo -u postgres /usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
```

## Start the server

Run `pg_ctl` as the postgres user to start the server, specifying the location of the data directory and the log. (Backslashes "\\" are used to continue a command to the next line.)

```
sudo -u postgres /usr/local/pgsql/bin/pg_ctl \
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
sudo -u postgres /usr/local/pgsql/bin/pg_ctl \
     -D /usr/local/pgsql/data stop 
```

## Configure Your Shell

In order to launch programs like `psql`  without typing the full path (`/usr/local/pgsql/bin/psql`), you need to add Postgres `bin` directory to your shell’s environment variable.

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

## Connect to the server (and set a password if needed)

The default settings allow the postgres user to connect to a server running on the same computer without a password.

```
psql -U postgres
```

You should be automatically connected to a database (also named postgres) and see a something like this:
```
psql (14.1)
Type "help" for help.

postgres=# 
```

My local installation of Postgres is used for learning and experimenting and does not contain any private or sensitive information, so I leave the password blank for convenience.

Use `ALTER USER` command to set the password if needed:

```
postgres=# ALTER USER postgres PASSWORD 'myPassword';
ALTER ROLE
```
---
Next: [Start the Server Automatically](launchctl.md)
