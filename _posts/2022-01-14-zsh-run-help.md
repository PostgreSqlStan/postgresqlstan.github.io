---
title: "zsh Tip: run-help is extremely helpful"
last_modified_at: "2023-02-02"
category: CLI
tags:
  - macOS
  - zsh
excerpt: "Configure zsh to use the run-help function, making it easer to access documentation."
classes: wide
header:
  teaser: /assets/teasers/zsh-run-help.jpg
---

So far, the most useful thing I've learned about zsh is how to enable the `run-help` function, which makes it much easier to learn even more.

## Enabling run-help

Properly configured, the `run-help` function offers additional help files and easier access to documentation that lacks its own dedicated man page, like builtin shell commands.

Unfortunately, the `run-help` function is not enabled by default on macOS. To enable it, simply add these lines to your \~/.zshrc startup file:

```
HELPDIR=/usr/share/zsh/5.8/help    # help files dir on macOS 12.1
unalias run-help 2>/dev/null       # don't display err if already done
autoload run-help
alias help=run-help                # optionally alias to help
```

**Update:** At some point Apple has changed something about the default zsh configuration resulting in the appropriate help directory being included in the environmental variables `fpath`:

```
% echo $fpath
/Users/stan/functions /usr/local/share/zsh/site-functions /usr/share/zsh/site-functions /usr/share/zsh/5.8.1/functions
```

I'm not sure which update make this change, which makes it unecessary (but harmless) to set `HELPDIR` to a correct value.

{% capture notice-1 %}

You can test these commands by entering them in the terminal. Any changes will be reverted when the window is closed.

Some features of `run-help` are dependent on files in the directory indicated by the shell variable HELPDIR. The directory location will vary by installation.
{% endcapture %}<div class="notice--warning">{{ notice-1 | markdownify }}</div>

To learn more about the `history` command, for example, you can now jump straight to the relevant information:

```shell
 % help history
fc [ -e ename ] [ -LI ] [ -m match ] [ old=new ... ] [ first [ last ] ]
fc -l [ -LI ] [ -nrdfEiD ] [ -t timefmt ] [ -m match ]
      [ old=new ... ] [ first [ last ] ]
fc -p [ -a ] [ filename [ histsize [ savehistsize ] ] ]
fc -P
fc -ARWI [ filename ]
       The fc command controls the interactive history mechanism.  Note…
```

To see how much easier this is, try locating the same information with `man history`. (Go ahead. I'll wait.)

*But, wait, there's more.*

### Invoke run-help with ESC-h

Verify bindings for the `run-help` function:

```shell
%  bindkey | grep run-help
"^[H" run-help
"^[h" run-help
```

The `run-help` function can also be invoked by pressing "ESC" then "h" after typing any command (without pressing return).

## Credit

Ironically, the documentation for `run-help` is too deeply hidden in the zshcontrib man page to be found by `run-help`.

I might have found it eventually, but probably not. Credit goes to helpful contributors at
[Stack Exchange](https://stackoverflow.com/questions/4405382/how-can-i-read-documentation-about-built-in-zsh-commands).

## Appendum

I discovered additional help modules mentioned in the zshcontrib man pages which can be enabled by autoloading them:

```shell
autoload run−help−git
autoload run−help−ip
autoload run−help−openssl
autoload run−help−p4
autoload run−help−sudo
autoload run−help−svk
autoload run−help−svn
```




