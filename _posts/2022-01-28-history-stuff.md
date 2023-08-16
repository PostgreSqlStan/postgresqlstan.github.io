---
classes: wide
title: "Random shell (zsh) history stuff"
category: macOS
tags:
  - CLI
  - zsh
header:
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.7
  teaser: /assets/teasers/history-notes.jpg
---

Some things about shell (zsh) history I don't want to forget.

## Showing history

Use a negative argument to specify how far back to list. For example, to list the last 25 commands:

```shell
% history -25
```

The -i option shows timestamps (required EXTENDED_HISTORY to be enabled to save timestamps):

```shell
% history -i
```

To update a shell's history from sessions in other windows:

```shell
% fc -RI
```

History aliases:

```shell
alias h=' fc -li' # history w/timestamp
alias rh=' fc -RI' # refresh history
```

## Substitutions

Use option-. to insert last word from prior command:

```shell
% mkdir newdir
% cd # press option-. to insert "newdir" from prior command
```

I don't use it as often with the corrections enabled in zsh, but the ability to replace strings in the previous command is still useful at times. With CORRECT and CORRECT_ALL not enabled:

```shell
% mkdir newdir
% cd newdr
cd: no such file or directory: testdr
% ^dr^dir
cd testdir
```



