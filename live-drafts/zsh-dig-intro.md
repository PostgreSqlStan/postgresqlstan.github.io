---
title: "Dig into zsh - Introduction"
category: zsh
tags:
  - zsh
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3 # same as adding an opacity of 0.5 to a black background
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
toc: true
toc_sticky: true
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

*Dig into zsh* is a hands-on exploration of the zsh shell. The examples provided are intended as starting points for your own exploration.

{% capture requirements %}**Requirements**: a terminal running zsh and a fair amount of patience

*This isn't exactly a beginner's guide, so some prior shell experience would probably be helpful too.*
{% endcapture %}<div class="notice">{{ requirements | markdownify }}</div>

## Fear the shell

If you use a shell regularly, eventually you're going delete an entire directory (or much more) due to a typo or brief loss of attention. I've done it. Chances are you will too.

{% capture quote-1 %}
:link: [A User's Guide to the Z-Shell](https://zsh.sourceforge.io/Guide/zshguide02.html#l13) :

Everybody at some time or another deletes more files than they mean to (and that’s a gross understatement); my favourite is:

```rm *>o```

That `>` should be a `.`, but I still had the shift key pressed. This removes all files, echoing the output (there isn’t any) into a file `o`. Delightfully, the empty file `o` is not removed. (Don’t try this at home.)

{% endcapture %}<div class="notice--info">{{ quote-1 | markdownify }}</div>

By default, the zsh shell tries to protect your from this particular mistake, but an adventurous user will have no difficulty overcoming whatever protections might be in place.

If you're going to be using the shell regularly make sure you have a good backup system in place.

### Break the Shell

That said, don't be too cautious. The shell is just a program. If you break it, you can just restart it, typically by opening a new terminal window.

For example, what happens if you accidently set the environment variable `$path` to the value in `$fpath`:

```
% path=(~/bin $fpath)
% ls
zsh: command not found: ls
```

That shell is wrecked. No problem. Close the window and open a new one.

You can also launch a new shell to test changes without effecting the current shell:

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

### Preview substitutions

Expand substitutions with tab and restore the command line with undo (<kbd>control</kbd><kbd>_</kbd>).

For example, type: `print -- *`<kbd>tab</kbd>

When you press <kbd>tab</kbd>, `*` is replaced with the names of matching directories and files. Press <kbd>control</kbd><kbd>_</kbd> to restore the command line to its unexpanded state.



### Break commands into smaller parts

The easiest way to decipher confusing shell sytnax is to break it into smaller parts you can examine with `print`.

For example, take a look at this method to load a directory of functions:

```zsh
autoload -Uz ${fpath[1]}/*(:t)
```

To understand how `${fpath[1]}/*(:t)` is transformed into `fn fn2 note`, break it into smaller parts and examine them. First, let's see what's in `${fpath}`:

```
% print -l -- '${fpath} :' ${fpath}
${fpath} :
/Users/heath/functions
/usr/local/share/zsh/site-functions
/usr/share/zsh/site-functions
/usr/share/zsh/5.8/functions
```

Parameter expansion is prevented within single quotes. The `-l` option prints each argument on a separate line and `--` tells `print` there are no more options.
{: .notice}

Then start adding parts and examining the results.

```zsh
print -l -- '${fpath[1]} :' ${fpath[1]}
print -l -- '${fpath[1]}/ :' ${fpath[1]}/
print -l -- '${fpath[1]}/* :' ${fpath[1]}/*
print -l -- '${fpath[1]}/*(:t) :' ${fpath[1]}/*(:t)
```

Don't worry about adding parts incorrectly. Trial and error is a great way to learn syntax.

### Pasting examples

You're much more likely to remember commands and syntax if you type it in yourself, rather than pasting. Committing keystrokes to muscle memory will also free up your short-term memory to focus on other things. I can't recommend it enough.

But, for when you don't have the time or patience to type, here's a couple tips for pasting code examples.

#### Strip the prompt with an alias

When pasting examples in the shell, you can use a temporary alias to strip out the prompt:

```zsh
alias %=' '
```

(The space inserted in place of the `%` tells the shell to expand any following aliases. Depending on your options, it might also exclude the line from the history file.)

I wouldn't recommend saving this alias. Let it live and die with the current shell.

#### Paste into braces

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

For best use, I recommend opening a terminal window before reading any further.

Go ahead. We'll wait.

<figure style="width: 600px" class="align-center">
  <a href="/assets/images/Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg" title="Waiting, pastel on paper, 1880–1882" alt="Waiting, pastel on paper, 1880–1882">
  <img src="/assets/images/Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg" alt=""></a>
  <figcaption>"Waiting" by Edgar Degas, from  <a href="https://commons.wikimedia.org/wiki/File:Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg">Wikimedia Commons</a></figcaption>
</figure>

---
