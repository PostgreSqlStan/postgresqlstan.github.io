---
title: Start the PostgreSQL server automatically on macOS
date: 2022-01-29
sidebar:
  nav: guides
classes: wide
---

The [contrib/start-scripts/macos](https://github.com/postgres/postgres/tree/master/contrib/start-scripts/macos) directory of the PostgreSQL source code contains files and instructions to automatically launch the postgres server at system start on macOS.

The supplied shell script and plist already match the default settings for a standard installation.

If you've installed postgres following the directions on this site, just copy the script and .plist file  in `contrib/start-scripts/macos` to the appropriate system locations:

```
% cd ~/sandbox/postgresql-14.1/contrib/start-scripts/macos
% sudo cp postgres-wrapper.sh /usr/local/pgsql/bin
% sudo cp org.postgresql.postgres.plist /Library/LaunchDaemons/
```

To test without restarting your computer, load the plist with `launchctl`:

```
% sudo launchctl load /Library/LaunchDaemons/org.postgresql.postgres.plist
```

Use the `unload` option to stop the server:

```
% sudo launchctl unload /Library/LaunchDaemons/org.postgresql.postgres.plist
```

{% capture notice-1 %}
Due to the `KeepAlive` setting in the plist file, the PostgreSQL server will restart automatically if you stop it using normal methods. _This was a very funny thing to learn the hard way!_
{% endcapture %}

<div class="notice">{{ notice-1 | markdownify }}</div>
