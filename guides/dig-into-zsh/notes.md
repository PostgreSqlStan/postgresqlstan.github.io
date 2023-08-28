---
# classes: wide
# date: 2023-08-16
title: "Dig into zsh - Learn to Function"
category: zsh
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3 # same as adding an opacity of 0.5 to a black background
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
sidebar:
  nav: dig-into-zsh
# toc: true
# toc_sticky: true
---
<style type="text/css">
kbd {
  background-color: #444;
  border-top: 3px solid #aaa;
  border-left: 3px solid #999;
  border-right: 3px solid #333;
  border-bottom: 4px solid #222;
  border-radius: 4px;
  color: whitesmoke;
  display: inline-block;
  font-size: .8em;
  font-family: "Trebuchet MS", Helvetica, sans-serif;
  line-height: 100%;
  margin: 0 .1em;
  padding: .2em 0.5em;
  text-shadow:
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000;
}</style>


## Notes

### Preview substitutions

Pressing <kbd>tab</kbd> before pressing <kbd>enter</kbd>, which expands wildcards and substitutions on the line, is a good way to prevent mistakes.

To edit a command afterwards, undo expansions it with (<kbd>control</kbd><kbd>_</kbd>).

For example, type: `print -- *`<kbd>tab</kbd> (without pressing <kbd>enter</kbd>).

After pressing <kbd>tab</kbd>, the wildcard `*` is replaced with the names of matching directories and files. Pressing <kbd>control</kbd><kbd>_</kbd> restores the command line to its previous state.



