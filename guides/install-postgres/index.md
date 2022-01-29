---
title: Install PostgreSQL from source on macOS
description: "Step by step directions for compiling and installing PostgreSQL from source on macOS."
permalink: /guides/install-postgres/
date: 2022-01-29
sidebar:
  nav: guides
excerpt: "Step by step directions for compiling and installing PostgreSQL from source on macOS."
---

{% capture notice-1 %}

**Skills Required:** Intermediate terminal knowledge

Along with basic commands you should also already know what shell you're using, how to configure it, how to use auto-completion while typing commands, and have a decent understanding of file permissions.
{% endcapture %}<div class="notice--info">{{ notice-1 | markdownify }}</div>

The sensible way to install Postgres on a Mac is to use one of the recommended [macOS package installers](https://www.postgresql.org/download/macosx).
Building from source is “only recommended for people developing PostgreSQL or extensions.” 

But if you’re comfortable using a terminal and truly determined (or required), the directions for [installing PostgreSQL from source](https://www.postgresql.org/docs/current/installation.html) on Unix-compatible platforms are relatively simple and only require a few modifications (steps #2 and #4) to work on macOS.



1. [Download and install postgres](./install.md)
2. [Create postgres user](/create-postgres-user.md) (on macOS)
3. [Start the server and configure your environment](/post-install.md) 
4. [Start the server automatically](/launchctl.md) (on macOS)

{% capture notice-2 %}
Tested with PostgreSQL 14 on macOS Big Sur (11) and Monterey (12).
{% endcapture %}<div class="notice">{{ notice-2 | markdownify }}</div>
