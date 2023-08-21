---
# last_modified_at: 2023-01-13
title: "psql: reverse history search on macOS"
category: PostgreSQL
excerpt: ""
header:
  overlay_image: /assets/headers/pg_bg_header.jpg
  overlay_filter: 0.85
  teaser: /assets/teasers/cat-laptop.jpg
classes: wide
---

I share this discovery in the hopes it might save others from the years of unnecessary suffering I endured.

Unless it is compiled with the GNU Readline library, the key binding for reverse history search is not enabled by default on macOS. The solution is to create a configuration file for the editline library used by macOS.

Create `~/.editrc` with the following:

```zsh
bind "^R" em-inc-search-prev
```

*Suffering ends.*
