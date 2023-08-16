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
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.7
  teaser: /assets/teasers/zsh-run-help.jpg
---

Properly configured, the zsh `run-help` function offers additional help files and much easier access to documentation than the man command. So far, it's most useful thing I've learned about zsh.

To enable it, simply add these lines to your \~/.zshrc startup file:

```zsh
unalias run-help 2>/dev/null       # don't display err if already done
autoload -Uz run-help              # load the function
alias help=run-help                # optionally alias run-help to help
```

You'll also want to set the shell parameter HELPDIR to the location of the `help` directory, typically in a subdirectory of `/usr/share/zsh/` or `/usr/local/share/zsh`.

```zsh
HELPDIR=/usr/share/zsh/5.8.1/help    # macos 13
```

{% capture notice-1 %}
You can test these changes by entering them in the terminal. Any changes will be reverted when the window is closed.
{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

If configured correctly, `run-help` should show this information for the `history` command:

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
