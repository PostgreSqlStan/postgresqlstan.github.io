---
title: "zsh Shell History - Options"
last_modified_at: "2022-01-19 21:47"
category: CLI
tags:
  - zsh
excerpt: "I read the docs so you don't have to."
classes: wide
header:
  teaser: /assets/teasers/zsh-history-options.jpg
---

*I read the docs so you don't have to.*

I decided to review **all** the zsh history options that effect how history is saved. Doing so, I discovered some of the my settings (copied from an otherwise informative article) should not have been enabled at the same time.

Read along to learn how I reviewed all the settings or jump to the end to see the list of options and the relevant section of my `~/.zshrc` config file.

---

My environment:

```shell
% uname -a ; zsh --version
Darwin air.local 21.2.0 Darwin Kernel Version 21.2.0: Sun Nov 28 20:29:10 PST 2021; root:xnu-8019.61.5~1/RELEASE_ARM64_T8101 arm64
zsh 5.8 (x86_64-apple-darwin21.0)
```

First, I want to bypass my .zshrc file to see what the defaults are. I do that by starting zsh with `ZDOTDIR` set to an empty directory:

```shell
% mkdir ~/testconfig
% ZDOTDIR=~/testconfig zsh
stan@air ~ %
```

The `setopt` command without arguments lists the enabled shell options:

```shell
stan@air% setopt
combiningchars
interactive
monitor
shinstdin
zle
```

It's a short list that doesn't include any history options.

{% capture notice-1 %}
Options names are case insensitive and underscores are ignored. For example, `allexport` is equivalent to `A__lleXP_ort`.

The sense of an option name may be inverted by preceding it with "no", so `setopt No_Beep` is equivalent to `unsetopt beep`. This inversion can only be done once, so `nonobeep` is not a synonym for `beep`.
{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

The `unsetopt` command without arguments lists all of the unset options. Piping that to the grep command, I can search for lines containing "hist":

```shell
stan@air% unsetopt | grep hist
noappendhistory
nobanghist
cshjunkiehistory
extendedhistory
histallowclobber
nohistbeep
histexpiredupsfirst
histfcntllock
histfindnodups
histignorealldups
histignoredups
histignorespace
histlexwords
histnofunctions
histnostore
histreduceblanks
nohistsavebycopy
histsavenodups
histsubstpattern
histverify
incappendhistory
incappendhistorytime
sharehistory
```

Searching through docs, I located and summarized descriptions of the corresponding history options:

| option                   | default | description                                        |
|--------------------------|--------------------------------------------------------------|
| `APPEND_HISTORY`         | +       | append to the history file rather than replace it  |
| `BANG_HIST`              | +       | enable "!" history expansion                       |
| `CSH_JUNKIE_HISTORY`     | -       | csh behavior option                                |
| `EXTENDED_HISTORY`       | -       | include timestamp                                  |
| `HIST_ALLOW_CLOBBER`     | -       | related to shell clobber setting                   |
| `HIST_BEEP`              | +       | beep if attempting to access a history entry which isn’t there |
| `HIST_EXPIRE_DUPS_FIRST` | -       | trim dupes first when history is full. *You should be sure to set the value of HISTSIZE to a larger number than SAVEHIST* |              |
| `HIST_FCNTL_LOCK     `   | -       | may provide better performance, in particular avoiding history corruption when files are stored on NFS. |
| `HIST_FIND_NO_DUPS   `   | -       | do not display previously found command            |
| `HIST_IGNORE_ALL_DUPS`   | -       | remove old command if new one is a duplicate       |
| `HIST_IGNORE_DUPS`       | -       | do not save duplicate of prior command             |
| `HIST_IGNORE_SPACE`      | -       | do not save if line starts with space              |
| `HIST_LEX_WORDS`         | -       | related to how white space is saved                |
| `HIST_NO_FUNCTIONS`      | -       | do not save function commands                      |
| `HIST_NO_STORE`          | -       | do not save history commands                       |
| `HIST_REDUCE_BLANKS`     | -       | strip superfluous blanks                           |
| `HIST_SAVE_NO_DUPS `     | -       | omit older duplicates of newer commands            |
| `HIST_SUBST_PATTERN`     | -       | use pattern matching for substitutions with history modifiers |
| `HIST_VERIFY`            | -       | expand line without executing it                   |
| `INC_APPEND_HISTORY`     | -       | don’t wait for shell to exit to save history lines |
| `INC_APPEND_HISTORY_TIME`| -       | *only useful if INC_APPEND_HISTORY and SHARE_HISTORY are turned off* |
| `SHARE_HISTORY`          | -       | *INC_APPEND_HISTORY should be turned off if this option is in effect* |

(Note the warnings in italics in the above table.)

There are three other important settings that are not enabled by the `setopt` command: `HISTFILE`, `SAVEHIST` and `HISTSIZE`.

`HISTFILE` gets set (in /etc/zshrc) to `~/.zsh_history`, which I'll leave as is. I've increased `SAVEHIST` from the outdated setting of 1000 or so, but not by a huge amount, and set `HISTSIZE` to a larger size.

Eliminating the settings I never want to change and commenting out options I might want to change at some point, but not now, the history options in my `.zshrc` file ended up like this.

```shell
SAVEHIST=9000
HISTSIZE=9999                   # set HISTSIZE > SAVEHIST

setopt EXTENDED_HISTORY         # include timestamp
setopt HIST_BEEP                # beep if attempting to access a history entry which isn’t there
setopt HIST_EXPIRE_DUPS_FIRST   # trim dupes first if history is full
setopt HIST_FIND_NO_DUPS        # do not display previously found command
setopt HIST_IGNORE_DUPS         # do not save duplicate of prior command
setopt HIST_IGNORE_SPACE        # do not save if line starts with space
setopt HIST_NO_STORE            # do not save history commands
setopt HIST_REDUCE_BLANKS       # strip superfluous blanks
setopt INC_APPEND_HISTORY       # don’t wait for shell to exit to save history lines

# setopt HIST_ALLOW_CLOBBER       # related to shell clobber setting
# setopt HIST_IGNORE_ALL_DUPS     # remove old event if new one is a duplicate
# setopt HIST_LEX_WORDS           # related to how white space is saved
# setopt HIST_NO_FUNCTIONS        # do not save function commands
# setopt HIST_SAVE_NO_DUPS        # omit older duplicates of newer commands
# setopt HIST_SUBST_PATTERN       # use pattern matching for substitutions
# setopt HIST_VERIFY              # expand command line without executing it
```


