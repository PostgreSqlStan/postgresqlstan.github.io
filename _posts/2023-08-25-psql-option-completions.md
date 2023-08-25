---
# last_modified_at: 2023-08-24
title: "psql tip: set (or pset) options interactively"
category: PostgreSQL
tags:
  - psql
excerpt: "Tab completion provides builtin cheatsheets for psql settings."
header:
  overlay_image: /assets/headers/pg_bg_header.jpg
  overlay_filter: 0.85
  teaser: /assets/teasers/psql-teaser.jpg
classes: wide
---

Tab completion makes it easy to change psql settings interactively, removing the need to memorize options or spend time searching through documentation.

Realizing these builtin cheatsheets are always at my fingertips has improved my productivity in psql tremendously.

{% capture notice1 %}
:information_source: Enter `\? variables` in psql for help with psql variables.<br>
:bulb: Or enter `\? v<TAB>` and let tab completion finish the word.
{% endcapture %}<div class="notice--info">{{ notice1 | markdownify }}</div>

Most of the options that can be specified when launching psql can also be changed while it's running, using either the `\pset` or `\set` meta-commands.

<!-- Use `\pset` to change the value of variables that control display settings.  -->
To list display options, enter `\pset <TAB><TAB>`:

<figure class="align-center">
  <a href="/assets/ss/autocomplete/s3.jpg" title="tab completions for '\pset '" alt="psql tab completion example">
  <img src="/assets/ss/autocomplete/s3.jpg" alt=""></a>
  <figcaption>Screenshot: tab completions for \pset</figcaption>
</figure>

For display settings (but not other options), enter an option without a value to see its current setting.

<figure class="align-center">
  <a href="/assets/ss/autocomplete/s5.jpg" title="output for: \pset linestyle" alt="psql output example">
  <img src="/assets/ss/autocomplete/s5.jpg" alt=""></a>
  <figcaption>Screenshot: linestyle value shown by entering "\pset linestyle"</figcaption>
</figure>


Some settings also have completions for valid values:

<figure class="align-center">
  <a href="/assets/ss/autocomplete/s4.jpg" title="tab completions for '\pset linestyle '" alt="psql tab completion example">
  <img src="/assets/ss/autocomplete/s4.jpg" alt=""></a>
  <figcaption>Screenshot: tab completions for \pset linestyle</figcaption>
</figure>


Other psql options are stored in “specially treated” variables which can be changed with `\set`.

{% capture notice2 %}
By convention, psql variables in upper case are used by psql, not of all which can be changed. Depending on your psql configuration, completions might include lower case variables used for other purposes as well.
{% endcapture %}<div class="notice--info">{{ notice2 | markdownify }}</div>

Enter `\set <TAB><TAB>` to see completions for psql variables:

<figure class="align-center">
  <a href="/assets/ss/autocomplete/s1.jpg" title="tab completions for '\set '" alt="psql tab completion example">
  <img src="/assets/ss/autocomplete/s1.jpg" alt=""></a>
  <figcaption>Screenshot: tab completions for \set</figcaption>
</figure>

Some variables also have completions for valid settings:

<figure class="align-center">
  <a href="/assets/ss/autocomplete/s2.jpg" title="tab completions for '\set VERBOSITY '" alt="psql tab completion example">
  <img src="/assets/ss/autocomplete/s2.jpg" alt=""></a>
  <figcaption>Screenshot: tab completions for \set VERBOSITY</figcaption>
</figure>

Unlike `\pset`, if you don't provide a value for an option, it will be reset to the default. For example, to restore `AUTOCOMMIT` to its default value of `on`:

`\set AUTOCOMMIT`



:warning: Setting a `PROMPT` variable to nothing will not restore the default prompt.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Was experimenting with the prompt in psql and wondered if setting PROMPT1 to nothing would reset it to the default.<br><br>😂 <a href="https://t.co/6lepT3PxkX">pic.twitter.com/6lepT3PxkX</a></p>&mdash; PostgreSQL Stan (@PostgresqlStan) <a href="https://twitter.com/PostgresqlStan/status/1619853017599320065?ref_src=twsrc%5Etfw">January 30, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


{% capture notice3 %}
**Tab completion on Windows**: Tab completion is not available for psql on Windows but reportedly works for psql running on WSL (Windows Subsystem for Linux).
{% endcapture %}<div class="notice--info">{{ notice3 | markdownify }}</div>

<!-- - Tab completion for psql is provided by the Readline library on Linux and Editline library on BSD-based systems such as macOS. It is typically activated by pressing tab but can vary by configuration. -->


<!-- With help from tab completion, changing psql settings interactively removes the need to memorize psql options or spend time searching through documentation. -->


<!-- Tab completion removes the need to memorize psql options or spend time searching through documentation. -->

<!-- Most psql settings can be changed interactively with `\pset` (for display settings) or `\set` (for other options). -->

<!-- Display settings can be changed with `\pset` and other options with `\set`. -->

<!-- Display settings can be changed with `\pset` and other options with `\set`. Tab completion provides concise, builtin cheatsheets, removing the need to memorize options or spend time searching through the excellent but lengthy documentation. -->

<!-- ## Display Settings -->

<!-- ## Options -->
