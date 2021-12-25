# Start the PostgreSQL server automatically on macOS
_Tested on macOS 11 (Big Sur) and 12 (Monterey)_

The [contrib/start-scripts/macos](https://github.com/postgres/postgres/tree/master/contrib/start-scripts/macos) directory of the source code contains files and instructions to automatically launch the PostgreSQL server at system start on macOS.

The supplied shell script and plist already match the default settings for a standard installation and don’t need to be edited. Just copy the files to the appropriate locations.

```
cd ~/sandbox/postgresql-14.1/contrib/start-scripts/macos
sudo cp postgres-wrapper.sh /usr/local/pgsql/bin
sudo cp org.postgresql.postgres.plist /Library/LaunchDaemons/
```


To test without restarting the system, load the plist with `launchctl`:
```
sudo launchctl load /Library/LaunchDaemons/org.postgresql.postgres.plist
```

Due to the `KeepAlive` setting in the plist file, the PostgreSQL server will restart automatically if you stop it using normal methods. _This was a very funny thing to learn the "hard" way._

Use the `unload` option to stop the server with `launchctl`:

```
sudo launchctl unload /Library/LaunchDaemons/org.postgresql.postgres.plist
```
