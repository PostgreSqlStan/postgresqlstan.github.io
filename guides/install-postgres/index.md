---
title: Install PostgreSQL from source on macOS
description: "Step by step directions for compiling and installing PostgreSQL from source on macOS."
permalink: /guides/install-postgres/
date: 2021-12-29
last_modified_at: 2021-12-29
sidebar:
  nav: guides
author_profile: false
excerpt: "Step by step directions for compiling and installing PostgreSQL from source on macOS."
header:
  overlay_color: "#333"
---

The sensible way to install Postgres on a Mac is to use one of the recommended [macOS package installers](https://www.postgresql.org/download/macosx).
Building from source is “only recommended for people developing PostgreSQL or extensions.” 

But if you’re comfortable using a terminal and truly determined (or required), the directions for [installing PostgreSQL from source](https://www.postgresql.org/docs/current/installation.html) on Unix-compatible platforms are relatively simple and only require a few modifications to work on macOS.

{% capture notice-1 %}
Tested on macOS Big Sur (11) and Monterey (12).
{% endcapture %}

<div class="notice">{{ notice-1 | markdownify }}</div>

1. [Download and install postgres](./install.md)
2. [Create postgres user](/create-postgres-user.md) (on macOS)
3. [Start the server and configure your environment](/post-install.md) 
4. [Start the server automatically](/launchctl.md) (on macOS)

