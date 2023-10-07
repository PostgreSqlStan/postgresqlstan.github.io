---
title: "Prepend, don't append, PATH"
category: zsh
excerpt: "I've seen this mistake too often to remain silent any longer."
header:
  overlay_color: "#333"
  overlay_filter: 0.85
  teaser: /assets/teasers/prepend-path-teaser.jpg
toc: true
toc_sticky: true
toc_label: "Prepend PATH"
---

**Update**

Unsurprisingly, there is disagreement. (I was sort of hoping for that.)

<blockquote class="twitter-tweet" data-conversation="none" data-theme="dark"><p lang="en" dir="ltr">On the contrary, appending ensures that the system versions of utilities don&#39;t get shadowed. If some jerk does<br><br>$ ln -s /bin/rm ~/bin/cat<br><br>I&#39;m sunk if I<br><br>$ PATH=&quot;$HOME/bin:$PATH&quot;<br>$ cat important.txt<br><br>but if it&#39;s appended, I&#39;m safe:<br><br>$ PATH=&quot;$PATH:$HOME/bin&quot;<br>$ cat important.txt</p>&mdash; Tim Chase (@gumnos) <a href="https://twitter.com/gumnos/status/1710427088191156256?ref_src=twsrc%5Etfw">October 6, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

I'll update post when I figure out useful advice for when to prepend and when to append a shell's `PATH`. (I'm still pretty sure there's a valid case for both.)

Meanwhile, take the following advice with a huge grain of salt.

---

Add directories to the *beginning*, not end, of the shell's `PATH` variable.


:x: Don't do this:

```zsh
PATH=$PATH:~/bin
```

:heavy_check_mark: Do this:

```zsh
PATH=~/bin:$PATH
```

## Interactive demo


It's easy to demonstrate the difference between prepending and appending `PATH` interactively.

Don't worry. Messing with the `PATH` interactively doesn't have any effect on your shell's settings. It only affects the current shell.<br><br>
If you're not using zsh, substitute `echo` for the `print` commands below.
{: .notice }

First, create and navigate to an experimental `bin` directory. It can be located anywhere as long as the directory isn't already included in `PATH`:

```zsh
mkdir ~/sandbox/bin
cd ~/sandbox/bin
```

Create a command named `cat`:

```
% cat << EOF > ./cat
> #! /bin/zsh
> print meow
> EOF
% chmod +x ./cat
% ./cat
meow
```

Try  `#! /bin/sh` and `echo` instead of `print` if zsh isn't installed on your system.
{: .notice }

### Append

Add the current directory to the end of `PATH`:

```
% oldpath=$PATH  # save current PATH setting
% PATH=$PATH:$(pwd)  # add current dir to end of PATH
% cat cat
#! /bin/zsh
print meow
% PATH=$oldpath  # restore original PATH
```

The shell used the system's `cat` command, not the one in the current directory.

### Prepend

Now, try adding the directory to the beginning of `PATH`:

```
% PATH=$(pwd):$PATH  # add this dir to beginning of PATH
% cat cat
meow
```

The `cat` command from the current directory was used, overriding the builtin command.


## Don't append

On macOS the default `PATH` begins with `/usr/local/bin`, allowing commands installed there to override builtin commands.

Commands added to `PATH` should always override system commands, not the other way around.

Likewise, that's how user directories should be added to `PATH`, at the beginning.

