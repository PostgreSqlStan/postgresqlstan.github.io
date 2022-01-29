---
title: "Build and install Postgres on macOS"
date: 2022-01-29
sidebar:
  nav: guides
classes: wide
---

{% capture notice-1 %}**Prerequisite: XCode Command Line Tools**

Entering `gcc -v` in the terminal will begin dialog to download the Xcode Command Line Tools if they're not already installed.

{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

Once the Command Line Tools are installed, the directions for compiling and installing PostgreSQL are the same on macOS as other Unix-compatible platforms.


## Download the source code

Locate the appropriate version (v14.1 in this example) at
[https://www.postgresql.org/ftp/source/](https://www.postgresql.org/ftp/source/) and copy the link to the .bz2 archive.

In the terminal, change to a working directory (`~/sandbox` in this example), download the archive, and extract the archive:

```
% cd ~/sandbox
% curl -O https://ftp.postgresql.org/pub/source/v14.1/postgresql-14.1.tar.bz2
% tar -xf postgresql-14.1.tar.bz2
```

## Build and Install

Move to the unarchived directory. I usually read any README or INSTALL files, but I've already read them for you, so you can skip that step.

```
% cd postgresql-14.1/
% less README INSTALL
```

Following the directions for Unix-compatible platforms, first run `./configure`, which will display many messages and warnings:

```
% ./configure
```

Then run `make`, which compiles the source files into binaries:

```
% make
```

Run the last command as superuser to install postgres at the default location (`/usr/local/pgsql`):

```
% sudo make install
```

If you want to install the man pages and HTML docs, run this command as well:

```
% sudo make install-docs
```

---
[Next: Create a postgres user](create-postgres-user.md){: .btn .btn--info}
