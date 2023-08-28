---
# classes: wide
date: 2023-08-16
title: "Dig into zsh - Introduction"
category: zsh
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3 # same as adding an opacity of 0.5 to a black background
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
toc: true
toc_sticky: true
sidebar:
  nav: dig-into-zsh
---

***Dig into zsh*** is a hands-on exploration of the zsh shell, based on my own ~~struggles~~ learning adventures.

<!-- The focus here is on the learning process more than solutions. If you're seeking quick answers, you should probably look elsewhere. -->


{% capture requirements %}**Requirements**: a terminal running zsh and a fair amount of patience<br>
*Some prior shell experience would probably be helpful.*<br>
*I try to explain things simply, but this isn't really a beginner's guide.*
{% endcapture %}<div class="notice">{{ requirements | markdownify }}</div>


## Fear the shell

If you use a shell regularly, eventually you're going delete an entire directory (or much more) due to a typo or brief loss of attention.

{% capture quote-1 %}
[A User's Guide to the Z-Shell](https://zsh.sourceforge.io/Guide/zshguide02.html#l13):

 Everybody at some time or another deletes more files than they mean to (and that’s a gross understatement); my favourite is:

```rm *>o```

That `>` should be a `.`, but I still had the shift key pressed. This removes all files, echoing the output (there isn’t any) into a file `o`. Delightfully, the empty file `o` is not removed. (Don’t try this at home.)

{% endcapture %}<div class="notice--info">{{ quote-1 | markdownify }}</div>

While zsh shell offers some protections from this type of mistake, an adventurous user will have no difficulty overcoming whatever resonable restrictions might be in place.

Make sure you have a reliable backup system in place. You're ~~probably~~ going to need it eventually.


## Break the shell

Don't be afraid to experiment.

The shell is just a program. If you "break" it, restart the shell to fix it, typically by opening a new terminal window.

For example, set the environment variable `$path`, which tells the shell where to look for programs, to nothing:

```
% path=
% ls
zsh: command not found: ls
```

That shell is wrecked. No problem. Close the window and open a new one.

Alternatively, to avoid wrecking your current shell, you can test changes in a subshell:

```
% zsh
% setopt VERBOSE
% print verbose
print verbose
verbose
% exit
exit
Saving session...completed.
% print not verbose
not verbose
```

## Print early and print often

The easiest way to decipher intimidating shell sytnax is to break it into smaller parts and examine them with `print`.

For example, we'll examine this command, which loads a directory of functions:

```zsh
autoload -Uz ${fpath[1]}/*(:t)
```

To understand how `${fpath[1]}/*(:t)` expands, break it smaller parts to print. First, let's see what's in `${fpath}`:

```
% print -l -- '${fpath} :' ${fpath}
${fpath} :
/Users/stan/functions
/usr/local/share/zsh/site-functions
/usr/share/zsh/site-functions
/usr/share/zsh/5.8/functions
```

Single quotes around `${fpath}` prevent parameter expansion. The `-l` option prints each argument on a separate line and `--` tells `print` there are no more options.
{: .notice}

Add and print each part to see how it expands:

```zsh
print -l -- '${fpath[1]} :' ${fpath[1]}
print -l -- '${fpath[1]}/ :' ${fpath[1]}/
print -l -- '${fpath[1]}/* :' ${fpath[1]}/*
print -l -- '${fpath[1]}/*(:t) :' ${fpath[1]}/*(:t)
```

(Don't worry about adding parts incorrectly. Trial and error is a great way to learn syntax.)


## Pasting Examples

My general advice is don't paste. Type (using tab or auto completions as much as possible).

You're much more likely to remember things you type yourself. Committing keystrokes to muscle memory also frees up short-term memory to focus on other things. :pencil2:

But when you just don't have the time or patience, here's a couple tips for pasting code into a zsh shell. :pencil2:

### Strip the prompt with an alias.

When pasting examples in the shell, you can use a temporary alias to strip out the prompt:

```zsh
alias %=' '
```

(The space inserted in place of the `%` tells the shell to expand any following aliases. Depending on your options, it might also exclude the line from the history file.)

I wouldn't recommend saving this alias. Let it live and die with the current shell.

### Paste into braces

Use curly brackets, also known as braces, to prevent commands from being executed immediately when pasted into the shell.

As soon as you enter a left brace, `{`, the prompt changes:

```
% {
cursh>
```

Now copy and paste multiple lines of commands, such as:

```zsh
msg=hi
print $msg
```

None of the command will be executed until you enter the closing brace:

```
 % {
cursh> msg=hi
print $msg
cursh> }
hi
```


---

[Next: How to Function (part 1)](zsh-functions-1.md){: .btn .btn--info}
