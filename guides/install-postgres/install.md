---
title: "Build and install Postgres on macOS"
date: 2021-12-29
last_modified_at: 2021-12-29
sidebar:
  nav: guides
author_profile: false
---

{% capture notice-1 %}**Prerequisite: XCode Command Line Tools**

Type `gcc -v` in the terminal and a dialog to download the Xcode Command Line Tools will appear if they're not already installed.

{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

## Download the source code

Using a web browser, navigate to the desired version directory (v14.1 in this example) at https://www.postgresql.org/ftp/source/ and copy the link to the bz2 archive.

In the terminal, change to a working directory, download the archive, and extract the archive:

```
cd ~/sandbox
curl -O https://ftp.postgresql.org/pub/source/v14.1/postgresql-14.1.tar.bz2
tar -xf postgresql-14.1.tar.bz2
```

## Build and Install

Move to the unarchived postgres directory. I usually read any README or INSTALL files, but I've already read them for you, so you can skip that step.

```
cd postgresql-14.1/
# less README INSTALL
```

Following the directions for Unix-compatible platforms using default settings and standard conventions, run `./configure`, which will display many messages you can (usually) ignore.

```
./configure
```

Then run `make`, which compiles the source files into binaries, displaying many more messages you can (usually) ignore. This step might take a while.

```
make
```

Run the last command as superuser to install postgres at the default location (`/usr/local/pgsql`).

```
sudo make install
```

If you want to install the man pages and HTML docs, run this command as well:

```
sudo make install-docs
```

---
[Next: Create a postgres user](create-postgres-user.md){: .btn .btn--info}
