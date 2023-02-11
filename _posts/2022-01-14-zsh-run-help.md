---
last_modified_at: "2023-02-11"
title: "zsh Tip: run-help is extremely helpful"
category: CLI
tags:
  - macOS
  - zsh
excerpt: "Configure zsh to use the run-help function, making it easer to access documentation."
classes: wide
header:
  teaser: /assets/teasers/zsh-run-help.jpg
---

So far, the most useful thing I've learned about zsh is how to enable the `run-help` function, an essential tool for learning more.

## Enabling run-help

Properly configured, the `run-help` function offers additional help files and easier access to documentation that lacks its own dedicated man page, like builtin shell commands.

To enable it, simply add these lines to your \~/.zshrc startup file:

```zsh
unalias run-help 2>/dev/null       # don't display err if already done
autoload -Uz run-help              # load the function
alias help=run-help                # optionally alias run-help to help
```

{% capture notice-1 %}
You can test these changes by entering them in the terminal. Any changes will be reverted when the window is closed.
{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

An additional `help` directory also contains information for several commands whose documentation is located deep in zsh manual pages. For example, when configured to use the `help` directory `run-help` shows this information for the `history` command:

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

(To see how much easier this is, try locating the same information with `man history`. *Go ahead. I'll wait.*)

To enable the additional help, locate the `help` directory, typically in a version subdirectory of `/usr/share/zsh/` or `/usr/local/share/zsh`, and set the `HELPDIR` parameter to its location.

```zsh
HELPDIR=/usr/share/zsh/5.8.1/help    # macos 13
```

Once `HELPDIR` is set correctly, `run-help -l` will list additional help topics instead of this message:

```
% run-help -l
There is no list of special help topics available at this time.
```
---

*But, wait, there's more.*

### Run run-help with option-h

Enter a command (without pressing enter) and press ESC-h to run the help function for it. (Option-h if your terminal is configured to use option as meta key.)

Use `bindkey` to verify the bindings:

```zsh
%  bindkey | grep run-help
"^[H" run-help
"^[h" run-help
```

## Credit

Ironically, the documentation for `run-help` is too deeply hidden in the zshcontrib man page to be found by `run-help`.

I might have found it eventually, but probably not. Credit goes to helpful contributors at
[Stack Exchange](https://stackoverflow.com/questions/4405382/how-can-i-read-documentation-about-built-in-zsh-commands).


## Appendum

I discovered additional help modules mentioned in the zshcontrib man pages which can be enabled by autoloading them:

```zsh
autoload run−help−git
autoload run−help−ip
autoload run−help−openssl
autoload run−help−p4
autoload run−help−sudo
autoload run−help−svk
autoload run−help−svn
```
