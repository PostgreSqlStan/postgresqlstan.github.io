---
title: "Install PostgreSQL from source on macOS"
permalink: /guides/install-postgres/

last_modified_at: Dec 25 2021
---

The sensible way to install Postgres on a Mac is to use one of the recommended [macOS package installers](https://www.postgresql.org/download/macosx).
Building from source is “only recommended for people developing PostgreSQL or extensions.” 

But if you’re comfortable using a terminal and truly determined (or required), the directions for [installing PostgreSQL from source](https://www.postgresql.org/docs/current/installation.html) on Unix-compatible platforms are relatively simple and only require a few modifications to work on macOS.

1. [Download and install postgres](install.md)
2. [Create postgres user](create-postgres-user.md) (on macOS)
3. [Start the server and configure your environment](post-install.md) 
4. [Start the server automatically](launchctl.md) (on macOS)

